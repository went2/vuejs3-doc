# 计算属性 {#computed-properties}

## 基础示例 {#basic-example}

在模板中写表达式虽然方便，但也只能用来做简单的操作。如果在模板中写太多逻辑，会让使其变得臃肿，难以维护。比如说，我们有一个包含了内嵌数组的对象：

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  }
}
```

</div>
<div class="composition-api">

```js
const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})
```

</div>

我们想根据 `author` 是否已有一些书籍来展示不同的信息：

```vue-html
<p>Has published books:</p>
<span>{{ author.books.length > 0 ? 'Yes' : 'No' }}</span>
```

这时的模板已经有了点凌乱的迹象，我们得定睛看才能明白这里有一个基于 `author.books` 属性的计算，更重要的是，如果模板的其他地方也要这个计算，我们可能不想把相同表达式再写一遍。

因此我们推荐使用 **计算属性** 来描述包含响应式状态的复杂逻辑。这是重构后的示例：

<div class="options-api">

```js
export default {
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Advanced Guide',
          'Vue 3 - Basic Guide',
          'Vue 4 - The Mystery'
        ]
      }
    }
  },
  computed: {
    // 一个计算属性的 getter
    publishedBooksMessage() {
      // `this` 指向当前组件实例
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}
```

```vue-html
<p>Has published books:</p>
<span>{{ publishedBooksMessage }}</span>
```

[在 Playground 中试试手](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmV4cG9ydCBkZWZhdWx0IHtcbiAgZGF0YSgpIHtcbiAgICByZXR1cm4ge1xuICAgICAgYXV0aG9yOiB7XG4gICAgICAgIG5hbWU6ICdKb2huIERvZScsXG4gICAgICAgIGJvb2tzOiBbXG4gICAgICAgICAgJ1Z1ZSAyIC0gQWR2YW5jZWQgR3VpZGUnLFxuICAgICAgICAgICdWdWUgMyAtIEJhc2ljIEd1aWRlJyxcbiAgICAgICAgICAnVnVlIDQgLSBUaGUgTXlzdGVyeSdcbiAgICAgICAgXVxuICAgICAgfVxuICAgIH1cbiAgfSxcbiAgY29tcHV0ZWQ6IHtcbiAgICBwdWJsaXNoZWRCb29rc01lc3NhZ2UoKSB7XG4gICAgICByZXR1cm4gdGhpcy5hdXRob3IuYm9va3MubGVuZ3RoID4gMCA/ICdZZXMnIDogJ05vJ1xuICAgIH1cbiAgfVxufVxuPC9zY3JpcHQ+XG5cbjx0ZW1wbGF0ZT5cbiAgPHA+SGFzIHB1Ymxpc2hlZCBib29rczo8L3A+XG4gIDxzcGFuPnt7IGF1dGhvci5ib29rcy5sZW5ndGggPiAwID8gJ1llcycgOiAnTm8nIH19PC9zcGFuPlxuPC90ZW1wbGF0ZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59In0=)

我们定义了一个计算属性 `publishedBooksMessage`。

更改此应用的 `data` 中 `books` 数组的值后，可以看到 `publishedBooksMessage` 也会随之改变。

在模板中使用计算属性的方式和一般的实例属性一样。Vue 会检测到 `this.publishedBooksMessage` 依赖于 `this.author.books`，所以当 `this.author.books` 改变时，任何依赖于 `this.publishedBooksMessage` 的绑定都将同时更新。

计算属性的类型请参考：[为计算属性标记类型 ](/guide/typescript/options-api.html#typing-computed) <sup class="vt-badge ts" />

</div>

<div class="composition-api">

```vue
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// 一个计算属性 ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

[在 Playground 中试试](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCB7IHJlYWN0aXZlLCBjb21wdXRlZCB9IGZyb20gJ3Z1ZSdcblxuY29uc3QgYXV0aG9yID0gcmVhY3RpdmUoe1xuICBuYW1lOiAnSm9obiBEb2UnLFxuICBib29rczogW1xuICAgICdWdWUgMiAtIEFkdmFuY2VkIEd1aWRlJyxcbiAgICAnVnVlIDMgLSBCYXNpYyBHdWlkZScsXG4gICAgJ1Z1ZSA0IC0gVGhlIE15c3RlcnknXG4gIF1cbn0pXG5cbi8vIGEgY29tcHV0ZWQgcmVmXG5jb25zdCBwdWJsaXNoZWRCb29rc01lc3NhZ2UgPSBjb21wdXRlZCgoKSA9PiB7XG4gIHJldHVybiBhdXRob3IuYm9va3MubGVuZ3RoID4gMCA/ICdZZXMnIDogJ05vJ1xufSlcbjwvc2NyaXB0PlxuXG48dGVtcGxhdGU+XG4gIDxwPkhhcyBwdWJsaXNoZWQgYm9va3M6PC9wPlxuICA8c3Bhbj57eyBwdWJsaXNoZWRCb29rc01lc3NhZ2UgfX08L3NwYW4+XG48L3RlbXBsYXRlPiIsImltcG9ydC1tYXAuanNvbiI6IntcbiAgXCJpbXBvcnRzXCI6IHtcbiAgICBcInZ1ZVwiOiBcImh0dHBzOi8vc2ZjLnZ1ZWpzLm9yZy92dWUucnVudGltZS5lc20tYnJvd3Nlci5qc1wiXG4gIH1cbn0ifQ==)

我们定义了一个计算属性 `publishedBooksMessage`。`computed()` 方法接收一个 getter 函数，返回值为一个**计算属性 ref**。就像其他的 ref，你可以通过 `publishedBooksMessage.value` 访问计算结果。计算属性 ref 也会在模板中自动解封，因此在模板表达式中引用时无需添加 `.value`。

计算属性会自动追踪响应式依赖，Vue 会检测到 `publishedBooksMessage` 依赖于 `author.books`，所以当 `author.books` 改变时，任何依赖于 `publishedBooksMessage` 的绑定都会同时更新。

计算属性的类型请参考：[为计算属性标注类型](/guide/typescript/composition-api.html#typing-computed) <sup class="vt-badge ts" />

</div>

## 计算属性缓存 vs 方法 {#computed-caching-vs-methods}

你可能注意到我们在表达式中调用一个函数也会实现和计算属性相同的效果：

```vue-html
<p>{{ calculateBooksMessage() }}</p>
```

<div class="options-api">

```js
// 组件内部
methods: {
  calculateBooksMessage() {
    return this.author.books.length > 0 ? 'Yes' : 'No'
  }
}
```

</div>

<div class="composition-api">

```js
// 组件内部
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Yes' : 'No'
}
```

</div>

我们确实可以将同一个函数定义为方法而不是计算属性，两种方式在结果上也完全相同。但是，不同之处在于 **计算属性值会基于其响应式依赖被缓存**。一个计算属性仅会在其响应式依赖更新时才重新计算，这意味着只要 `author.books` 不改变，无论多少次访问 `publishedBooksMessage` 都会立即返回先前的计算结果，而不用重复执行 getter 函数。

这也意味着下面的计算属性永远不会更新，因为 `Date.now()` 并不是一个响应式依赖<sup>[[1]](#footnote-1)</sup>：

<div class="options-api">

```js
computed: {
  now() {
    return Date.now()
  }
}
```

</div>

<div class="composition-api">

```js
const now = computed(() => Date.now())
```

</div>

相比之下，方法调用**总是**会在重新渲染时再次执行函数。

为什么需要缓存呢？想象我们有一个非常耗性能的计算属性 `list`，需要循环一个巨量数组再做许多计算，并且可能也有其他计算属性依赖于 `list`。没有缓存的话，我们会重复执行非常多次 `list` 的计算函数，然而这实际上没有必要！如果你确定不需要缓存，那就使用方法调用。

<small>
<div id="footnote-1">[1] Data.now() 返回 UNIX 纪元开始到当前时间经过了多少毫秒，计算一次后就不再改变</div>
</small>

## 可写计算属性 {#writable-computed}

计算属性默认仅能通过计算函数得出结果，如果你尝试给一个计算属性赋值，会收到一个运行时警告。只在某些特殊场景中你可能才需要用到“可写”的计算属性，你可以通过同时提供 getter 和 setter 来创建：

<div class="options-api">

```js
export default {
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: {
      // getter
      get() {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set(newValue) {
        // 注意：我们这里使用的是解构赋值语法
        [this.firstName, this.lastName] = newValue.split(' ')
      }
    }
  }
}
```

现在当你运行 `this.fullName = 'John Doe'` 时，setter 会被调用而 `this.firstName` 和 `this.lastName` 会随之更新。

</div>

<div class="composition-api">

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // 注意：我们这里使用的是解构赋值语法
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

现在当你运行 `fullName.value = 'John Doe'` 时，setter 会被调用而 `firstName` 和 `lastName` 会随之更新。

</div>

## 最佳实践 {#best-practices}

### 计算函数不应有副作用 {#getters-should-be-side-effect-free}

计算属性的 getter 函数应只做计算而没有任何其他的副作用，这一点非常重要，请务必牢记。举个例子，不要在计算函数中做异步请求或者更改 DOM！一个计算属性相当于宣告如何根据其他值派生出一个值，因此计算函数的职责应该仅为计算和返回该值。在之后的指南中我们会讨论如何使用 [watchers](./watchers) 根据其他响应式状态的变更来执行副作用。

### 避免直接修改计算属性值 {#avoid-mutating-computed-value}

从计算属性返回的值是派生出来的状态，把它看作是一个“临时快照”，每当源状态发生变化时，就会创建一个新的快照，更改快照是没有意义的。应该将计算属性的返回值视为只读的，不要直接修改它。要改的话，应该修改它所依赖的源状态，以触发一次新的计算。

## 小结

- 本节提到的概念

```
计算属性
├─ 基础示例
├─ 计算属性缓存 vs 方法
├─ 可写计算属性
├─ 最佳实践
│  ├─ 计算函数不应有副作用
│  └─ 计算函数不应有副作用
```
