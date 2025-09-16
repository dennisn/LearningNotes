# C# Performance Playbook
  - Pluralsight by Chris Behrens
  - If you can fix a problem by throwing hardware at it, you should --> cheaper than develop a fix & maintain/support it
  - Different dimention of quality:  maintainability, readability, deployability, and security
    + May conflict with performance, and conflict among themselves
  - When not to optimise !
    + Should start with Just-in-time optimise + good coding habits --> All optimization is premature optimization unless it's just‑in‑time
    + Inordinately pursuing performance will compromise other quality dimensions, which may have greater impact
  - **BenchmarkDotNet**: https://benchmarkdotnet.org/index.html
    + Remember to run benchmarks in "Release" mode --> avoid debug optimisation distorted the result