# NodeJS

## Big pictures
  - Node.js is the runtime environment for javascript (i.e. replace the browser as the running container) --> allow javascript to run on desktop, interact with OS, etc.
  - It has an extensive libraries of components, updated continuously
  - Characteristics: light-weight, single threads but is event-driven ==> can handle concurrent tasks in pseudo-parallel
  - `npm`: package management system (i.e. registry, commandline tools to install & manage packages), run node
    + `npm audit`: check for security problems with installed packages
    + `npm update`: update installed package (normally will keep the same major version)
    + `npm audit fix`: specifically fix security problems by pulling patched version

## IO
  - 3 ways: synchronous, async. with callback and async with await (i.e. modern async.)
  - Local file system vs Cloud:
    + **Local FS**: files, organised into directory
    + **Clould**: objects with id, organised into bucket

### Local filesystem
  - `await access(<filepath>, constants.R_OK)`: check if can read the file. Other constants: W_OK, X_OK
  - `await chmod(<filepath>, 0o600)`: change permission to read+write for owner only (i.e. Octa 6 + 0 + 0)
  - `const stats = await stats(<filepath>)`: get the statistics of the file, such as size, created/modified/access time
  - `await unlink(<filepath>)`: delete the file
  - `const files = await readdir(<path>)`: read all filenames in given path

### Stream
  - Benefits: 
    + can handle large IO with a constant resources required
    + Efficient with low latency: using event loop, start processing from the first arrived chunks
  - Types: Read, Write, Duplex (e.g. sockets), Transform
  - `pipe()`: available for all "read" stream --> connect the output to next stream, handle the backpressure
    + Sample: `readStream.pipe(transformStream).pipe(writeStream);`
  - `pipeline()`: similar to `pipe()`, but coordinate the error handling & tear down of component streams
    + Sample: `await pipeline(readStream, transformStream, writeStream);`
  - Fan-out: data split to multiple pipe-line
    + Implementation: `pipe()` to different transform/consumer
      ```
      src.pipe(a);
      src.pipe(b);
      ```
    + Alternative implementation: using PassThrough object as splitter
      ```
      const tee = new PassThrough()
      src.pipe(tee);
      tee.pipe(a);
      tee.pipe(b);
      ```
    + Common problems:
      - Back-pressure is shared: all pipes run with the slowest consumer
      - Chunk are duplicated: not problem with data, but object reference will be shared, and may in-adversely have side-effects
 #### Side effects: 
 - Use transform to spy on the data flow through the pipeline --> to log, audit, etc.
    + Consideration:
      - must not be slow/async --> will slow down the whole pipeline
      - Must be idempotency --> in case the data is resent via retry
      - Explicitly scope failure of side effects (i.e. should the pipe fail when the side-effect transform encounter error ?)

#### Error handling
  - With `pipe()`: error emits by each components --> must attach error handling to each component separately
    + Complication: ensure clean up code work correctly (i.e. not multiple times), and ensure other components are shut down properly when one component failed
  - `pipeline()`: simplify above complication by co-ordinate error into one call, and automate tear down of component streams
  - NOTE: pipeline + temp output + idempotent side effects ==> basic pattern