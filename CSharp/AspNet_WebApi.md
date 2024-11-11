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
  

## Core Minimal