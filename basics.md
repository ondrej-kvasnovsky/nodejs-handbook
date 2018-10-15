# Basics

[Event demultiplexing](https://jsblog.insiderattack.net/event-loop-and-the-big-picture-nodejs-event-loop-part-1-1cb67a182810) is used to avoid waiting for I/O events and is design principle behind [Reactor pattern](https://hackernoon.com/the-node-js-system-51090c35dddc), which is the main principle in NodeJS - [event loop](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/).

## Asynchronous and synchronous operations

We should avoid synchronous functions in the code because they block the event loop. We also shouldn't mix synchronous and asynchronous code in single function.

```
const fs = require('fs');
const cache = {};
const inconsistentRead = function(fileName, callback) {
  if (cache[fileName]) {
    // invoked synchronously
    // callback(cache[fileName]);
    // make it asynchronous - and puts it before any IO event in the event loop
    process.nextTick(() => callback(cache[fileName]));
    // make it asynchronous - and puts it at after all the IO events in the event loop
    setImmediate(() => callback(cache[fileName]));
  } else {
    // invoked asynchronously
    fs.readFile(fileName, 'utf8', (err, data) => {
      cache[fileName] = data;
      callback(data);
    })
  }
};
```



