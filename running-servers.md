# Running Servers

### Out of memory error

When NodeJS runs out of memory, it gives this kind of message.

```
<--- Last few GCs --->

[2101:0x2a1fd10]    90609 ms: Mark-sweep 1318.6 (1432.8) -> 1318.6 (1433.3) MB, 232.9 / 0.1 ms  allocation failure GC in old space requested
[2101:0x2a1fd10]    90780 ms: Mark-sweep 1318.6 (1433.3) -> 1318.6 (1426.3) MB, 170.5 / 0.1 ms  last resort GC in old space requested
[2101:0x2a1fd10]    90949 ms: Mark-sweep 1318.6 (1426.3) -> 1318.6 (1426.3) MB, 168.6 / 0.1 ms  last resort GC in old space requested


<--- JS stacktrace --->

==== JS stack trace =========================================

Security context: 0x2ad85dfa54d9 <JSObject>
    1: stringSlice(aka stringSlice) [buffer.js:590] [bytecode=0x23db3fc6a931 offset=94](this=0x2c01e97822d1 <undefined>,buf=0x1ed63e446e59 <Uint8Array map = 0x18e39be44eb9>,encoding=0x2ad85dfb5ac1 <String[4]: utf8>,start=0,end=271728)
    2: handleStreamEnd [/root/node_modules/axios-proxy-fix/lib/adapters/http.js:~187] [pc=0x8f091dd2a6a](this=0xa759cd79451 <IncomingMessage map = 0x18e39be77cb9>)...

FATAL ERROR: CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory
 1: node::Abort() [node]
 2: 0x8c807c [node]
 3: v8::Utils::ReportOOMFailure(char const*, bool) [node]
 4: v8::internal::V8::FatalProcessOutOfMemory(char const*, bool) [node]
 5: v8::internal::Factory::NewRawTwoByteString(int, v8::internal::PretenureFlag) [node]
 6: v8::internal::Factory::NewStringFromUtf8(v8::internal::Vector<char const>, v8::internal::PretenureFlag) [node]
 7: v8::String::NewFromUtf8(v8::Isolate*, char const*, v8::NewStringType, int) [node]
 8: node::StringBytes::Encode(v8::Isolate*, char const*, unsigned long, node::encoding, v8::Local<v8::Value>*) [node]
 9: 0x8e2f22 [node]
10: v8::internal::FunctionCallbackArguments::Call(void (*)(v8::FunctionCallbackInfo<v8::Value> const&)) [node]
11: 0xaddc5c [node]
12: v8::internal::Builtin_HandleApiCall(int, v8::internal::Object**, v8::internal::Isolate*) [node]
13: 0x8f091c842fd
```

We need to solve memory leak or increase the memory node can use.

```
node --max_old_space_size=4096 index.js
```

### pm2

[pm2](http://pm2.keymetrics.io) makes it possible to utilize all CPUs available on the server. Lets install `pm2` on a server.

```
npm install -g pm2
```

We are going to create a sample project that will use `pm2`. 

First create `package.json` with required dependencies and scripts that are going to help us to run the code.

```
{
  "name": "pm2-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "koa": "^2.5.0",
    "koa-router": "^7.4.0"
  }
}

```

Now we create `index.js` that will create `koa` HTTP server and user `koa-router` to route HTTP requests to our code.

```
var Koa = require('koa');
var Router = require('koa-router');

var app = new Koa();
var router = new Router();

router.get('/', (ctx, next) => {
  ctx.body = 'Hello world';
});

app
  .use(router.routes())
  .use(router.allowedMethods());

app.listen(3000);
```

We are done with coding and we want to install dependencies and start the server. `npm start` will start single instance of NodeJS that will handle our requests. 

```
$ npm install
$ npm start
```

Lets use all CPUs available and create multiple NodeJS instance on the server. 

```
$ pm2 start -i max index.js
[PM2][WARN] You are starting 0 processes in fork_mode without load balancing. To enable it remove -x option.
[PM2] Applying action restartProcessId on app [index](ids: 0,1,2,3,4,5,6,7)
[PM2] [index](1) ✓
[PM2] [index](0) ✓
[PM2] [index](2) ✓
[PM2] [index](3) ✓
[PM2] [index](4) ✓
[PM2] [index](5) ✓
[PM2] [index](6) ✓
[PM2] [index](7) ✓
[PM2] Process successfully started
```

We can stop all the instances, still using pm2. 

```
$ pm2 stop index
[PM2] Applying action stopProcessId on app [index](ids: 0,1,2,3,4,5,6,7)
[PM2] [index](0) ✓
[PM2] [index](1) ✓
[PM2] [index](2) ✓
[PM2] [index](3) ✓
[PM2] [index](4) ✓
[PM2] [index](5) ✓
[PM2] [index](6) ✓
[PM2] [index](7) ✓
```

Other useful commands are: 

```
$ pm2 show index
$ pm2 monit
$ pm2 restart index
$ pm2 delete index
$ pm2 kill
$ npm install pm2@latest -g ; pm2 update
```











