# Asp.Net Web Api
  - Two approaches: Core MVC (Model-View-Controller) vs. Core Minimal

## Core MVC 


### 1. Overview

Based on "**ASP.NET Core Web API Fundamentals**" by Kevin Dockx on PluralSight.com

### 2. Basic

#### launchSettings.json
  - Can open via Project -> Properties -> Debug -> "Open debug launch profiles UI" link
  - Three profiles: http, https and IIS. The first two use Kestrel web server
#### Basic structure
  - `WebApplication.CreateBuilder(args)`: create a builder to build a web application host
    - builder can be used to register & add configured services
  - WebApplication was built from the builder
    - IApplication allows middlewares to be added into the request pipeline processing (e.g. which service will run when during the request processing)
  - Through WebApplication, we can access the IWebHostEnvironment/IHostEnvironment, which contains information about the current environment and application

### 3. API and returning resources
  - Controller class: decorated with `[ApiController]`, extends `ControllerBase`
  - **Routing**: matches a request URI to an action on a controller
    - Prefer method is "endpoint routing": calling `app.UseRouting` and `app.UseEndpoints`
    - UseRouting: mark the position in pipeline where routing decision is made (e.g. where an endpoint is selected)
    - UseEndpoints: mark the position in pipeline where the routing executed (e.g. the selected endpoint is executed)
    - Between the two, we can inject other middlewares to transform the request, or even change the selected endpoint (e.g. authorization)
    - latest version call `app.MapControllers()` directly without UseRouting & UseEndPoints, as UseRouting is implied
  - Attribute routing at controller class & method level: with URI template, map request to controller actions
    - Attribute: HttpGet, HttpPost, HttpPut, HttpPatch, HttpDelete, Route
    - `[Route]` doesn't map to an HTTP method, but use at class level to provide a template that will prefix all templates defined within that class
  - Often, the outer facing model (DTO) is different from the entity model (e.g. maps to datastore)
    - DTO may have calculated fields
    - Entity may have identifiers, DTO may not need them
  - Status code: important for consumer of the API
    - Dont send 200 (OK) when something's wrong
    - Dont send 500 (Internal Server Error) when it's a client fault
    - User helper method: OK(), NotFound(), etc.
  - Child resources: should prefix with parent resource ID (e.g. `/cities/{cityId}/pointsOfInterest`, `/cities/{cityId}/pointsOfInterest/{pointOfInterestid}`)
  - Error: `AddProblemDetails()` to services so it can return a standard ProblemDetails object
    - Can use option to customized the problem details object, adding additional info (e.g. machine name, environment variable, binary version, etc.)
  - Content negotiation: support format for input/output data exchange (e.g. request header "Content-Type" and "Accept")
    - have to turned on `options.ReturnHttpNotAcceptable = true` in AddControllers()
    - These are automatically handled by ObjectResult, but need matching formatter (e.g. `AddControllers().AddXmlDataContractSerializerFormatters()`)
    - When working with file, can use "**FileExtensionContentTypeProvider**" to automatically work out the content type
      ```
      // Add service as singleton, then injected into required controller to use
      builder.Services.AddSingleton<FileExtensionContentTypeProvider>();
      ```
### 4. Manipulate resources and validate inputs
  - Data can be passed to the API in various way:
    - `[FromBody]`: request body 
    - `[FromForm]`: form data in request body
    - `[FromHeader]`: request header
    - `[FromQuery]`: query string parameter
    - `[FromRoute]`: route data from the current request
    - `[FromServices]`: service(s) injected as action parameter
    - `[AsParameters]`: method parameters
  - On creation success, we can use `CreatedAtRoute()` helper function to signify success as well as returned where the new object can be found
    - Will need the "name" of the action that can retrieve the just-created resource. The name here can be "decorated" to the action
    - Also need additional routeValues to fill out the route URI
    - Finally it need the created object DTO
  - Similar function such as `CreatedAtAction()`
  - If we annotated the DTO class with "System.ComponentModel.DataAnnotations.XXX" attributes, some basic validation can be performed automatically (e.g. MaxLength)
    - Invalid error message can be access from `ModelState` object
  - Update: Full update use "PUT", while "PATCH" for partial update
    - Full update: supplied all fields
    - Partial update: array of operations, each 
      ```
      { 
        "op": "replace", 
        "path": "/<field_name>", 
        "value": "<field_value>" 
      }
      ```
  - "DELETE" for deleting resources

