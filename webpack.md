#webpack
[webpack](http://webpack.github.io)是一种module bundler，目前首选的构建工具，而且有一统江山的趋势。

##webpack config

###[devtool](http://webpack.github.io/docs/configuration.html#devtool)

**entry**: 指定打包的入口文件，每有一个键值对，就是一个入口文件。
###output###
配置打包结果，path定义了输出的文件夹，filename则定义了打包结果文件的名称，filename里面的[name]会由entry中的键替换,例子中的/build/bundle.js便是生成的文件。

`output.chunkFilename`用于配置按需加载，是未被列在`entry`中，却又需要被打包出来的文件命名配置

```
require.ensure([], require => callback(null, require('./container/Page').default), 'page')
```

异步加载的模块是要以文件形式加载，所以这时生成的文件名是以`chunkname`配置的，生成出的文件名就是`page.min.js`

`require.ensure()` API的第三个参数是给这个模块命名，否则 `chunkFilename: "[name].min.js"` 中的 `[name]` 是一个自动分配的、可读性很差的id。参见：[named chunk](http://webpack.github.io/docs/code-splitting.html#named-chunks)


**resolve**：定义了解析模块路径时的配置，常用的就是extensions，可以用来指定模块的后缀，这样在引入模块时就不需要写后缀了，会自动补全.
**module**：定义了对模块的处理逻辑，这里可以用loaders定义了一系列的加载器，以及一些正则。当需要加载的文件匹配test的正则时，就会进行处理。这里我们使用了react-hot 和 babel。babel-loader是我们使用ES-6进行开发时用于生成JS文件。
最后我们生成了一个style.css仅仅做个例子，告诉我们如何引入样式文件，实际上我们可以加载诸如sass-loader这样的加载器。
**loader**:对文件进行处理，这正是webpack强大的原因。比如定义了凡是.js结尾的文件都是用babel-loader做处理，而.jsx结尾的文件会先经过jsx-loader处理，然后经过babel-loader处理。当然这些loader也需要通过npm install安装。
**plugins**: 这里定义了需要使用的插件，比如commonsPlugin在打包多个入口文件时会提取出公用的部分，生成common.js。


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

##[webpack-isomorphic-tools](https://github.com/halt-hammerzeit/webpack-isomorphic-tools)
为了解决在 Node.js 中无法 require 静态资源的问题，引入了webpack-isomorphic-tools

1. 以webpack插件的形式，预编译sass, less，图片文件、字体文件等，将其转换为一个 `assets.json` 文件保存到项目目录下。
2. require hook，所有less文件的引入，代理到生成的 JSON 文件中，匹配文件路径，返回一个预先编译好的 JSON 对象。

在 webpack 编译前端代码时，将资源文件进行映射，存储在一个 JSON 文件中，然后 Node.js Server 找到该文件， hack Node.js 的 require 语句

http://www.aliued.com/?p=3077



##webpack plugins
[webpack plugins list](https://github.com/webpack/docs/wiki/list-of-plugins)
