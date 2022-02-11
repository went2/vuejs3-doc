---
outline: deep
---

# 响应式基础 {#reactivity-fundamentals}

:::tip API 偏好
本页及指南后续章节页面的示例代码分别包含了选项式 API 和组合式 API 两种版本。你当前选择的是 <span class="options-api">选项式 API</span><span class="composition-api">组合式 API</span>。你可以点击页面左侧边栏顶部的 “API 偏好” 开关来切换两种 API 风格。
:::

## 声明响应式状态 {#declaring-reactive-state}

<div class="options-api">

在选项式 API 中，我们用 `data` 这个选项声明组件的响应式状态，选项的值是返回一个对象的函数。在创建一个新的组件实例时，Vue 会调用该函数，将返回的对象纳入其响应式系统，对象的顶层属性会直接暴露到组件实例（方法和生命周期钩子中的 `this`）上：

```js{2-6}
export default {
  data() {
    return {
      count: 1
    }
  },

  // `mounted` 是生命周期钩子，后面进行解释
  mounted() {
    // `this` 指向当前组件实例
    console.log(this.count) // => 1

    // 数据属性也可以修改
    this.count = 2
  }
}
```

[在 Playground 中试试手](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmV4cG9ydCBkZWZhdWx0IHtcbiAgZGF0YSgpIHtcbiAgICByZXR1cm4ge1xuICAgICAgY291bnQ6IDFcbiAgICB9XG4gIH0sXG5cbiAgLy8gYG1vdW50ZWRgIGlzIGEgbGlmZWN5Y2xlIGhvb2sgd2hpY2ggd2Ugd2lsbCBleHBsYWluIGxhdGVyXG4gIG1vdW50ZWQoKSB7XG4gICAgLy8gYHRoaXNgIHJlZmVycyB0byB0aGUgY29tcG9uZW50IGluc3RhbmNlLlxuICAgIGNvbnNvbGUubG9nKHRoaXMuY291bnQpIC8vID0+IDFcblxuICAgIC8vIGRhdGEgY2FuIGJlIG11dGF0ZWQgYXMgd2VsbFxuICAgIHRoaXMuY291bnQgPSAyXG4gIH1cbn1cbjwvc2NyaXB0PlxuXG48dGVtcGxhdGU+XG4gIENvdW50IGlzOiB7eyBjb3VudCB9fVxuPC90ZW1wbGF0ZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59In0=)


这些实例上的属性仅在实例首次创建时添加，因此你需要确保它们都出现在 `data` 函数返回的对象上，若所需的值还未准备好，必要时也可以使用 `null`、`undefined` 等其他值进行占位。

你也可以把新属性直接添加到 `this` 上而不是 `data` 内，但这样添加的属性不会触发响应式更新。

Vue 在组件实例上暴露其内置 API 一般使用 `$` 作为前缀，同时也保留字符 `_` 用作内部属性的前缀，因此不要以这些字符开头来命名 `data` 顶层的属性。

### 响应式代理 vs. 原始对象 {#reactive-proxy-vs-original} \*

