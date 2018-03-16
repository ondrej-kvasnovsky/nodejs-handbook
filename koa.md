# Koa

### HTTP server with Router

Create a project. 

```
$ mkdir koa-demo
$ cd koa-demo
$ npm init
$ npm install koa
$ npm install koa-router
```

It should result in `package.json` like this. I have added `start` script to easier starting up the service.

```
{
  "name": "app",
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
    "koa": "2.5.0",
    "koa-router": "7.4.0"
  }
}
```

Then we need to create `index.js` that will start up the server and add router URL path mapping. 

```
var Koa = require('koa');
var Router = require('koa-router');

var app = new Koa();
var router = new Router();

router.get('/', (ctx, next) => {
  ctx.body = 'test response'
});

app
  .use(router.routes())
  .use(router.allowedMethods());

app.listen(4444);
```

Then we can run the server: 

```
$ npm start
> koa-test@1.0.0 start /Users/ondrej/projects/koa-demo
> node index.js
```



