# Python 3 Performance

## 1. Measuring Performance
Performance is often caused by bottlenects: CPU, Memory --> Cache missed, Disk --> read/write IO
  + CPU
  + Memory: cache misses --> disk read/writh
  + Disk: unnecessary read/write
  + Network: slowest

### 1.A. Strategy to improve performance
Basic components are: Prevent, Fix and Measure
  - Prevention: from the architecture & design, including library selection
  - Fix: locate the bottlenecks, aware of the trade-offs in readability, memory and efforts
  - Measurement: important to correctly identify root causes, and evaluate the effects of the final fixes

### 1.B. Basic ways to measure performance
Python code to measure performance
  + time() function: most basic
  + `timeit` module
  + `pytest-benchmark` pluging for pytest: integrate into unittest to detect early performance problems. Sample usage as follows
    ```
    def test_benchmark_heavy_work(benchmark):
      benchmark(heavy_work)
    ```

### 1.C. Why Profile
  - For finding bottlenecks via detailed measuring of execution times
  - Two main types: 
    + Event-based: gather all data on events --> lot of data, high accuracy but high overhead (slow the program down)
    + Statistical: sampling the program --> less data, approximation only, but less overhead
  - Python modules for "integrated" profile: `profile` and `cProfile` (both are event-based)
    + To run: `python -m profile <program-name>`
    + significant slow the performance, can't handle multi-threading
    + No visibility inside functions, or memory consumption
  - 3rd party profilers: 
    + line_profiler: visibility inside
    + Py-spy: sampling, multithread
    + Scalene: sampling, line level, memory & threading
    + Yappi: event-based, fast
    + Memory_profiler: focus on memory usage at function level
  - line_profiler usage:
    + decorate the function to be profiled with `@profile`
    + run `kernprof -lv <program-name>`
  - memory_profiler usage:
    + decorate the function to be profiled with `@profile`
    + run `python -m memory_profiler <program-name>`

### 1.D. Visualizing profiling data
  - Profile data is large & complext --> need friendlier way to visualize data
  - memory_profiler: visualised using matplotlib
    + run `mprof run <program-name>`
    + run `mprof plot --output <output-graphic-filename>`
  - Using snakeviz to visualize cProfile
    + Generate cProfile data: `python -m cProfile -o file.prof <program-name>`
    + Visualize the data: `snakeviz file.prof`

## Using the Right Data Structure
  - Which data structure is faster ?
  - O notation: performance scalability
    + O(n2) > O(nlogn) > O(n) > O(logn) > O(1)
  - Lists vs Arrays
    + List: order collection of item (can be mixed-types, but optimised for same type)
      - Fast: Get, Set, Append
      - Slow: Search, Delete
      - Memory allocation: triggered when list is full --> best to create array up-front with known size
    + Arrays: specific module (e.g. "numpy")
      - numpy: significant faster than list
  - Sets vs Tuples
    + Sets: un-ordered collection of unique item. Item must be **immutable**
      - Fast: Add, Delete, Search
      - Slow: remove duplicate (???)
    + Tuples: like an **immutable** list --> light weight and faster
      - Size: smallest vs. List & Sets
      - Search: faster than list, but still much slower vs Sets
  - Queue vs Deques
    + `queue` module: simple FIFO queue, specialized for multi-threads
      - `queue` class: put & get
    + `collections` module: double-ended queue for FIFO & LIFO, support multi-threads
      - `deque` class: appendleft/append, pop/popleft
      - slow access by index, but fast append & pop at either end
      - Contrast to list: `pop(0)` is very slow
  - Dictionaries: mutable colllection of key-value pairs. Key must be hashtable & unique
    + Fast: Get, Set, Search
  - Data class vs. Dictionary vs. NamedTuple
    + Named tuples: tuples with named field --> better readability
      - More memory efficiency vs. data class, but with less features
    + Data class: to store data with a class, without boilerplate --> support type hint
      - Declare as class, but decorate with `@dataclass`
      - By default is mutable, but can turn into immutable

