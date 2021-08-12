# Vue 3 起手式

######  Tags: `Vue3`、`CDN`、`Composition API`

&nbsp;

## CDN
首先先來看到使用 `CDN` 的方式引入 Vue 3： 


透過 CDN 引入 Vue 3 ( Line 14 )，若要使用 Vue 的 Methods ( e.g. creatApp、ref、reactive )，都需要在前面加上 Vue。

```javascript=
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        你點了 <button @click="add">{{ count }}</button> 次
    </div>
    
    <script src="https://unpkg.com/vue@next"></script>
    <script>
        const app = Vue.createApp({
            setup() {
                const count = Vue.ref(0)
                const add = () => {
                    count.value += 1
                }

                return {
                    count,
                    add,
                }
            },
        })
        app.mount('#app')
    </script>
</body>
</html>
```

若你不想要在一直加 `Vue`，可以這麼做：
```javascript=
<script src="https://unpkg.com/vue@next"></script>
<script>
    const { createApp, ref } = Vue
    const app = createApp({
        setup() {
            const count = ref(0)
            const add = () => {
                count.value += 1
            }

            return {
                count,
                add,
            }
        },
    })
    app.mount('#app')
</script>
```

---

## ESM

再來看到使用 `ESM` 的方式引入 Vue 3： 

這邊注意 ESM 引入的方式，我們要先將 script 的 type 設定為 `module`，這樣我們就可以使用 import 的方式引入需要的功能了。

Vue 來源記得要選擇 ESM 版本的，與上方 CDN 引入的不同喔，詳情請見 [Vue Installation](https://v3.vuejs.org/guide/installation.html#from-cdn-or-without-a-bundler)！

```javascript=
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <div>
            你已經點擊 <button @click="counter += 1">{{ counter }}</button> 次
        </div>
    </div>

    <script type="module">
        import { 
            createApp
        } from 'https://cdnjs.cloudflare.com/ajax/libs/vue/3.1.4/vue.esm-browser.min.js';

        const app = createApp({
            data() {
                return {
                    counter: 0    
                }
            }
        })
        app.mount('#app')
    </script>
</body>
</html>
```

---

## Vue CLI

若是使用 SFC （ Single File Components ）來撰寫 Vue，在 `.vue` 檔中的 script 內 `import { 你要引入的 Methods } from 'vue'` 即可，同樣 Methods 前不需要加 Vue。