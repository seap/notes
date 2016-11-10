
#React Roadmap

#React 生命周期

#React 服务端渲染

##React.renderToString
把 React 元素转成一个 HTML 字符串，
```
var MyComponent = React.createClass({
  render:fucniton(){
  return <div> Hello World!</div>;
  }
});
var world= React.renderToString (<MyComponent/>);

//这个示例返回一个单行并且格式化的输出
<div data-reactid="1" data-ract-checksum="-1663559667">
Hello World!
</div>
```
- data-reactid: 在浏览器环境下，React使用`data-reactid`区分DOM节点。这也是每当组件的state及props发生变化时，React都可以精准的跟新制定DOM节点的原因。
- data-react-checksum: 仅仅存在于服务端。顾名思义，它是已创建DOM和校验和。这准许React在客户端服用与服务端结构上相同点的DOM结构。该属性只会添加到跟元素上。

##React.renderToStaticMarkup



##React.renderToString vs. React.renderToStaticMarkup

##Speed up Server Side Rendering
- 使用 Production Mode: NODE_ENV=production
- 加入 transform-react-constant-elements 与 transform-react-inline-elements 插件，减少执行期的计算
- 使用 React.min 文件，减少 React 内部执行环境的判断
- 避免使用 React.createClass，使用 es2015 的 class （不要让 babel 转换成 es5）
- 使用 Chunked Encoding，[react-dom-stream](https://github.com/aickin/react-dom-stream)
  https://www.youtube.com/watch?v=PnpfGy7q96U
  https://github.com/aickin/react-server-perf-tricks
