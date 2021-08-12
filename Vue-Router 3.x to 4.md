# Vue-Router 3.x to 4

######  Tags: `Vue3`、`Vue-Router4`

![](https://kanboo.github.io/2018/08/06/VueRouter/vuerouter.jpg)

==僅是紀錄 平常工作會用到的東東==

## 安裝方式

從 `new Router(...)` 改為 `createRouter` 建立 router 實體。

```javascript
// router/index.js

import { createRouter } from 'vue-router';

const router = createRouter({
  // ...
});

export default router;
```

```javascript
// main.js

import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

const app = createApp(App);

app.use(router).mount('#app');
```

## 路由模式
從原本的 `mode: 'history' | 'hash'`
改為 `history: 'createWebHistory()' | 'createWebHashHistory()'`
```javascript
// router/index.js

import { createRouter, createWebHistory } from 'vue-router';
import Home from '../views/Home.vue';
import About from '../views/About.vue';

const routes = [
    { path: '/', component: Home, },
    { path: '/about', component: About, },
];

const router = createRouter({
    history: createWebHistory(),
    routes,
})

export default router;
```

## 移除 * 路由
通常遇到使用者輸入非設定好的路由時，我們會將使用者導向 404 頁面，或者是重新導向至首頁。
```javascript
// ...

{ path: '/:pathMatch(.*)*', component: Notfound, }

// OR

{ path: '/:pathMatch(.*)*', redirect: '/', }

// ...
```
## Vue-Router In Composition API - useRoute 、 useRouter 
使用 `useRoute` 建立 route，可以直接使用 route 物件。
可以查看路徑資訊，像是 `route.params` 或者是 `route.query`。

使用 `useRouter` 建立 router，可以直接使用 router 實體。
像是進行路由的轉換，從 Vue2 的 `this.$router.push()` 改為 Vue3的 `router.push()`。
```javascript
<template>
    <h1>This is an about page</h1>
    <button type="button" @click="toIndex">回首頁</button>
</template>

<script>
import {
  useRouter,
  useRoute,
} from 'vue-router';

export default {
    setup() {
        const router= useRouter();
        const route= useRoute();
        
        const toIndex = () => {
            router.push('/')
        };
        
        return {
            toIndex,
        };
    },
};
```




