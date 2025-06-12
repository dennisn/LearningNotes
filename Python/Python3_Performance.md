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

## Optimizing Python Code

## Using More Threads

## Using Asynchronous Code

## Using More Processes