## Optimizing Python Code
Ways to optimise python code
  - Caching: re-use results from expensive computation, or IO
    + Trade off: extra memory/storage
    + Limitation: results can be out-date
    + Approaches: basic dict (may overgrown), use `@lru_cache()` from functools, or 3rd party lib (e.g. "**joblib**")
  - For vs. list comprehension
    + `for` loop: better for complex logic, but longer and slower
    + list comprehension: only for creating new list with simple logic, but concise & faster
  - Generator expressions: lazy version of comprehension
    + Syntax: use `()` instead of `[]`
    + very low memory, but just-in-time and access only next item --> good for streaming
    + NOTE: operation within generator will only, and always, re-computed when items are accessed
  - Concat strings have 3 ways: `+`, using f-string, or `join()`
    + Using `+`: basic and scalable, but slow
    + Using f-string: `f'{items[0] items[1]}'`: high performance but not scalable
    + Using `join()`: scalable and fast
  - Permission or forgiveness (e.g. check vs. catching exception)
    + Permission is slower when violations are low
    + Check: best to avoid often encountered problem
    + Exception: for errors happen rarely
  - Functions: trade off performance vs. readability
    + the performance penalty is very small though
  - Numerical calculations: faster with specialized modules suchas "**NumPy**" or "**Pandas**"
  - Using different interpreters: CPython (reference implementation), PyPy, Cython, Jython
    + Different interpreters: may not be as compatibility
    + PyPy: highly compatible with speed boost, but lag behind a bit
  - Optimisation Risks:
    - Tradeoffs must be awared & think about
    - Code is less readable
    - May introduce ew bugs
    - Small gains for lot of efforts

## Using More Threads
  - Threads: 
    + GIL: global interpreter lock --> one thread run at anytime
    + Lighweight, shared memory --> high potential for bugs
  - Processes:
    + No GIL lock --> more potential performance gain
    + Heavyweight, separate memory --> low potential for bugs
  - Module `threading`:
    + Usage: subclass `threading.Thread` and overwrite `run()`; or pass a function as "target" to `threading.Thread()` constructor
  - Challenges of working with threads: synchronizing data, debug, GIL
    + GIL: good for synchronizing data, but restrict performance gain for CPU-intensive tasks (i.e. unable to utilise multi-processors)
  - When to use multi-threading
    + Usage: tasks that wait for external events, blocking I/O with simple logic
    + Avoid: CPU-intensive tasks, or complex logic

## Using Asynchronous Code
  - Asynchronous code: use the new module `asyncio`
    + Vs. Threads: lower overhead & potential for bugs, but high learning curve and may not be supported by many library
    + Use similar syntax as C#
  - Learning curve:
    + New syntax: `async` and `await`
    + New concepts: coroutine & event loop
    + Need to use new library: `aiohttp`, `aiomysql`, etc.
  - Debug --> harder to know the order of execution & state of the application
  - New libraries needed: need to avoid blocking code & libraries with blocking code !
  - When to use 'asyncio': for IO operations, small tasks
    + also need to aware the library to avoid blocking code in 3rd parties library

## Using More Processes
  - Process-based parallelism: using module `multiprocessing`
    + separate memory spaces, no GIL --> utilize all CPU cores
    + Increased memory overhead, and harder to share resources
  - Using class `multiprocessing.Process`
    + Similar to `threading.Thread`: subclass `Process`, then override `run(self)`, or create Process with target=<processing-procedure>
  - Inter-processes communication: can use `Pipe` or `Queue` in "multiprocessing" module
  - When to use multiple processes: in data pipeline, producer-consumer apps or for parallelizable workloads
    + Use logging --> ease of debug for hard to reproduce state
    + Monitor CPU usage, ensure process terminate cleanly
    + Take care of accessing shared resources
    + Limit/control the number of processes: "#processes >> #CPU" --> reduce performance
  - Scaling to multiple machines
    + `celery` libary: using task queue to distribute data processing to multiple machine
    + `dask`: integrates with NumPy, Pandas --> scaling from laptop to cluster on the cloud
    + `ray`: similar to `dask`, but designed to be general purpose
    + Kubernetes: general purpose cloud managed service

# Clean Code Practices

## Exception
  - Determine modules's responsibility --> decide whether to catch an exception
  - Logging: always good, then the exception can re-raise with `raise`
  - Custom exception: to simplify exception handling in client, and hide exception details
    + Better include the source exception when re-raise: `raise XXXException from e`
    + To hide the original exception: `raise XXXException from None`