Vue3 借助 [JavaScript Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 实现响应式数据。熟悉 Vue2 的开发者需要注意以下边界情况：

```js
export default {
  data() {
    return {
      someObject: {}
    }
  },
  mounted() {
    const newObject = {}
    this.someObject = newObject

    console.log(newObject === this.someObject) // false
  }
}
```

给 `this.someObject` 赋值后再访问它的值，得到的值是原始 `newObject` 对象的响应式代理。 **和 Vue2 不同的是，原始的 `newObject` 会原封不动地存在那里，不会变成响应式：确保始终通过 `this.` 来访问响应式状态。**

</div>

<div class="composition-api">

我们用 [`reactive()`](/api/reactivity-core.html#reactive) 函数创建一个响应式对象或数组：

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

响应式对象是 [JavaScript Proxy 对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，它具有普通对象一样的行为，此外， Vue 会追踪对响应式对象的属性进行访问与修改的操作。如果你好奇其中的细节，我们在 [深入响应式系统](/guide/extras/reactivity-in-depth.html) 一章中会进行解释，但我们推荐你先读完这里的主要指引。

也可参考: [为响应式对象标注类型 Typing Reactive](/guide/typescript/composition-api.html#typing-reactive) <sup class="vt-badge ts" />

如果要在组件模板中使用一个响应式状态，在 `setup()` 函数中定义并返回它：

```js{5,9-11}
import { reactive } from 'vue'

export default {
  // `setup` 是一个专门用于组合式 API 的特殊钩子
  setup() {
    const state = reactive({ count: 0 })

    // 将 state 对象暴露给模板使用
    return {
      state
    }
  }
}
```

```vue-html
<div>{{ state.count }}</div>
```

与定义状态类似，我们也可以在这个作用域中声明能够修改响应式状态的函数，将它作为方法与状态一并暴露出去：

```js{7-9,14}
import { reactive } from 'vue'

export default {
  setup() {
    const state = reactive({ count: 0 })

    function increment() {
      state.count++
    }

    // 不要忘了把函数也暴露出去
    return {
      state,
      increment
    }
  }
}
```

暴露出来的方法通常用作事件监听器：

```vue-html
<button @click="increment">
  {{ state.count }}
</button>
```

### `<script setup>` \*\*

在 `setup()` 函数中手动暴露状态和方法比较繁琐，好在你可以通过使用构建工具简化该操作。当使用单文件组件（SFC）时，可以使用 `<script setup>` 来简化大量样板代码：

```vue
<script setup>
import { reactive } from 'vue'

const state = reactive({ count: 0 })

function increment() {
  state.count++
}
</script>

<template>
  <button @click="increment">
    {{ state.count }}
  </button>
</template>
```

[在 Playground 中试试](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCB7IHJlYWN0aXZlIH0gZnJvbSAndnVlJ1xuXG5jb25zdCBzdGF0ZSA9IHJlYWN0aXZlKHsgY291bnQ6IDAgfSlcblxuZnVuY3Rpb24gaW5jcmVtZW50KCkge1xuICBzdGF0ZS5jb3VudCsrXG59XG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8YnV0dG9uIEBjbGljaz1cImluY3JlbWVudFwiPlxuICAgIHt7IHN0YXRlLmNvdW50IH19XG4gIDwvYnV0dG9uPlxuPC90ZW1wbGF0ZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59In0=)

在 `<script setup>` 标签内导入和声明的顶层（top-level）对象可以直接用在同组件的模板中。

> 指南后续部分的组合式 API 示例代码都会使用单文件组件 + `<script setup>` 的语法，大多数 Vue 开发者也这么用。

</div>

<div class="options-api">

## 声明方法 \* {#declaring-methods}

要为组件实例添加方法，我们使用 `methods` 选项，它是一个对象，包含所有需要的方法：

```js{7-11}
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    // 其他方法或生命周期函数中也可以调用方法
    this.increment()
  }
}
```

Vue 自动为 `methods` 中的方法绑定了永远指向组件实例的 `this`。这确保了方法在作为事件监听器或回调函数时始终保持正确的 `this`。你不应该在定义 `methods` 时使用箭头函数，因为这会让 Vue 无法自动绑定 `this` 值：

```js
export default {
  methods: {
    increment: () => {
      // 错误: 这里访问不到 `this`
    }
  }
}
```

和组件实例上的其他属性一样，在模板中也可以访问到 `methods` 内的方法，在模板中它们常常用于事件监听器：

```vue-html
<button @click="increment">{{ count }}</button>
```

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmV4cG9ydCBkZWZhdWx0IHtcbiAgZGF0YSgpIHtcbiAgICByZXR1cm4ge1xuICAgICAgY291bnQ6IDBcbiAgICB9XG4gIH0sXG4gIG1ldGhvZHM6IHtcbiAgICBpbmNyZW1lbnQoKSB7XG4gICAgICB0aGlzLmNvdW50KytcbiAgICB9XG4gIH0sXG4gIG1vdW50ZWQoKSB7XG4gICAgdGhpcy5pbmNyZW1lbnQoKVxuICB9XG59XG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8YnV0dG9uIEBjbGljaz1cImluY3JlbWVudFwiPnt7IGNvdW50IH19PC9idXR0b24+XG48L3RlbXBsYXRlPiIsImltcG9ydC1tYXAuanNvbiI6IntcbiAgXCJpbXBvcnRzXCI6IHtcbiAgICBcInZ1ZVwiOiBcImh0dHBzOi8vc2ZjLnZ1ZWpzLm9yZy92dWUucnVudGltZS5lc20tYnJvd3Nlci5qc1wiXG4gIH1cbn0ifQ==)

上述示例中点击 `<button>` 后会调用 `increment` 方法。

</div>

### DOM 更新时机 {#dom-update-timing} 

当你更改响应式状态后，DOM 会自动更新。但是注意，DOM 不以同步方式进行更新，Vue 会将它们推入更新循环的 “next tick” 来执行，以确保无论改变了多少次状态，每个需要更新的组件都只更新一次。

若要等待一个状态改变后的 DOM 更新完成，可以使用 [nextTick()](/api/general.html#nexttick) 这个全局 API：

<div class="composition-api">

```js
import { nextTick } from 'vue'

function increment() {
  count.value++
  nextTick(() => {
    // access updated DOM
  })
}
```

</div>
<div class="options-api">

```js
import { nextTick } from 'vue'

export default {
  methods: {
    increment() {
      this.count++
      nextTick(() => {
        // access updated DOM
      })
    }
  }
}
```

</div>

### 深层响应性 {#deep-reactivity}

在 Vue 中，状态都是默认深层响应式的。这意味着即使你更改嵌套的深层对象或数组，Vue 也能检测到更改：

<div class="options-api">

```js
export default {
  data() {
    return {
      obj: {
        nested: { count: 0 },
        arr: ['foo', 'bar']
      }
    }
  },
  methods: {
    mutateDeeply() {
      // 这些改动都会被检测到
      this.obj.nested.count++
      this.obj.arr.push('baz')
    }
  }
}
```

</div>

<div class="composition-api">

```js
import { reactive } from 'vue'

const obj = reactive({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // 这些改动都会被检测到
  obj.nested.count++
  obj.arr.push('baz')
}
```

</div>

也可以创建一个 [浅层响应式对象](/api/reactivity-advanced.html#shallowreactive)，仅限它们的顶层属性具有响应性，一般仅在某些特殊场景中使用。

<div class="composition-api">

### 响应式代理 vs. 原始对象 \*\* {#reactive-proxy-vs-original}

要注意，`reactive()` 返回的是一个原始对象的 [Proxy 对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，它不等于原对象：

```js
const raw = {}
const proxy = reactive(raw)

// proxy 对象不等于原对象
console.log(proxy === raw) // false
```

只有代理对象才具有响应性，修改原始对象不会触发 DOM 更新，因此，使用 Vue 响应式系统的最佳实践是 **只使用代理对象作为状态**。

为保证访问代理的一致性，对同一个对象调用 `reactive()` 会总是返回同样的代理，而对代理调用 `reactive()` 则会返回它自己：

```js
// 在同一个对象上调用 reactive() 会返回相同的代理
console.log(reactive(raw) === proxy) // true

 // 在一个代理上调用 reactive() 会返回它自己
console.log(reactive(proxy) === proxy) // true
```

这个规则也适用于深层级的对象，由于深层响应性（deep reactivity），响应式对象内的深层级对象也是代理对象：

```js
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### reactive() 的局限 \*\* {#limitations-of-reactive}

`reactive()` API 有两个局限：

1. 仅对对象类型有效（对象、数组，以及`Map`、`Set` 这样的[集合类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects#keyed_collections)），对 `string`、`number` 和 `boolean` 这样的 [基础类型](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) 无效。

2. 因为 Vue 的响应式系统是通过属性访问进行追踪的，因此我们必须始终保存对该响应式对象的引用，这意味着我们不可以轻易“替换”一个响应式对象：

   ```js
   let state = reactive({ count: 0 })

   // 不会生效
   state = reactive({ count: 1 })
   ```

   同时这也意味着把响应式对象的某个基础类型属性传入函数，或是从响应式对象中解构属性时，我们会失去它的响应性：

   ```js
   const state = reactive({ count: 0 })

   // n 是一个本地变量
   // 与 state.count 无关
   let n = state.count
   // 不会影响 state
   n++

   // count 也与 state.count 无关
   let { count } = state
   // 不会影响 state
   count++

   // 该函数接受纯数字作为参数
   // 并不会追踪 state.count 的变化
   callSomeFunction(state.count)
   ```

## 用 `ref()` 定义响应式变量 \*\* {#reactive-variables-with-ref}

为了解决 `reactive()` 的局限，Vue 提供 [`ref()`](/api/reactivity-core.html#ref) 方法创建响应式的 **ref**<sup>[[1]](#footnote-1)</sup>，用它来 hold 住任何类型的值：

```js
import { ref } from 'vue'

const count = ref(0)
```

`ref()` 会获取参数中的值，将其包裹为一个带 `.value` 属性的对象：

```js
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

也可参考: [为 ref 标注类型](/guide/typescript/composition-api.html#typing-ref) <sup class="vt-badge ts" />

和响应式对象的属性类似，ref 的 `.value` 属性也是响应式的。同时，当值为对象类型时，ref 会用 `reactive()` 自动转换它的 `.value`。

一个包含对象类型值的 ref 可以响应式地替换整个对象：

```js
const objectRef = ref({ count: 0 })

// 这是响应式替换
objectRef.value = { count: 1 }
```

ref 被传递给函数或是从普通对象上解构时，不会丢失响应性：

```js
const obj = {
  foo: ref(1),
  bar: ref(2)
}

// 该函数接收一个 ref
// 需要通过 .value 取值
// 但它会保持响应性
callSomeFunction(obj.foo)

// 仍然是响应式的
const { foo, bar } = obj
```

一言蔽之，`ref()` 使我们能创造一种对任何值的 "引用" 并能随意传递不丢失响应性。这个功能非常重要，因为它经常用于将逻辑提取到 [组合函数](/guide/reusability/composables.html) 中。

<small>
译者注：
<div id="footnote-1">[1] ref 指被 ref() 函数包裹后得到的响应式对象</div>
</small>

### ref 在模板中的自动解封 \*\* {#ref-unwrapping-in-templates}

在模板中访问作为顶层属性的 ref 时，它们会自动地 “解封（unwrapping）” 而无需使用 `.value`。这里用 `ref()` 改写之前的计数器示例：

```vue{13}
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }} <!-- 无需使用 .value -->
  </button>
</template>
```

[在 Playground 中试试手](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCB7IHJlZiB9IGZyb20gJ3Z1ZSdcblxuY29uc3QgY291bnQgPSByZWYoMClcblxuZnVuY3Rpb24gaW5jcmVtZW50KCkge1xuICBjb3VudC52YWx1ZSsrXG59XG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8YnV0dG9uIEBjbGljaz1cImluY3JlbWVudFwiPnt7IGNvdW50IH19PC9idXR0b24+XG48L3RlbXBsYXRlPiIsImltcG9ydC1tYXAuanNvbiI6IntcbiAgXCJpbXBvcnRzXCI6IHtcbiAgICBcInZ1ZVwiOiBcImh0dHBzOi8vc2ZjLnZ1ZWpzLm9yZy92dWUucnVudGltZS5lc20tYnJvd3Nlci5qc1wiXG4gIH1cbn0ifQ==)

注意，解封过程仅作用于顶层属性，访问深层级的 ref 是不会解封的：

```js
const object = { foo: ref(1) }
```

```vue-html
{{ object.foo }} <!-- 不会解封 -->
```

可以将 `foo` 设置为顶层属性来解决：

```js
const { foo } = object
```

```vue-html
{{ foo }} <!-- 已经解封 -->
```

现在 `foo` 已按照预期解封。

### ref 在响应式对象中的解封 \*\* {#ref-unwrapping-in-reactive-objects}

当 `ref` 成为一个响应式对象的属性被访问或更改时，它会自动解封而表现得和普通属性一样：

```js
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

如果将一个新的 ref 赋值给响应式对象某个已经为 ref 的属性，那么它会替换掉旧的 ref：

```js
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// 原来的 ref 现在已经和 state.count 脱去联系
console.log(count.value) // 1
```

ref 只有在一个响应式对象之内时才会发生解封。当作为 [浅层响应式对象](/api/reactivity-advanced.html#shallowreactive) 的属性被访问时不会解封。

#### ref 在数组和集合的解封 {#ref=unwrapping-in-arrays-and=collections}

和响应式对象不同，当 ref 是一个响应式数组或 `Map` 这样的原生集合类型的元素时，访问 ref，不会进行解封：

```js
const books = reactive([ref('Vue 3 Guide')])
// 这里需要 .value
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// 这里需要 .value
console.log(map.get('count').value)
```

</div>

<div class="options-api">

### 有状态的方法 \* {#stateful-methods}

在某些情况下，我们可能需要动态地创建一个方法函数，比如创建一个预置防抖的事件处理器：

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // 使用 Lodash 的防抖函数
    click: debounce(function () {
      // ... 对点击的响应 ...
    }, 500)
  }
}
```

不过这种方法对于被重用的组件来说是有问题的，因为这个预置防抖的函数是 **有状态的**：它在运行时维护着一个内部状态。如果多个组件实例都共享这同一个预置防抖的函数，那么它们之间将会互相影响。

要保持每个组件实例的防抖函数都彼此独立，我们可以改为在 `created` 生命周期钩子中创建这个预置防抖的函数：

```js
export default {
  created() {
    // 每个实例都有了自己的预置防抖的处理函数
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // 最好是在组件卸载时
    // 清除掉防抖计时器
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... 对点击的响应 ...
    }
  }
}
```

</div>

<div class="composition-api">

## 响应性转换 <sup class="vt-badge experimental" /> \*\* {#reactivity-transform}

必须对 ref 使用 `.value` 是一个受 JavaScript 语言能力约束而带来的缺陷。然而我们可以通过在编译时期自动在合适的位置添加上 `.value` 来改进开发体验。Vue 提供在编译时作相应转换，使我们可以像这样改写上面的计数器示例：

```vue
<script setup>
let count = $ref(0)

function increment() {
  // 不需要用 .value
  count++
}
</script>

<template>
  <button @click="increment">{{ count }}</button>
</template>
```

你可以在 [响应性转换](/guide/extras/reactivity-transform.html) 这个专门章节了解更多细节。请注意它仍处于实验性阶段，在最终提案落地前仍可能发生改动。

</div>

## 小结

- 本节提到的概念

```
响应式基础
├─ 声明响应式状态
│  ├─ 响应式代理 vs. 原始对象
│  └─ <script setup>
├─ 声明响方法
│  ├─ DOM 更新时机
│  └─ 深层响应性
│  └─ reactive() 的局限
├─ 用 ref() 定义响应式变量
│  ├─ ref 在模板中的解封
│  └─ ref 在响应式对象中的解封
│  │  ├─ ref 在数组和集合的解封
│  └─ 有状态的方法
├─ 响应性转换
```
