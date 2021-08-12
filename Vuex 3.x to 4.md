# Vuex 3.x to 4

######  Tags: `Vue3`、`Vuex4`

![](https://kanboo.github.io/2018/08/19/Vuex/Vuex.png)

==僅是紀錄 平常工作會用到的東東==

## 安裝方式

從 `new Store(...)` 改為 `createStore` 建立 store 實體。

```javascript
// store/index.js

import { createStore } from 'vuex';

export default createStore({
    state: {},
    mutations: {},
    actions: {},
    modules: {},
});
```

```javascript
// main.js

import { createApp } from 'vue';
import App from './App.vue';
import store from './store';

const app = createApp(App);

app.use(store).mount('#app');
```
:::info
`new Store(...)` 仍可以使用，但建議使用 `createStore`，與 Vue 3 以及 Vue-Router 4.0 保有一致性。
:::

## Vuex In Composition API - useStore

```javascript
// store/index.js

import { createStore } from 'vuex';

export default createStore({
    state: {
        // 計數器 預設 0
        count: 0, 
    },
    mutations: {
        // 執行會使計數器 + 1
        addCount(state) {
          state.count += 1;
        },
    },
    getter: {
        // 返回 計數器 是 偶數 還是 基數
        evenOrOdd: (state) => (state.count % 2 === 0 ? 'even' : 'odd'),
    }
});
```
```javascript
// counter.vue

<template>
    <div class="content">
        <p>{{ count }} is {{ evenOrOdd }}</p>
        /*
          還是可以這樣寫 ！
          <p>{{ $store.state.count }} is {{ evenOrOdd }}</p>
        */ 
        <button type="button" @click="add">add</button> 
    </div>    
</template>

<script>
    import {
      useStore,
    } from 'vuex';

    import {
      computed,
    } from 'vue';

    export default ({
      setup() {
      
        const store = useStore();
        // 建立 store 實體

        const count = computed(() => store.state.count);
        // 使用 computed 取出 state
          
        const evenOrOdd = computed(() => store.getters.evenOrOdd);
        // 使用 computed 取出 getters
        
        const add = () => store.commit('addCount');
        // 執行 store.commit

        return {
          count,
          evenOrOdd,
          add,
        };
      },
    });
</script>

<style scoped>
.content {
  width: 200px;
  height: 200px;
  margin: 0 auto;
  background-color: azure;
  border-radius: 3px;
  box-shadow: rgba(50, 50, 93, 0.25) 0px 6px 12px -2px, rgba(0, 0, 0, 0.3) 0px 3px 7px -3px;
  padding: 10px;
}
</style>

```

>![](https://i.imgur.com/nhamNzH.gif)  &nbsp;  搞定 ！
