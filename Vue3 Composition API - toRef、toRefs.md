# Vue3 Composition API - toRef、toRefs

######  tags: `Vue3`、`Composition API`、`setup`、`toRef`、`toRefs`

&nbsp;

在講解 `toRef` 及 `toRefs`之前，我們先來看看以下範例：

```javascript
<template>
  <div>
    目前 工號 : {{ userData.id }} <br>
    目前 名字 : {{ userData.name }} <br>
    目前 分機  : {{ userData.work.extensionNumber  }}
    <hr>
    <button @click="userData.id = 12345">改 id</button><hr>
    <button @click="userData.name = 'chenyo'">改名字</button><hr>
    <button @click="userData.work.extensionNumber = 57890">改分機</button>
  </div>
</template>

<script>
import {
  reactive,
} from 'vue';

export default {
  name: 'App',
  setup() {

    const userData = reactive({
      id: 13768,
      name: '小陳',
      work: {
        unit: 'IT',
        extensionNumber: 58195,
        position: 'Front-end Engineer',
      }
    })

    return{
      userData,
    }
  }
}
</script>
```
操作情境如下：

![](https://i.imgur.com/uFxdU2e.gif)


這邊可以發現我們將所有的功能都包在 `reactive` 裡面，這邊也將 handleNumArr return 回去給模板 render，這邊一切看似都很美好很簡潔，直到我們在寫模板時：

```javascript
<template>
  <div>
    目前 工號 : {{ userData.id }} <br>
    目前 名字 : {{ userData.name }} <br>
    目前 分機  : {{ userData.work.extensionNumber  }}
    <hr>
    <button @click="userData.id = 12345">改 id</button><hr>
    <button @click="userData.name = 'chenyo'">改名字</button><hr>
    <button @click="userData.work.extensionNumber = 57890">改分機</button>
  </div>
</template>
```
這邊可以看到非常多的 userData，思考了一下，那我如果在 return 的時候，單獨回傳 `id`、`name` 及 `extensionNumber`，這樣模板就變成期盼的樣子了！

以下是我預期改完的樣子：
```javascript
<template>
  <div>
    目前 工號 : {{ id }} <br>
    目前 名字 : {{ name }} <br>
    目前 分機  : {{ extensionNumber  }}
    <hr>
    <button @click="id = 12345">改 id</button><hr>
    <button @click="name = 'chenyo'">改名字</button><hr>
    <button @click="extensionNumber = 57890">改分機</button>
  </div>
</template>
```

那從這邊反推回去，我必須要做到以下：
```javascript
// setup() scope

return{
  id,
  name,
  extensionNumber
}

```
但是這邊很明顯的，我們並沒有定義這些變數，只是 userData 有這些 property 而已。
於是我腦筋一轉，幹了以下一件大事：
```javascript
return{
  id: userData.id,
  name : userData.name,
  extensionNumber: userData.work.extensionNumber,
}
```
這樣子我們 return 的參數都有值了，我們再來看看畫面的呈現：
![](https://i.imgur.com/szVhEgw.png)
看起來一切正常呢！
但這時候操作一下，會發現我們三個按鈕綁定的功能都壞掉了：
![](https://i.imgur.com/hehRQTU.gif)

為什麼會這樣勒 ... 

其實我們之前 return 的 userData 是 `Proxy` 的格式，是一個雙向綁定的 `RefImpl`，而當我們今天用上方定義的方式 return，回傳的值只是死的 value 而已，並不是一個具備 reactivity 的 RefImpl：
```javascript
return{
  id: userData.id,
  name : userData.name,
  extensionNumber: userData.work.extensionNumber,
}

// 上方程式 編譯過後等同於 下方程式

return{
  id: 13768,
  name : '小陳',
  extensionNumber: 58195,
}
```

那我們該如何同時做到在模板很精簡，且 return 值是有 reactivity 性質的 RefImpl 呢？

在解答之前，我們先來看看 `toRef`。

## toRef

`toRef`可以針對響應式物件的某個 property 產生一個與其鍊結的 ref。

```javascript
const userData = reactive({
  id: 13768,
  name: '小陳',
  work: {
    unit: 'IT',
    extensionNumber: 58195,
    position: 'Front-end Engineer',
  }
})

const idRef = toRef(userData, 'id')
const nameRef = toRef(userData, 'name')
const extensionNumberRef = toRef(userData.work, 'extensionNumber')
```

這時候這三個 ref 都 binding 著指定的 property，換言之，我異動 `idRef` 時， `userData.id` 也會同步變動；同理，我異動 `userData.id` 時， `idRef` 也會同步變動。

了解了 `toRef` 的用法後，我們可以把原本的程式改為以下：

```javascript
<template>
  <div>
    目前 工號 : {{ id }} <br>
    目前 名字 : {{ name }} <br>
    目前 分機  : {{ extensionNumber  }}
    <hr>
    <button @click="id = 12345">改 id</button><hr>
    <button @click="name = 'chenyo'">改名字</button><hr>
    <button @click="extensionNumber = 57890">改分機</button>
  </div>
</template>

<script>
import {
  reactive, toRef,
} from 'vue';

export default {
  name: 'App',
  setup() {

    const userData = reactive({
      id: 13768,
      name: '小陳',
      work: {
        unit: 'IT',
        extensionNumber: 58195,
        position: 'Front-end Engineer',
      }
    })

    return{
      id: toRef(userData, 'id'),
      name: toRef(userData, 'name'),
      extensionNumber: toRef(userData.work, 'extensionNumber'),
    }
  }
}
</script>
```

改動完後可以發現模板變得很精簡，功能也都正常運行，資料的確有綁定著原本的 property，但這樣看下來，return 的部份重複性有點高，若是我的 property 有 100 個難不成我要設定 100 行？

這時候我們就可以使用 `toRefs` ！

## toRefs

`toRefs`可以針對一響應式物件的所有 property 批次轉換成 ref 並 return。

```javascript
const userData = reactive({
  id: 13768,
  name: '小陳',
  work: {
    unit: 'IT',
    extensionNumber: 58195,
    position: 'Front-end Engineer',
  }
})

const userDataRef = toRefs(userData)
```
![](https://i.imgur.com/KdGMTKg.png)

可以看到 `toRefs` 就是批次地做 `toRef` 做的事情並包裝成物件。

這時候我們將程式碼改為以下：

```javascript
<template>
  <div>
    目前 工號 : {{ id }} <br>
    目前 名字 : {{ name }} <br>
    目前 分機  : {{ work.extensionNumber  }}
    <hr>
    <button @click="id = 12345">改 id</button><hr>
    <button @click="name = 'chenyo'">改名字</button><hr>
    <button @click="work.extensionNumber = 57890">改分機</button>
  </div>
</template>

<script>
import {
  reactive, toRef, toRefs
} from 'vue';

export default {
  name: 'App',
  setup() {

    const userData = reactive({
      id: 13768,
      name: '小陳',
      work: {
        unit: 'IT',
        extensionNumber: 58195,
        position: 'Front-end Engineer',
      }
    })

    return{
      ...toRefs(userData)
    }
  }
}
</script>
```

這邊注意以下幾點：
1. toRefs 出來是一個物件，我們需要將其解構後放進 return 的 object 中，若是直接在 return 中塞入 `toRefs(userData)`，會有語法錯誤的問題。
2. toRefs 出來的 property 為第一層物件們 `id`、`name` 及 `work`，因此在模板中放置 extensionNumber 需使用 `work.extensionNumber`。

這邊看到模板跟 return，是不是相當的精簡呀！

至於該用 `toRef` 或 `toRefs`呢？ 這取決於對象是不是物件，以及返回值得數量，一個以上就可以使用 `toRefs` 了，程式碼也較為精簡。

&nbsp;

但這邊就要特別注意命名了，若是解構完後名稱相同的話，後者會取代前者的值喔！

```javascript
setup() {
    const userData = reactive({
      id: 13768,
      name: '小陳',
      work: {
        unit: 'IT',
        extensionNumber: 58195,
        position: 'Front-end Engineer',
      }
    })

    const companyData = reactive({
      name: 'tung',
      locate: {
        country: 'Taiwan',
        city: 'Taichung'
      }
    })

    return{
      ...toRefs(userData),
      ...toRefs(companyData),
    }
}

// 這時候在模板顯示 {{ name }}，值為 companyData.name => tung
```