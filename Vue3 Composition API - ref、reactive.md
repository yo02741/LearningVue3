# Vue3 Composition API - ref、reactive

######  tags: `Vue3`、`Composition API`、`setup`、`ref`、`reactive`

&nbsp;

在 Composition API 中，我們可以透過 `ref` 及 `reactive` 來設定變量，那我們來看看他們有什麼差異：

## ref
- 可以存放各種型態
```javascript
const ref1 = ref(0)  // number
const ref2 = ref('string')  // string
const ref3 = ref(true)  // boolean
const ref4 = ref(null)  // null
const ref5 = ref(undefined)  // undefined
const ref6 = ref([])  // Object
const ref7 = ref({})  // Object - Array

// All Good !
```
&nbsp;
- 取值需透過 `.value`
```javascript
const ref1 = ref(0)

onMounted(() => {
    console.log(ref1) 
    // RefImpl {_shallow: false, __v_isRef: true, _rawValue: 0, _value: 0}

    console.log(ref1.value)
    // 0
})
```
&nbsp;
- reassign 不會踩雷
```javascript
const ref6 = ref([])
onMounted(() => {
    ref1.value = ['1', '2', '3']  // OK!
})
```
    
## reactive
- 只能存放物件型態
```javascript
const ref1 = reactive(0)  // number
const ref2 = reactive('string')  // string
const ref3 = reactive(true)  // boolean
const ref4 = reactive(null)  // null
const ref5 = reactive(undefined)  // undefined
const ref6 = reactive([])  // Object
const ref7 = reactive({})  // Object - Array

// Only ref6 && ref7 PASS!
```

&nbsp;
- 取值 不需 透過 `.value`
```javascript
const ref6 = reactive([])
const ref7 = reactive({})

onMounted(() => {
    console.log(ref6); // Proxy Object
    console.log(ref7); // Proxy Object
})
```
&nbsp;
- reassign 

    - 由於我們：
    &nbsp; 
    1. 用 const 定義 ref6
    2. reactive 取值並不需要透過 `.value`
    3. 也無法使用 let 定義並直接 assign 一個新陣列，這會導致失去的 reactivity 的特性。
&nbsp;
    ```javascript
    const ref6 = reactive([])
    onMounted(() => {
        ref1 = ['1', '2', '3'] // TypeError: "ref6" is read-only
    })
    ```
    - 因此我們：
    &nbsp;
    1. 可以的話，直接對原陣列處理，例如： `<array>.length = 0` 、` <array>.push()` ... 之類的操作。
    2. 若真的需要重新 assign 一個全新的陣列時，請先將陣列清空，再使用 `Object.assign` 的方式來重新賦值，因為這種方法會看位子，如下：
    
    &nbsp;

    ```javascript
    const ref6 = reactive(['1', '2', '3'])

    onMounted(() => {
        Object.assign(ref6, ['4']) // ref6： ['4', '2', '3']
    })

    // 以上 這不是我們要的 所以我們要先清空再使用 Object.assign
    ```
    ```javascript
    const ref6 = reactive(['1', '2', '3'])
    onMounted(() => {
        ref6.length = 0
        Object.assign(ref6, ['4']) // ref6： ['4']
    })
    ```

    :::info
    使用情境：定義一個 array 叫 list，提取後端資料後，將資料 assign 進 list；當後續取完新的資料時，可以先將 array 清空再使用 Object.assign 重新賦值。
    :::
    
    

    講完 Array，來講講 Object：

    我們在 Vue2 Option API 的 data 中定義 Object 時，通常都會事先定義好他的 key，
    如下：

    ```javascript
    const userData = reactive({
      id: '',
      name: '',
      position: ''
    })

    onMounted(() => {
        Object.assign(userData, {
          id: '123',
          name: 'chenyo',
          position: 'frontend'
        })
    })
    ```

    那在針對 Object 使用 `Object.assign` 時，也是要注意你的新物件的 key 是不是有與原物件的 key相吻合。

    :::info
    使用情境：當我事先定義一個 object 來存放 ( e.g. 當前的 threshold，資料包含 tpr、fpr、threshold )，後續可以使用 `Object.assign` 來重新對這個 object 賦值。
    :::

---

回顧： 

1. 用 ref 定義全部，萬無一失，但寫邏輯的時候會寫出一堆 `.value` 。
2. 用 reactive 包物件，在撰寫邏輯會更為方便，精簡很多，同時程式的可讀性也更優。


筆者結論：

1. Object / Array 用 reactive 包，其他用 ref 包。
2. 也可以考慮將同個 scope 的變數 包在一個 reactive，如下範例：

這邊設計一個 handleNumArr 的 reactive，內容有：
- num: string
    - 用來綁定要存去陣列的數字
- list: object - array
    - 用來存放輸入的數字的陣列
- store: function
    - 儲存數字的函式
- clear: function
    - 清除陣列的函式
- fetch: function
    - 提取<遠端資料>並存至陣列的函式    
&nbsp;
```javascript
<template>
  <div>
    <pre>{{ handleNumArr.list }}</pre>
    <input type="text" v-model="handleNumArr.num"><br>
    <button @click="handleNumArr.store">1. store</button>
    <button @click="handleNumArr.clear">2. clear</button>
    <button @click="handleNumArr.fetch">3. fetch</button>
  </div>
</template>

<script>
import {
  reactive,
} from 'vue';

export default {
  name: 'App',
  setup() {
    const handleNumArr = reactive({
      num: '',
      list: [],
      store: function() {
        this.list.push(this.num)
        this.num = ''
      },
      clear: function() {
        this.list.length = 0
      },
      fetch: function() {
        this.list = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10']
      },
    })

    return{
      handleNumArr,
    }
  }
}
</script>
```
結果如下:

![](https://i.imgur.com/9CWYsHD.gif)

:::info
1. 上例的 this 為父層 handleNumArr，並不是要找 Vue Instance 喔！
2. 若使用 ES6 Arrow Function 並使用 this，這樣並不會指向父層喔！
3. 要渲染至畫面的變數及相關的函式需要在 setup 中透過 return 返回喔！
:::