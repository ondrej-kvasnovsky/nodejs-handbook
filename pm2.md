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
$ pm2 status
$ pm2 show index
$ pm2 monit
$ pm2 restart index
$ pm2 delete index
$ pm2 kill
$ npm install pm2@latest -g ; pm2 update
$ pm2 scale index=2
```

### Make the server running when server starts up

```
$ npm i -g pm2
$ pm2 start index.js
$ pm2 startup
$ pm2 save
$ pm2 ls
```



