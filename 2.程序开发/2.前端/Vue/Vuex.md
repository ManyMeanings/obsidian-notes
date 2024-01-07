- state、getters、mutations 和 actions 
- state：定义状态 
- getters： 定义状态计算逻辑 
- mutations： 修改状态
- actions：异步修改状态

- `store.commit()` 触发 mutations 
- `store.dispatch()` 触发 actions 
- 组件在计算属性中通过 `this.$store.state` 访问状态，通过 `this.$store.getters` 访问 getters 
- 组件通过 `this.$store.commit()` 触发 mutations 
- 组件通过 `this.$store.dispatch()`触发 actions

- `mapState()`、`mapGetters()`、`mapMutations()`、` mapActions() `
- 模块化 & 命名空间 
- 插件 
- Composition API，`useStore()`
