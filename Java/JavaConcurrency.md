# Java Concurrency
  
## Concurrency and Threads
  - Executing multiple paths of code at the same time
    + Multi-Processing: multiple CPUs
    + Multi-Tasking: one CPU alternating between tasks
    - Multi-Threading: different parts of the same program running on different threads
  - Concurrency: Pros: improve performance & Cons: increase complexity & add overhead cost
  - Runnable vs. Callable:
    + Runnable: run a function without any return value --> used with Thread
    + Callable: run a function then return an object, can throw exception --> used with ExecutorService
  - Thread can go to sleep
    + often not a good sign of using thread, but used to slow down the background Thread
    + Thread can be interrupted by calling `interupt()` method on the thread, which will set the flag `interrupted`
    + NOTE: interupt a sleeping thread will throw an `InterruptedException`
  - `join()`: wait for a thread to complete
    + can wait infinitely or with some time limit

## Synchronized and Locks
  - Thread interference: multiple threads access (read/write) same variables --> result is underterministics
  - `synchronized` keywords: only one thread is allowed at a time
    + this can be applied to object or methods (NOTE: Each instance method is treated as separated "object")
    + synchronized keywords: control access to a specific pieces of code --> finer control in some cases
    + Limitation: threads are stop waiting forever, but there is no way to check if the lock is available before waiting
  - `Lock` interface (for example: `ReentrantLock`)
    + lock()/unlock(): similar effect as "synchronized"
    + Also has tryLock()/tryLock(time, timeUnit): attempt to get the lock, but wait only the specified time before return
    
## ExecutorService and Thread pools
  - ExecutorService: interface to manage and execute task async by queuing them into a threads pool
    + Basic workflow: Get an instance of ExecutorService, run task with it, then shutdown the ExecutorService
    + Simple run task with `invokeXXX()` --> return value directly
    + Using `submit()` to return a Future object
  - ScheduledExecutorService: run by scheduled, similar to ExecutorService
    + Invoke with `schedule()`, `scheduledAtFixedRate()` and `scheduledAtFixedDelay()`
  - Future: handle to the value, which may/may not available yet
    + Basic state methods: `isDone()`, `isCancelled()`, `cancel()` (NOTE: cancelled also mean done)
    + Retrieve the value: `get()` vs `get(long time, TimeUnit unit)`
  - ThreadPool: collection of threads ready to run tasks
    + pick up tasks from a queue
    + when done, wait for new task
    + Some basic types:
      - `SingleThreadExecutor`: contains only 1 Thread
      - `SingleThreadScheduledExecutor`: only 1 thread, but will run tasks with a predefined delay
      - `CachedThreadPool`: automatically create new thread when existing ones are busy. Finished task won't immediately destroyed but wait a bit (i.e. "cached") in case new task come in --> may create max int of threads
      - `FixedThreadPool`: fixed number of threads in pool
      - `ScheduledThreadPool`: similar to `SingleThreadScheduledExecutor`, but have more than 1 threads

## Concurrent Collection and Atomic variables
  - Normal type doesn't allow changing collection when looping --> Concurrent collection allowed this as it allow locking per segment
  - CopyOnWriteList/Set: create a new copy on modification
    + good for read, bad for lot of write --> spawning rubbish
  - `Collections.synchronizedXXX()`: convert normal collection into Synchronized collection (i.e. collection safe in multi-threads environment) --> locking the whole object ?
    + bad performance, better use Concurrent collection
    + can't be modified in a loop
  - Atomic classes: simple types with "atomic" method (i.e. as a single unit of execution)
  
## Threading problems
  - Liveness of application: responsiveness, memory consistency and data integrity
  - Deadlock: thread waiting for each other indefinitely
  - Livelock: thread running & trigger each other in a loop
  - Starvation: low priority thread cannot access resources, as high priority ones occupy the required resources
  - Race condition: resource access is not shared correctly --> results are undeterministics, and depending on the order of access