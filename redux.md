Redux 提供了一套类似`Flux`的单向数据流，整个应用只维护一个 Store

Store的几个特点：
- 整个应用只有一个唯一的 Store
- Store 对应的状态树（State），由调用一个 reducer 函数（root reducer）生成
- 状态树上的每个字段都可以进一步由不同的 reducer 函数生成
- Store 包含了几个方法比如 dispatch, getState 来处理数据流
- Store 的状态树只能由 dispatch(action) 来触发更改