### 5. Services and Dependency Injection
  - **Inversion of Control (IoC)**: delegates the selection of a concrete implementation for a class dependencies to an external component
  - **Dependency Injection (DI)**: a specific "Inversion of Control" pattern where the container will initialize objects & provide the required dependencies to the intialized objects
    - Classes "declare" their dependencies as parameters of constructor.
    - Constructor's parameter(s) should be service interface(s), not concrete implementation
    - Service(s) are registered on the container, which manages the service life-cycle, and providing instances of the services to initialized objects as needed
  - Benefits of IoC/DI: dependencies can be easily replaced.
    - Class(es) are easier to test in isolation, as dependencies can be mocked
  - Three basic life-cycles for registered services:
    - `Transient`: always different instance for each and every controllers & services 
    - `Scoped`: same instance within a request, but different across different requests
    - `Singleton`: the same for every object and every request

### 6. Introduction to Entity Framework Core
  - **Object-Relational Mapping (ORM)**: a technique that lets you query and manipulate data from a database as if via Object (OO paradigm)
  - **SQL injection**: malicious code is inserted into strings that are later passed to a database instance for parsing & execution, indirectly executed the code
  - `DbContext`: The basic service in EF core, allowing accessing to database via entities
    - MS has different Nuget packages to cater for different database types
    - Old way: use `OnConfiguring(DbContextOptionsBuilder optionsBuilder)` to configure the DbContextOptionsBuilder
    - Simpler way: pass the "DbContextOptions" object to constructor, which can pass to the base class `DbContext`
      ```
       public CityInfoContext(DbContextOptions<CityInfoContext> options)
            : base(options)
       {
       }
      ```
  - "**Microsoft.EntityFrameworkCore.Tools**": enable many migration tools for seeding the database, as well as update the db schema overtime
    - run command `add-migration <ProjectName>` to add the initial migration
    - Each migration will have two methods: for moving up the version and moving down theversion
  - `OnModelCreating(ModelBuilder modelBuilder)`: allows us access to model builder
    - Can be used to manually construct the model if basic convention is not enough
    - Can also used to deeding the data
      - Initialise sample data for each enity
        ```
        modelBuilder.Entity<City>()
           .HasData(
          new City("New York City")
          {
            Id = 1,
            Description = "The one with that big park."
          },
          new City("Antwerp")
          {
            Id = 2,
            Description = "The one with the cathedral that was never really finished."
          });
        ```
      - can use "add-migration" to convert this seeding into a migration script

### 7. Using Entity Framework Core in controllers
  - **Repository pattern**: create an abstraction layer for business logics that operate on the model. 
    - Separate to the logic that retrieve data and/or map it to entity, which can be tightly couple to a specific database technology
    - Allow flexibility in replacing database technology
  - Purpose of async: free up threads from long wait operations (e.g. IO, network calls), which could improve the scalability of application
    - NOTE: async doesn't make a specific processing time shorter, but allow running of more processing during long "wait" time
  - AutoMapper: a convention-based object-to-object mapper
    - reduce mapping codes, which can be error prones, especially during refactoring
    - Great for mapping between DTOs and their entity object
    - To use, call `AddAutoMapper()` on the services collection to register it
      - Need subclass of `AutoMapper.Profile`, where `CreateMap<[EntityClass], [DTOClass]>()` is called to create the mapping profiles
      - To use, inject IMapper object, then call `IMapper.Map<[TargetClass]>([SourceObject(s)])`
    
### 8. Searching, Filtering and Paging resources
  - Search vs. Filtering: exact match vs. partial match
  - Paging: best practice for returning collection, to avoid return too large collections that impact on performance
    - Paging need 2 params: page number & page size (i.e. how many item per page)
      - Page size need an upper limit so it won't be too large and cause performance issue
      - If missing, can use a default page number=1 and reasonable page size
    - LINQ with defer execution can be used with paging to load the exact part of the collection
      - Use `Order()/OrderBy()`, `Skip(<NoItem>)` and `Take(<NoItems>)` to return the items within the pages
    - Paging metadata (e.g. Page size, current page, total pages, total items, etc.) also need to be returned
      - can be returned via custom header like `X-Pagination`, instead of as part of the result

