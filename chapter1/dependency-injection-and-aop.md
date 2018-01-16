# Dependency Injection and AOP

[Dependency injection](https://en.wikipedia.org/wiki/Dependency_injection) in JavaScript might seem to be a weird topic to solve at first look. Anyway, dependency injection in JavaScript might be explained to be useful with the following sentence, "W_e want to be able to inject code based on a key, rather than referencing a file that contains the code we want to use."_. When we reference a code, like a class or a method, by a key, we can easily reference code and thus also switch the code. There might be also benefits when it becomes to testing and refactoring.

We will get some kind of AOP when [decorators](https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841) are implemented in JavaScript. Anyway, I am not sure if we are going to be able to solve the following. I want to be able to hook at some code execution based on naming conventions. Lets say, I want to measure performance of all methods in my controller. I want to be able to define something like this. 

    const pointcuts = [
      {
        'components': ['.*Controller'],
        'methods': ['.*'],
        'handler': {
          apply: async function (target, thisArg, args) {
            const start = new Date()

            // call a method on controller
            const result = await target.apply(thisArg, args)

            const diff = new Date() - start
            console.log(`Time: ${diff}`) // report time it took to execute
            return result
          }
        }
      }
    ]

Now I want to be able to pass these [pointcuts](https://en.wikipedia.org/wiki/Pointcut) to my application. Lets say we can use a library di-and-aop that will instantiate our code and apply our pointcuts to the code execution. 

The code that would do it would look like this. The pointcut definitions would be somewhere around the code that starts up the server, because it needs to be passed to a library that will create a context, that contains all the code that can be referenced by keys. 

    const ContextBuilder = require('di-aop-context-builder')

    const componentScan = [
      {
        'dir': 'src',
        'include': '**/*.js'
      }
    ]

    const pointcuts = [
      {
        'components': ['.*Controller'],
        'methods': ['show'],
        'handler': {
          apply: async function (target, thisArg, args) {
            const start = new Date()

            const result = await target.apply(thisArg, args)

            const diff = new Date() - start
            console.log(`Time: ${diff}`) // report time it took to execute
            return result
          }
        }
      }
    ]

    const contextBuilder = new ContextBuilder(componentScan, pointcuts)

    contextBuilder.getContext().getComponent('app').listen(3000, function () {
      const host = 'localhost'
      const port = '3000'

      console.log('Listening at http://%s:%s', host, port)
    })

Full example of this application is available on [github](https://github.com/ondrej-kvasnovsky/di-aop-context-builder-koa).

### di-aop-context-builder library

I have created a library named [`di-aop-context-builder`](https://www.npmjs.com/package/di-aop-context-builder) that takes `componentScan` and `pointcuts`  variables and creates a context that contains all the code references. When we can call `getContext()` method, we get our component and we can start up the server. `app` component, which we obtain from context, is actually an instnace of koa server.

> The library is just an experiment, to think about dependency injection and aspect oriented programming using pointcuts in JavaScript and NodeJS.



