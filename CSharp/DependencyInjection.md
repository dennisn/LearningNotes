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
  - `Add` will overwrite the registered service
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

### Options pattern (IOptions)
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

### Multiple registration using "Add"
  - If resolve 1 object only --> will get the last registered object
  - If resolving IEnumerable --> will get all registered objects