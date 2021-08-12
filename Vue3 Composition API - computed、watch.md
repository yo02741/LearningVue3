# Vue3 Composition API - computed、watch

######  tags: `Vue3`、`Composition API`、`setup`、`computed`、`watch`、`watchEffect`

&nbsp;

##   Computed

首先我們看到 `computed`，有兩種模式：`read-only` 與 `writable`。

#### > read-only

呼叫後會直接 return 值，若是異動此變數會報錯。

```javascript
const num = ref(10)
const doubleNum = computed(() => num.value * 2)

onMounted(() => {
  console.log(doubleNum.value); // 20


  doubleNum.value = 60
  // error - Write operation failed: computed value is readonly
})
```
    
#### > writable

從下例可以看到：
1. 若是直接呼叫這個 writable 的 computed，會觸發 `get`。
2. 若是對這個 writable 的 computed 賦值 / 異動透過 v-model 綁定 doubleNum 的 input 值，則會觸發 `set`。
&nbsp;
    
```javascript
<template>
  <div>
    <input type="number" v-model="num">
    <input type="number" v-model="doubleNum">
  </div>
</template>

<script>
import {
  ref,
  computed,
  onMounted
} from 'vue';

export default {
  name: 'App',
  setup() {
    const num = ref(10)
    const doubleNum = computed({
      get: () => num.value * 2,
      set: val => num.value = val / 2
    })

    onMounted(() => {
      console.log(doubleNum.value); // get - 20
      console.log(doubleNum.value = 50); // set - 50 & num became 25
    })

    return {
      num,
      doubleNum
    }
  },
}
</script>
```

##   watchEffect

watchEffect 會在 setup 時自動調用，並在依賴項目有異動時再次呼叫。
對於下面例子來說，依賴項目為 `num.value` 及 `doubleNum.value`

```javascript
setup() {
    const num = ref(1)
    const doubleNum = computed(() => num.value * 2)
    
    watchEffect(() => {
        console.log('num', num.value, '/ doubleNum', doubleNum.value )
    })
    
    onMounted(() => {
        
        // log -> num 1 / doubleNum 2
    
        
        setTimeout(() => {
        num.value = 10
        }, 3000); // after 3s: log -> num 10 / doubleNum 20
    })
    
    return {
      num,
      doubleNum,
    }
  },
```

##   watch

watch 可以針對`一至多`個變數監控，當有異動時會執行 callback。

&nbsp;

<!-- 若是 watch 的對象是巢狀物件也可以另外設定監控深層 ( option: deep )，也可以設定是否立即執行 ( option: immediate )。
 -->
 
監控單個 source ，若有異動則呼叫 callback。
```javascript
watch(num, () => console.log('num updated!'))

// when num 0 => 1,  log num updated!
```

也可以監控多個 source 。
```javascript
watch([num, str], () => console.log('num | str updated!'))
```

可以檢視單個 source 的新舊資料。
```javascript
watch(num, (newValue, oldValue) => console.log(newValue, oldValue))

// when num 0 => 1,  log 1, 0
```

也可以檢視多個 source 的新舊資料。
```javascript
watch([num, str], (newValue, oldValue) => console.log(newValue, oldValue))

// when (str '' => 'hello' && num still 0),  log [0, 'hello'], [0, '']
```

若監控對象為物件的 property，則需用 return 的方式。
```javascript
const userData = reactive({
      id: '',
      name: '',
    })

watch(
  () => userData.name,
  (newValue, oldValue) => console.log(newValue, oldValue))
  
// when userData.name '' => 'chenyo',  log 'chenyo', '' 
```

若要此 watch 在 setup 時就立即觸發，可以透過 `immediate`做到。
```javascript
const userData = reactive({
      id: '',
      name: '',
    })

watch(
      () => userData.name,
      () => console.log('name updated!'),
      { immediate: true })
  
// when setup, log 'name updated!'
```

若要監控整個物件，有以下兩種方法：
```javascript
const userData = reactive({
  id: '',
  name: '',
  creditCard: {
    no: '',
    cvv: '',
  }
})

watch(
  userData,
  () => console.log('userData update'),
)

// OR

watch(
  () => userData,
  () => console.log('userData update'),
  { deep: true }
)
```

若要監控物件中的 property：
```javascript
const userData = reactive({
  id: '',
  name: '',
  creditCard: {
    no: '',
    cvv: '',
  }
})

watch(
  () => userData.creditCard.cvv,
  () => console.log('userData.creditCard.cvv update'),
)
```

若要監控巢狀物件時要查看 新舊值，必須使用 deepClone：
```javascript
const userData = reactive({
  id: '',
  name: '',
  creditCard: {
    no: '',
    cvv: '',
  }
})

watch(
  () => JSON.parse(JSON.stringify(userData)),
  (data, prevData) => console.log(data, prevData),
  { deep: true }
)
```

若要監控陣列：
```javascript
const numbers = reactive([1, 2, 3, 4])

watch(
  () => [...numbers],
  (numbers, prevNumbers) => {
    console.log(numbers, prevNumbers)
  }
)
```