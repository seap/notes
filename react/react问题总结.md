> 整理react开发过程中遇到的问题，及相关的解决方案

# window is not defined

服务端渲染基本上也只会在生产环境开启，所以特别容易犯这个错误。解决方案就是搞清楚React的生命周期，只有在 
componentDidMount下才允许使用BOM和DOM。这条守则必须加到编码规范中！！！

# 单页应用中背景颜色

```
# 在各个container中慎用body相关样式
body {
    background: #FFF
}
```



# 单页应用中iOS的微信title不更新问题

找了很多解决方案，最终还是选择iframe的方案

```
var body = document.getElementsByTagName('body')[0];
document.title = "标题被我改了";
var iframe = document.createElement("iframe");
iframe.setAttribute("src", "loading.png");
iframe.addEventListener('load', function() {
setTimeout(function() {
  iframe.removeEventListener('load');
    document.body.removeChild(iframe);
  }, 0);
});
document.body.appendChild(iframe);
```

```
// view
<div>
  <iframe :src="iframe" style="display: none"></iframe>
</div>
// javascript
data: {
  iframe: ''
},
methods: {
  setTitle: function (title) {
    document.title = title
    // 判断是否为ios设备，ios设备需要通过加载iframe来刷新title
    if (navigator.userAgent.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/)) {
      this.iframe = '/favicon.ico' + Math.random()
    }
  }
}
```

写成react component，方便复用

```
import React, { Component, PropTypes } from 'react';
import styles from './main.scss';
import { shouldComponentUpdate } from 'react/lib/ReactComponentWithPureRenderMixin';

export default class TitleRefresh extends Component {
  constructor() {
    super();
    this.shouldComponentUpdate = shouldComponentUpdate.bind(this);
  }

  componentDidMount() {
    const agent = navigator.userAgent;
    if (/MicroMessenger/i.test(agent) && /iPhone|iPad/i.test(agent)) {
      this.renderIframe = () => {
        return <iframe ref={iframe => this.iframe = iframe}/>
      }
      this.forceUpdate();
    }
  }

  componentDidUpdate() {
    if (this.iframe) {
      setTimeout(() => {
        this.iframe.src = '//seap.github.io/favicon.ico';
      }, 0);
    }
  }

  renderIframe = () => {
    return null;
  }

  render() {
    return this.renderIframe();
  }
}

//main.css
iframe{
  width: 0;
  height: 0;
  background: #f00;
  opacity: 0;
}
```



# 移动端onscroll事件在部分浏览器内不能实时触发

[http://andyshora.com/mobile-scroll-event-problems.html](http://andyshora.com/mobile-scroll-event-problems.html) 
[https://segmentfault.com/q/1010000004453730](https://segmentfault.com/q/1010000004453730)



# react-router-redux 需要返回两次issue

```
//修改sync.js
// Are we being called for the first time?
if (!initialLocation) {
  // Remember as a fallback in case state is reset
  initialLocation = location;
  // Respect persisted location, if any
  if (getLocationInStore()) {
    return;
  }
} else {
  // do not dispatch action in the first time
  store.dispatch({
    type: _reducer.LOCATION_CHANGE,
    payload: location
  });
}
// Tell the store to update by dispatching an action
```



