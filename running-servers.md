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



