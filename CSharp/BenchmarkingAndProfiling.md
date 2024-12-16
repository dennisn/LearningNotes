# C# Benchmarking and Profiling

Definition: 
  - Profiling: using tools to identify "hot paths" (e.g. area of codes that run a lot)
  - Benchmark: Compare run statistics against some default values
==> Can be done with available tools. May want to put it in its own project, and treated it as with unittest
Also need unittest to ensure refactored codes are still correct

## Benchmarking
  - Can use "**BenchmarkDotNet**", which is open-source and use by .Net team themselves
  - Each benchmark class is like a unittest, but we need to have the local knowledge of the class under test to determine to benchmark which part, what statistics (e.g. memory, time, threads, etc.), etc.
    - To benchmark specific statistics, use corresponding "diagnoser" decorator
  - Useful to know which approach is "really" better (in usage of CPU, memory, etc.)
  
## Profiling
NOTE: Visual Studio profiling tools: Need to take snapshot

  - Want to do it with "Release" build, since "Debug" vs. "Release" may have slightly different performance profile
  - Improve performance may reduce code cleanliness
  - Visual Studio ".Net Async" profiling may help with spotting dead-locks: identify processes that can't seems to run to completion
  - also good at spotting memory leak
  
## Optimise memory & CPU usage
  - When to use heap (reference types) vs. stack (value types such as struct, tuple, etc)
    - Allocations & De-allocations of value types are much cheaper than of reference types
    - Use struct for instances that are **small**, often **short-lived or embedded** in other object
  - Reference types are generally better at copying & boxing/unboxing (e.g. casting into object/interfaces, stored in list, etc.)
  - Avoid closure allocation, since it will package the "enclosed" environment, which may do unnecessary boxing/unboxing
    - Closure created when lambda action reference external variable
      ```
	  var i = 100;
	  var result = Calculate(() => i * 100);
	  ...
	  int Calculate(Func<int> action) => action() + 100;
	  ```
	- Pass external variable as params to lambda action
	  ```
	  var i = 100;
	  var result = Calculate(i, (value) => value * 100);
	  ...
	  int Calculate(int value, Func<int> action) => action(value) + 100;	// 
	  ```
  - Using Span<T> and Memory<T>: similar usage as pointer, allowing better slicing of data without allocate memory for copy
	```
	Span<char> = "test string".AsSpan();
	```
  - Catching exception: performance hits since it needs to capture stacktrace, context, etc.
  
## Effective caching strategies
  - Caching:
    - Purpose: to avoid recomputation, repeated IO, etc.
	- Expiry: fixed time, sliding windows or manual expiry --> may impact on accuracy, memory footprint
	- Caching framework: more features
  - IMemoryCache vs. IDistributedCache vs. Out-of-process
    - Distribute cache: more suitable for scalability
	- Out-of-process: caching "across" processes
  - Lazy loading: also improve performance as avoid unnecessary but expensive initialization
    ```
	Lazy<T> property = new Lazy<T>(<initialization_method_or_lambda>)
	```
