# LifeCycle

######  tags: `Vue3`、`LifeCycle`、`Hook functions`

&nbsp;

我們先來看看 Vue3 的 Lifecycle Diagram：

![](https://v3.vuejs.org/images/lifecycle.svg)

==Vue3 Option API== 的 Hooks function 與 Vue2 大同小異，異動的地方只有 `beforeDestroy / destroyed (Vue2)` 改為 `beforeUnmount / unmounted (Vue3)`。

==Vue3 Composition API== 的 Hooks function 與 Option API 對比，除了名稱不同之外，
Composition API 並沒有 keep Alive 的 Hooks - `activated / deactivated` 可以使用。



| Vue 2 OptionAPI | Vue 3 OptionAPI | Vue 3 Composition API |
| -------- | -------- | -------- |
| beforeCreate     | beforeCreate     | setup()     |
| created     | created    | setup()     |
| beforeMount     | beforeMount    | onBeforeMount     |
| mounted     | mounted    | onMounted     |
| beforeUpdate     | beforeUpdate    | onBeforeUpdate     |
| updated     | updated    | onUpdated     |
| beforeDestroy     | beforeUnmount    | onBeforeUnmount     |
| destroyed     | unmounted    | onUnmounted     |
| errorCaptured | errorCaptured | onErrorCaptured |

---

Else：

[Vue LifeCycle 狀態與事件建立](https://hackmd.io/tN1loH0BSLOOu3Y0jFjraw)