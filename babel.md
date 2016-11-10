#Babel
[Babel](https://babeljs.io/)是一个编译器（javascript compiler），可以将ES6代码转为ES5代码，也可以将React中的JSX转为javascript。

##配置文件.babelrc
Babel的配置文件是`.babelrc`，用来设置转码规则和插件，存放在项目的根目录下。
```
{
  "presets": ["es2015", "stage-0", "react"],
  "plugins": [
    ["transform-decorators-legacy"],
  ]
}
```
### presets

`presets`字段设定转码规则，官方提供以下的规则集，你可以根据需要安装。

```
# ES2015转码规则
$ npm install --save-dev babel-preset-es2015

# react转码规则
$ npm install --save-dev babel-preset-react

# ES7不同阶段语法提案的转码规则（共有4个阶段），选装一个
# stage[0-3]是代表了不同阶段，0是代表刚开始讨论，而3是代表快要成为标准规范
# stage-0 其实包含了 stage-[1-3]，也就是说你装了0 ，其他三个默认就装了
$ npm install --save-dev babel-preset-stage-0
$ npm install --save-dev babel-preset-stage-1
$ npm install --save-dev babel-preset-stage-2
$ npm install --save-dev babel-preset-stage-3
```

stage3包含以下两个插件：
- transform-async-to-generator
- transform-exponentiation-operator

stage2包含stage3的所有插件，额外还有以下插件：
- syntax-trailing-function-commas
- transform-object-reset-spread

stage1包含stage2所有插件，额外还有以下插件：
- transform-class-constructor-call (Deprecated)
- transform-class-properties
- transform-decorators – disabled pending proposal update
- transform-export-extensions

stage0包含stage1所有插件，额外还有以下插件：
- transform-do-expressions
- transform-function-bind

### plugins

Babel插件被设计用来渐增的编译代码，也方便用户配置一些单一的标准。假设你想使用ES2019的特性的话，那么代码首先被编译为ES2018， 然后是ES2017，直到编译为浏览器支持的版本。这允许了用户能够使用它们想要使用的原生实现。总之：*一些我们需要的小功能，可以通过plugin来配置*。

我们工程中需要使用decorator，所以在babelrc里面，加了plugins: [‘transform-decorators-legacy’]

## [babel-register](https://babeljs.io/docs/usage/require/)

`babel-register`模块改写`require`命令，为它加上一个钩子。后面每当使用`require`加载`.js`、`.jsx`等后缀名的文件，就会先用Babel进行转码

```
$ npm install --save-dev babel-register
```

使用时，必须首先加载`babel-register`。

```
require("babel-register");
require("./index.js");
```

然后，就不需要手动对`index.js`转码了。需要注意的是，`babel-register`只会对`require`命令加载的文件转码，而不会对当前文件转码。另外，由于它是实时转码，所以只适合在开发环境使用。

## babel-polyfill

Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如`Object.assign`）都不会转码。举例来说，ES6在`Array`对象上新增了`Array.from`方法。Babel就不会转码这个方法。如果想让这个方法运行，必须使用`babel-polyfill`，为当前环境提供一个垫片。
