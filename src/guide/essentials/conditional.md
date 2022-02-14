# 条件渲染 {##conditional-rendering}

<script setup>
import { ref } from 'vue'
const awesome = ref(true)
</script>

## `v-if` {#v-if}

`v-if` 指令用于根据条件渲染一个区块，该区块只会在指令的表达式返回真值时才会渲染。

```vue-html
<h1 v-if="awesome">Vue is awesome!</h1>
```

## `v-else` {#v-else}

你可以使用 `v-else` 标记 `v-if` 不满足时的“else 区块”：

```vue-html
<button @click="awesome = !awesome">Toggle</button>

<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

<div class="demo">
  <button @click="awesome = !awesome">Toggle</button>
  <h1 v-if="awesome">Vue is awesome!</h1>
  <h1 v-else>Oh no 😢</h1>
</div>

<div class="composition-api">

[在 Playground 中试试手](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCB7IHJlZiB9IGZyb20gJ3Z1ZSdcblxuY29uc3QgYXdlc29tZSA9IHJlZih0cnVlKVxuPC9zY3JpcHQ+XG5cbjx0ZW1wbGF0ZT5cbiAgPGJ1dHRvbiBAY2xpY2s9XCJhd2Vzb21lID0gIWF3ZXNvbWVcIj50b2dnbGU8L2J1dHRvbj5cblxuXHQ8aDEgdi1pZj1cImF3ZXNvbWVcIj5WdWUgaXMgYXdlc29tZSE8L2gxPlxuXHQ8aDEgdi1lbHNlPk9oIG5vIPCfmKI8L2gxPlxuPC90ZW1wbGF0ZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59In0=)

</div>
<div class="options-api">

[在 Playground 中试试手](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmV4cG9ydCBkZWZhdWx0IHtcbiAgZGF0YSgpIHtcbiAgXHRyZXR1cm4ge1xuXHQgICAgYXdlc29tZTogdHJ1ZVxuICBcdH1cblx0fVxufVxuPC9zY3JpcHQ+XG5cbjx0ZW1wbGF0ZT5cbiAgPGJ1dHRvbiBAY2xpY2s9XCJhd2Vzb21lID0gIWF3ZXNvbWVcIj50b2dnbGU8L2J1dHRvbj5cblxuXHQ8aDEgdi1pZj1cImF3ZXNvbWVcIj5WdWUgaXMgYXdlc29tZSE8L2gxPlxuXHQ8aDEgdi1lbHNlPk9oIG5vIPCfmKI8L2gxPlxuPC90ZW1wbGF0ZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59In0=)

</div>

`v-else` 元素必须紧跟在一个 `v-if` 元素或 `v-else-if` 元素后面，否则就识别不了它。

## `v-else-if` {#v-else-if}

顾名思义，`v-else-if` 提供相应于 `v-if` 的“else if 区块”，它可以链式使用多次：

```vue-html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

和 `v-else` 相似，一个使用 `v-else-if` 的元素必须紧跟在一个 `v-if` 或一个 `v-else-if` 元素后。

## `<template>` 上的 `v-if` {#v-if-on-template}

因为 `v-if` 是一个指令，它必须依附于单一元素上，但如果我们想要切换显示的不只一个元素呢？在这种情况下我们可以在一个 `<template>` 元素上使用 `v-if`，它会创建一个不可见的包裹层，并且最后的渲染结果不会包含这个 `<template>` 元素。

```vue-html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

`v-else` 和 `v-else-if` 也能用在 `<template>` 元素上。

## `v-show` {#v-show}

我们还可以使用 `v-show` 来条件渲染一个元素，用法和 `v-if` 基本一致：

```vue-html
<h1 v-show="ok">Hello!</h1>
```

不同之处在于 `v-show` 始终会在 DOM 渲染并保留该节点；`v-show` 仅切换了该元素的 `display` CSS 属性。

`v-show` 不支持在 `<template>` 元素上使用，也没有 `v-else` 来配合。

## `v-if` vs `v-show` {#v-if-vs-v-show}

`v-if` 是“真正地”按条件渲染，因为它确保了条件区块内的事件监听器和子组件都会在切换时销毁与重建。

`v-if` 同时是**懒加载**的：如果在初次渲染时条件值为 false，则不会做任何事，条件区块会直到条件首次变为 true 时才渲染。

相比之下，`v-show` 简单许多，元素无论一开始条件如何，始终会被渲染，仅作 CSS 类的切换。

一般来说，`v-if` 的切换成本更高，而`v-show` 的首次渲染成本更高。因此当你需要非常频繁切换时，用 `v-show` 会更好，而在运行时不太改变条件的时候 `v-if` 会更合适。

## `v-if` 和 `v-for` {#v-if-with-v-for}

::: warning 注意
**不推荐的**在一个元素上同时使用 `v-if` 和 `v-for`，因为二者没有绝对的优先级，详情见[风格指南](/style-guide/#avoid-v-if-with-v-for-essential)。
:::

当 `v-if` 和 `v-for` 同时存在于一个元素上时，首先执行 `v-if`，详情见[列表渲染指引](list#v-for-with-v-if)。
