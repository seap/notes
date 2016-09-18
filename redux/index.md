## Redux 的基础概念：

- Redux 的核心是一个 store。
- store 是一个 JavaScript 对象，通过 Redux 提供的 createStore(reducers) 方法创建。store 有两个核心方法： .getState() 和 .dispatch()。
- .getState() 返回一个 JavaScript 对象，代表 store 当前的状态。
- .dispatch() 接受一个 action 作为参数，将这个 action 分发给所有订阅了更新的 reducer。
- action 是一个 JavaScript 对象，通常包含了 type、payload 等字段，用于描述发生的事件及相关信息（使用 Redux 中间件可以让你 dispatch 其它类型的 action，此处不展开）。
- reducer 是一个 JavaScript pure function，函数签名为 (previousState, action) => newState，即接受 previousState 和 action 两个参数，根据 action 中携带的信息对 previousState 做出相应的处理，并返回一个新的 state。