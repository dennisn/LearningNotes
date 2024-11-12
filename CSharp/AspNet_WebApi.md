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
    - These are automatically handle by ObjectResult, but need matching formatter (e.g. AddControllers().AddXmlDataContractSerializerFormatters())
	- When working with file, can use "**FileExtensionContentTypeProvider**" to automatically work out the content type
	  ```
	  // Add service as singleton, then injected into required controller to use
	  builder.Services.AddSingleton<FileExtensionContentTypeProvider>();
	  ```
### 4. Manipulate resources and validate inputs

## Core Minimal