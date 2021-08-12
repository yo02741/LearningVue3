# Vue3 Composition API - setup()

######  tags: `Vue3`、`Composition API`、`setup`

&nbsp;

Vue3 Composition API 將一切在 setup 的 scope 裡面全部完成，由於 setup 是在同 Option API beforeCreate 的期間啟動，也因此不再需要也無法在 setup 裡面使用 this 來呼叫 Vue Instance。

起手式： 
```javascript
<script>
export default {
    setup() {
        
    },
}
</script>
```

setup 有兩個 arguments，分別是：

- props

    若要在 setup 取的父層的資料，則需引入 `props`。
- context
    
    context 為一 Object，包含 `attrs`、`slots` 及 `emit`，可使用解構的方式傳入。
    
    - attrs : 父元件有傳遞，但在子元件未通過 props 聲明的值。
    - slots : 接收插槽值。
    - emit : 就是子元件與父元件溝通的途徑，使用方式從 `this.$emit(...) - Vue2 Option API` 改為 `emit(...) - Vue3 Composition API`。
  
  &nbsp;
  
context 解構，帶出內容：
```javascript
<script>
export default {
    setup(props, { emit }) { 
         // 例如 我只需要 emit
    },
}
</script>
```
    
接下來下面幾章會談到 setup 中有哪些 Composition API 的功能可以引用。


