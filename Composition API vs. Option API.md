# Composition API vs. Option API

######  tags: `Vue3`、`Composition API`

&nbsp;

先來看看 Vue 2 的 Option API 
```javascript
// Option API

<script>
export default {
    data() {},
    computed: {},
    watch: {},
    methods: {},
    mounted() {},
    .
    .
    .
};
</script>
```

雖然分類很明確，各司其職，但是往往我們的 Component 中會有不同面向的功能，這時候就會導致以下情形發生：

> ![](https://i.imgur.com/DcJo9JK.png)


> ![](https://i.imgur.com/10jPI7l.png)

這邊可以看到，不同的功能群組散落在 data 及 methods 裡，這造成了檢閱程式碼時的不容易。

那在 Composition API 中，我們在 `setup` 期間，將同一個功能群的 data、methods、computed 及 watch 定義在一起，這樣子不論是在撰寫程式，又或者是在 Code Review 時，都非常的易讀呢。

> ![](https://i.imgur.com/WW76Pf3.png)

那後面的文章會在講到我們要怎麼從 Option API 移植到 Composition API。


