#webpack
[webpack](http://webpack.github.io)是一种module bundler，目前首选的构建工具，而且有一统江山的趋势。

##webpack config

###[devtool](http://webpack.github.io/docs/configuration.html#devtool)


##webpack-dev-middleware
对于 `webpack-dev-middleware`，最直观简单的理解就是一个运行于内存中的文件系统。你定义了 `webpack.config`，webpack 就能据此梳理出所有模块的关系脉络，而 `webpack-dev-middleware` 就在此基础上形成一个微型的文件映射系统，每当应用程序请求一个文件——比如说你定义的某个`entry` ，它匹配到了就把内存中缓存的对应结果作为文件内容返回给你，反之则进入到下一个中间件。

因为是内存型的文件系统，所以 rebuilding 的速度非常快，因此特别适合于开发阶段用作静态资源服务器；又因为 webpack 可以把任何一种资源都当作是模块来处理，因此它完全可以取代其他的 HTTP 服务器。事实上，大多数 webpack 用户用过的 `webpack-dev-server` 就是一个`express`＋`webpack-dev-middleware`的实现。二者的区别仅在于 `webpack-dev-server` 是封装好的，除了 `webpack.config` 和命令行参数之外，你很难去做定制型开发，所以它是适合纯前端项目的辅助工具。而 `webpack-dev-middleware` 是中间件，你可以编写自己的后端服务然后把它整合进来，相对而言就自由得多了。我们做的是一个前后同构的应用，因此 `webpack-dev-server` 就不予考虑了。
但是新的问题在于 `webpack-dev-middleware` 是 express 标准的中间件，并不能直接用于 Koa。

一个标准的 express 中间件是这样的：
```
expressApp.use((req, res, next) => {
  if (nextNeeded) {
    // do what you want
    // until you need down-stream middleware(s)
    next();
  } else {  
    // anything else, e.g. sending response
  }
});
```
而一个标准的 Koa（v2.x）中间件是这样的：
```
koaApp.use(async (context, next) => {
  const beforeNextMiddleware = await doSomething();
  try {
    await next();
  } catch (error) {
    context.body = { message: error.message };
    context.status = error.status || 500;
  }
  return andMore().then(() => evenAfterDownStreams());
});
```
所以`webpack-dev-middleware`用在 Koa 里，需要封装一层中间件来协调两种不同的参数签名。

官方推荐了一个现成的封装 [koa-webpack-middleware](https://github.com/leecade/koa-webpack-middleware) webpack-dev-middleware for koa2 with HMR(hot module replacement) supports.

##webpack-isomorphic-tools
为了解决在 Node.js 中无法 require 静态资源的问题，引入了
[webpack-isomorphic-tools](https://github.com/halt-hammerzeit/webpack-isomorphic-tools)
在 webpack 编译前端代码时，将资源文件进行映射，存储在一个 JSON 文件中，然后 Node.js Server 找到该文件， hack Node.js 的 require 语句

##webpack plugins
[webpack plugins list](https://github.com/webpack/docs/wiki/list-of-plugins)