### 9. Securing your API
  - We need to know who (e.g. which entity) access the API, can they access it, etc.
  - Username/password for each API call: too much data for hacking ==> better approach is token-based

#### Token-based security:
  - API login end-point accepting a username/password, return an access token-based
    - Token would have user info, created/expiration date
    - Also some generic info: hash of payload to prevent tampering, algo. for signing, etc.
  - Rest of API can only access with valid token-based
    - Token can be passed as Bearer token on each request
      ```
      Authorization: Bearer mytoken123
      ```
  - Token can be created as JwtSecurityToken, which need 
    - Issuer: who created the token
    - Audience: who is this token for
    - A list of claims: a list of key/value pair of information about the client, as known/authorised by issuer
    - start/end time: period when this token is valid
    - A signing credential: the digital signature (e.g. a secret key) of the issuer, which can be used to validate this token
    - *NOTE*: can use jwt.io to parsed the token to view the content of its payload
  - To configure this: 
    ```
    IServiceCollection.AddAuthentication("Bearer")
      .AddJwtBearer(options =>
        {
          options.TokenValidationParameters = new() 
          {
            // turned on various validation option
            // specify valid values
            // and the security key
          }
        });
    ```
  - For customised token authentication, we can add our own authentication handler (subclass of `AuthenticationHandler`) via 
    ```
    AuthenticationBuilder AddScheme<AuthenticationSchemeOptions, AuthenticationHandler{TOptions}>(...)
    ```
  - From the API: we can retrieve the claim from user and act on it without extra processing
    - One way to use this is via security policy (e.g. AuthorizedPolicyBuilder)
      ```
      Services.AddAuthorization(options =>
      {
        options.AddPolicy("MustBeFromAntwerp", policy =>
        {
            policy.RequireAuthenticatedUser();
            policy.RequireClaim("city", "Antwerp");
        });
      });
      ```
    - Then in controller, just add the policy into "Authorize" annotation: `[Authorize(Policy = "MustBeFromAntwerp")]`
  - Tools in dotnet to generate token: `dotnet user-jwts`
  - OAuth2 and OpenId: open standard for security
    - Next course: "Securing Asp.Net Core with OAuth2 and OpenID Connect"

### 10. Versioning and Documenting your API

#### Versioning overview
  - Versioning: As API evolve, multiple version start to co-exist ==> Different versioning strategies
    - In URI: `https://root/api/v2/XXX`
    - In query parameter: `https://root/api/XXX?version=v1`
    - Custom header: `X-version: "v2"`
    - Accept header: `Accept: "application/json;version=v1"`
    - Media types: `Accept: "application/vnd.book.v1+json"`
  - Popular way is via "**Asp.Versioning.Mvc**"
    ```
    Services.AddApiVersioning(setupAction =>
      {
        setupAction.ReportApiVersions = true;  
        setupAction.AssumeDefaultVersionWhenUnspecified  = true;
        setupAction.DefaultApiVersion = new ApiVersion(1, 0); 
      })
    .AddMvc()
    ```

#### Version through query (default)
  - default version will be returned in header field: `api-supported-versions`
    - If it's deprecated, the header field will be: `api-deprecated-versions` instead
  - In controller, can decorated with multiple of `[ApiVersion(<version_number>)]` to the controller class, or its methods
  - By default, we can request a specific version via query string "api-version" (e.g. `https://root/api/XXX?api-version=2`)

#### Versioned route
  - More popular than query
  - Just need to alter the "Route" decorator: `Route["api/v{version:apiVersion}/XXX"]`

#### Documenting API with OpenAPI
  - "**OpenAPI**" is a specification language for HTTP APIs, describes the capabilities of your API, and how to interact with it
  - It's usable for any languages/tech. stack; often written in YAML or JSON
  - "**Swagger**" is a set of open-source tools for OpenAPI specification
    - "OpenApi specification" and "Swagger specification" are the same
  - "**Swashbuckle.AspNetCore**" is the Asp.Net package helps with working with OpenAPI
    - generate OpenAPI specification from Api
    - Wraps swagger-UI and provides an embedded version of it
    
