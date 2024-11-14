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

### 8. Searching, Filtering and Paging resources

### 9. Securing your API

### 10. Versioning and Documenting your API

### 11. Testing and Deploying your API

## Core Minimal