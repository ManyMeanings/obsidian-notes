---
routes.js
---

import { createRouter, createWebHistory } from "vue-router";
import Page1 from "./pages/Page1.vue";
import Page2 from "./pages/Page2.vue";
import Page3 from "./pages/Page3.vue";

const routes = [
  {
    path: "/",
    component: Page1,
  },
  {
    path: "/:postId",
    component: Page2,
    name: 'post'
    children: [{ path: "detail", component: Page3 }],
  },
];

const router = createRouter({
  history: createWebHistory(),
  routes,
});

export default router;

// ---main.js---
import router from "./routes";
app.use(router);

```

### 基本用法

```html
<!--App.vue-->
<nav>
  <router-link to="/">首页</router-link>
  <router-link :to="{ name: 'post', params: { postId: 1 }, query: {} }">{{ postName }}</router-link>
</nav>
<router-view></router-view>
```

### 同组件路由参数变化的情况

```js
export default {
  // 侦听路由变化，改变对应的响应性变量
  watch: {
    "$route.params": {
      handler(params, oldParams) {
        this.blogPost = getBlogPostById(params.postId);
      },
      immediate: true,
    },
  },
};
```

### 导航守卫

1. 先执行组件中的 beforeRouteLeave() 导航守卫 （如果有的话）。
2. 执行全局的 beforeEach() 导航守卫。
3. 在复用的组件中，执行 beforeRouteUpdate() 导航守卫。
4. 执行路由对象中的 beforeEnter() 导航守卫。
5. 解析异步的导航组件
6. 执行组件中的 beforeRouterEnter() 导航守卫。
7. 执行全局的 beforeResolve() 导航守卫。
8. 确认导航，并发生页面跳转。
9. 执行全局的 afterEach() 导航守卫。
10. 更新 DOM 节点。
11. 执行组件中 beforeRouterEnter 中的 next() 回调函数中的回调函数