#### Swagger documentation
  - ApiExplorer is needed to exposes information on our API, like the available endpoints and how to interact with them
    ```
    Services.AddEndpointsApiExplorer();
    ```
  - Swagger service is registered for generating the specification
    ```
    Services.AddSwaggerGen()
    ```
  - Two middlewares from swagger are needed:
    ```
    UserSwagger();    // generate the OpenAPI
    UserSwaggerUI();  // generate the default Swagger UI on top of the OpenAPI specs above
    ```
  - To generate documentation on exchanged model "**T**" in the API, using `ActionResult<T>`
    - Annotations on the model properties will also be added to OpenAPI
  - For OpenAPI to include XML documentation in class, we need to generate it into an XML file, then reference it in Swagger setupAction
    - Generate to file: in project --> Properties --> Build --> Output: tick "Generate a file container API documentation"
    - Also specify the XML documentation file path (*may use "<project_name>.xml"*)
    - In `AddSwaggerGen()`, we can specify the property "IncludeXmlComments" to point to the XML file above
      ```
      Services.AddSwaggerGen(setupAction => 
      {
        var xmlCommentsFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlCommentsFullPath = Path.Combine(AppContext.BaseDirectory, xmlCommentsFile);
        setupAction.IncludeXmlComments(xmlCommentsFullPath);
      }
      ```
  - To let client know of different possible responses from API, we use multiple `[ProducesResponseType]`
    ```
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    [ProducesResponseType(StatusCodes.Status200OK)]
    ```

#### Versioning the Swagger documentation
  - Use package "**Asp.Versioning.Mvc.ApiExplorer**"
  - First, need to substitute API version in URL
    ```
    Services.AddApiVersioning().AddMvc().AddApiExplorer(setupAction =>
    {
      setupAction.SubstituteApiVersionInUrl = true;
    });
    ```
  - Get the list of versions to define a swagger documents for each version
    ```
    var apiVersionDescriptionProvider = builder.Services.BuildServiceProvider().GetRequiredService<IApiVersionDescriptionProvider>();
    
    Services.AddSwaggerGen(setupAction =>
    {
      foreach (var description in apiVersionDescriptionProvider.ApiVersionDescriptions)
      {
        setupAction.SwaggerDoc(
          $"{description.GroupName}",
          new()
          {
            Title = "City Info API",
            Version = description.ApiVersion.ToString(),
            Description = "Through this API you can access cities and their points of interest."
          });
      }
    ...
    });
    ```
  - When generate Swagger UI, generate Swagger endpoint for each version
    ```
    app.UseSwaggerUI(setupAction =>
    {
      var descriptions = app.DescribeApiVersions();
      foreach (var description in descriptions)
      {
        setupAction.SwaggerEndpoint(
          $"/swagger/{description.GroupName}/swagger.json",
          description.GroupName.ToUpperInvariant());
      }
    });
    ```
    
#### Swagger documentation for API Authentication
  - Out-of-the-box support for 4 authentication scheme:
    - `http`: normal Http authentiation schemes (bearer, basic, etc)
    - `apiKey`: API Key
    - `oauth2`: OAuth2`
    - `openIdConnect`: OpenId Connect
  - Can use `AddSecurityDefinition()` in `AddSwaggerGen()` to define an authentication in Swagger UI
    ```
    Services.AddSwaggerGen(setupAction =>
    {
    ...
      setupAction.AddSecurityDefinition("CityInfoApiBearerAuth", new()
      {
        Type = SecuritySchemeType.Http,
        Scheme = "Bearer",
        Description = "Input a valid token to access this API"
      });
    ...
    });
    ```
  - For Swagger UI to use the above authentication, we need to `AddSecurityRequirement()`, which reference the above definition(s)
    ```
    Services.AddSwaggerGen(setupAction =>
    {
    ...
      setupAction.AddSecurityRequirement(new ()
      {
        {
          new ()
          {
            Reference = new OpenApiReference {
            Type = ReferenceType.SecurityScheme,
            Id = "CityInfoApiBearerAuth" }
          }, 
          new List<string>() 
        }
      });
    ...
    });
    ```


### 11. Testing and Deploying your API

## Core Minimal