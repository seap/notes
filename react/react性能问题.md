#性能问题
- 父组件更新默认触发所有子组件更新
- 列表类型的组件默认更新方式非常复杂


# 性能解决方案

- PureRenderMixin
- Immutability Helpers
- Immutable.js

##[Fackbook Immutability Helpers](http://facebook.github.io/react/docs/update.html)

实现JS不可变对象， 修改不可变对象会返回一个新的对象，之前的对象保持不变。
不同的对象在JS引擎中引用不同，因此可以直接比较引用即可确定是否发生改变。 
基本语法：Update(obj, cmd) 


##[Immutable JS](https://facebook.github.io/immutable-js/docs)
