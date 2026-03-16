# I/O in Node.js

- Pluralsight course: <https://app.pluralsight.com/ilx/video-courses/nodejs-file-system-streams-asyncio-uploads-pipes/course-overview>
- Three I/O styles: synchronous, asynchronous with callbacks, and asynchronous with `async/await` (modern async).
- Local file system vs cloud storage:
  - **Local FS**: files organized into directories.
  - **Cloud**: objects with IDs organized into buckets.

## Local Filesystem

- `await access(<filepath>, constants.R_OK)`: check if the file is readable. Other constants: `W_OK`, `X_OK`.
- `await chmod(<filepath>, 0o600)`: set read+write permission for owner only (octal `600`).
- `const stats = await stat(<filepath>)`: get file statistics like size and created/modified/access time.
- `await unlink(<filepath>)`: delete a file.
- `const files = await readdir(<path>)`: read all filenames in a directory.

## Streams

- Benefits:
  - Handle large I/O with constant resource usage.
  - Efficient and low-latency: start processing from the first chunks via the event loop.
- Types: `Readable`, `Writable`, `Duplex` (for example, sockets), and `Transform`.
- `pipe()`: available on readable streams; connects output to the next stream and manages backpressure.

```js
readStream.pipe(transformStream).pipe(writeStream);
```

- `pipeline()`: similar to `pipe()`, but coordinates error handling and teardown across component streams.

```js
await pipeline(readStream, transformStream, writeStream);
```

- Fan-out: split one source stream to multiple pipelines.
- Implementation: use `pipe()` to different consumers.

```js
src.pipe(a);
src.pipe(b);
```

- Alternative implementation: use a `PassThrough` stream as a splitter.

```js
const tee = new PassThrough();
src.pipe(tee);
tee.pipe(a);
tee.pipe(b);
```

- Common problems:
  - Backpressure is shared; all pipes run at the speed of the slowest consumer.
  - Chunks are duplicated by reference; data may still be correct, but shared object references can cause unintended side effects.

### Side Effects

- Use a transform stream to observe data flow for logging, auditing, etc.
- Considerations:
  - It should not be slow or asynchronous, or it can slow the whole pipeline.
  - It should be idempotent in case data is re-sent during retries.
  - Explicitly decide side-effect failure behavior (for example, whether pipeline execution should fail if the side-effect transform errors).

### Error Handling

- With `pipe()`: errors are emitted by each component, so attach handlers to each stream.
- Complication: ensure cleanup runs correctly (not multiple times), and ensure all related streams are shut down when one component fails.
- With `pipeline()`: simplifies this by coordinating errors in one call and automating stream teardown.
- Note: `pipeline` + temporary output + idempotent side effects is a useful baseline pattern.
