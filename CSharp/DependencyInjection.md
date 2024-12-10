# Dependency Injection
  - Summarise from "C# Dependency Injection" by Henry Been
  
## Principle
  - **Inversion of Control**: design principle that gives control of a program's flow to an external source (e.g. user action with call back, framework with dependency injection)
    - Sometimes refer to as "Hollywood Principle" (e.g. Don't call us, we'll call you): The system will call the dependent objects when it needs them
    - In Java/Spring framework, this can mean given the framework control over the implementations of dependencies being used
  - **Dependency Injection**: allows an object or functions to receive objects or functions it needs from external source (e.g. framework), instead of creating them internally
    - A specific type of IoC
	- Benefit: loose coupling --> improve flexibility (in testing and prod), modular the development
	
## Microsoft "Host" container
  - Need 2 Nuget packages:
    ```
    Microsoft.Extenions.DependencyInjection.Abstractions
	Microsoft.Extenions.Hosting
	```
  - Two main phases: Register the service, then resolve the service (i.e. build the host)
    ```
	Host.CreateDefaultBuilder()
	.ConfigureServices((context, services) =>
	{
		// register the service
	})
	.Build();  // resolve the service
	```
## Lifetime Management
  - Three basic lifetimes: `Transient`, `Singleton`, `Scoped`
    - Transient: new one created every time it is required
	- Singleton: Created once and reused
	- Scoped: one instance per scope (e.g subcontainer), where the DI container is the root scope (so will have different instance to other sub-scope
  - Dependency captivity: Longer-lived service hold a shorter-lived service ==> the shorter-lived service won't be "refreshed", hence elevated to the longer-lifetime
  
## Composition Root
  - A Composition Root is a (preferably) unique location in an application where modules are composed together.
    - Only applications should have Composition Roots. Libraries and frameworks shouldn't.
  - A DI Container should only be referenced from the Composition Root. All other modules should have no reference to the container.
  
### IServiceCollection: Add vs TryAdd
  - Most relevant with composition root
  - `IServiceCollection.Add` will register multiple objects: effect similar to overwrite
    - If resolve 1 object only --> will get the last registered object
    - If resolving IEnumerable --> will get all registered objects
  - `TryAdd` would only register the service if it wasn't before

### Grouping service registration
  - Normally, registration of related services are grouped together in static extension method, resided within the project containing the services
  - Composition root will call the extension method to registered all logical-related services
  - Can have extra `Action<XXXOption>` to modify the service registration behavior
  
For examples:
The extension method will be
```
public static IServiceCollection AddXXXService(this IServiceCollection services, 
	Action<XXXOption> optionsModifier) 
{
	var options = new XXXOption();
	optionsModifier(options);
	...
	if (options.EnableYYY)
	{
		// register the service
	}
	else 
	{
		// register a "NULL" service (e.g. one that does nothing)
	}
	...
}
```
The composition root will be
```
service.AddXXXService(o => {
	o.EnableYYY = true;
});
//or shorthand: service.AddXXXService(o => o.EnableYYY = true;);
```

## Options pattern (IOptions)
  - Ref: https://learn.microsoft.com/en-us/dotnet/core/extensions/options
  - Need 2 packages: `Microsoft.Extenions.Options` and `Microsoft.Extenions.Configuration.Binder`
    - The first is to define injected option parameter `IOptions<XXXOptions> options` ==> can access as `options.Value.XXXField`
	- The second is to bind the section
	  ```
	  // In the applications.json
	  {
		  "XXXOptions": {
		      "XXXField": "Value",
		  }
	  }
	  
	  // In the DI configuration
	  var options = new XXXOptions();
	  builder.Configuration.GetSection(nameof(XXXOptions)).Bind(options);
	  ```
	- Alternatively, use DI services
      ```
	  builder.Services
		.AddOptions<XXXOptions>()
		.Configure<IConfiguration>((options, configuration) =>
		{
			configuration.GetSection(nameof(XXXOptions)).Bind(options);
		});
      ```
	  
## Using HttpClient
(Reference: https://www.milanjovanovic.tech/blog/the-right-way-to-use-httpclient-in-dotnet)

`HttpClient` instances are meant to be "**long-lived**", and be re-used throughout the lifetime of the application

  1. The naive way: create new instance of `HttpClient` when needed
    - port exhaustion when new connections are constantly creating
  2. Using `IHttpClientFactory` to create HttpClient instance
    - The factory will cache & reuse the `HttpMessageHandler`, which has an internal Http connection pool that can be reuse
    - The `HttpClient` instances are short-lived
    - Still need configuration everytime
  3. Using "named client": via `AddHttpClient` on service configuration, then `IHttpClientFactory.CreateClient` with the specify name
    ```
	// in service registration
	services.AddHttpClient("<Client_Name>")
		.ConfigureHttpClient((serviceProvider, client) =>
		{
			var settings = serviceProvider.GetRequiredService<IOptions<XXXSettings>>().Value;
			client.BaseAddress = new Uri(settings.BaseAddress);
		});
	...
	
	// in usage with injected "IHttpClientFactory"
	using var client = _factory.CreateClient("<Client_Name>");
	```
	- Cons: magic client name, but maybe useful to share it across multiple users
  4. Typed client 
    - Via extension method `AddHttpClient<TClient>` or `AddHttpClient<TClient, TImplementation>`, then injected into user class
	```
	// in service registration
	services.AddHttpClient<IUserService, UserService>()
		.ConfigureHttpClient((serviceProvider, client) =>
		{
			var settings = serviceProvider.GetRequiredService<IOptions<XXXSettings>>().Value;
			client.BaseAddress = new Uri(settings.BaseAddress);
		});
	...
	// in usage service
	UserService(HttpClient client) 
	{
		_client = client;
	}
	```
	- Under the hood, it's still "named client", where the name is the type name. 
	- The instance is transient, so shouldn't be injected into singleton service to avoid caching problem
  5. Type client - Singleton
    - Could still inject "named client", but need to configured `PooledConnectionLifetime` to allow refreshed of DNS
	```
	// in service registration
	services.AddHttpClient<IUserService, UserService>()
		.ConfigureHttpClient((serviceProvider, client) =>
		{
			var settings = serviceProvider.GetRequiredService<IOptions<XXXSettings>>().Value;
			client.BaseAddress = new Uri(settings.BaseAddress);
		})
		.ConfigurePrimaryHttpMessageHandler(() =>
		{
			return new SocketsHttpHandler()
			{
				PooledConnectionLifetime = TimeSpan.FromMinutes(15)
			};
		})
		.SetHandlerLifetime(Timeout.InfiniteTimeSpan);
	```	
## Service Locator vs. Dependency Injection
  - Service-Locator: use a locator service to retrieve the required service (Often using the DI container as the service locator)
  - Dependency-Injection: Advertise its dependency and let the DI container "injected" the required object(s)
  
==> Service-Locator: dependencies are less transparent (i.e. constructor params doesn't show real dependencies), 
harder to test since we have to mock the locator