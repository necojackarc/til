# Show Elapsed Time

In Node.js, to get an elapsed time, you can use `process.hrtime` or `process.hrtime.bigint`.
The former is the legacy version of the latter before `bigint` was introduced in JavaScript.

## `process.hrtime`

```js
const NS_PER_SECONDS = 1e9; // 1,000,000,000

const elapsed_time_in_seconds = (start, scale = 3) => {
  const elapsed_time = process.hrtime(start); // returns [ seconds, nanoseconds ]
  // NOTE: Be aware of representable number range
  const elapsed_time_in_seconds = elapsed_time[0] + (elapsed_time[1] / NS_PER_SECONDS);
  return elapsed_time_in_seconds.toFixed(scale);
};

const start = process.hrtime();

process.on('exit', () => console.log('EXECUTION TIME:', elapsed_time_in_seconds(start), 'seconds'));
```

## `process.hrtime.bigint`

```js
const NS_PER_SECONDS = 1e9; // 1,000,000,000

const elapsed_time_in_seconds = (start, scale = 3) => {
  // The new version doesn't take a time nor a bigint
  // probably because you can simply do arithmetic operations on bigint objects.
  const end = process.hrtime.bigint();
  const elapsed_time = end - start;
  const scaler = 10 ** scale;
  const elapsed_time_in_seconds = parseFloat((elapsed_time * BigInt(scaler)) / BigInt(NS_PER_SECONDS)) / scaler;
  return elapsed_time_in_seconds.toFixed(scale);
};

const start = process.hrtime.bigint();

process.on('exit', () => console.log('EXECUTION TIME:', elapsed_time_in_seconds(start), 'seconds'));
```
