---
outline: deep
---

# 穿透属性? Fallthrough Attributes {#fallthrough-attributes}

> This page assumes you've already read the [Components Basics](/guide/essentials/component-basics). Read that first if you are new to components.

## 属性继承

一个从父组件“跌落下来”的属性，是父组件传给子组件的属性或 `v-on` 事件监听，但子组件内没有声明相应 [props](./props) 或 [emits](./events.html#declaring-emitted-events)，常见的“跌落”属性有 `class`、 `style`、和 `id` 属性。

组件渲染**单一根元素**时，fallthrough（跌落）而来的属性会**自动追加到根元素属性**上<sup>[[1]](#footnote-1)</sup>。如，`<MyButton>` 组件有以下template：

```vue-html
<!-- template of <MyButton> -->
<button>点我</button>
```

它在父组件中这样使用：

```vue-html
<MyButton class="large" />
```

渲染 `<MyButton>` 组件后得到 DOM 如下：

```html
<button class="large">点我</button>
```

<small>
<div id="footnote-1">[1] 跌落下来的属性只是自动追加到根元素上，看来不能在实例对象中进行操纵。</div>
</small>

### `class` 与 `style` 属性的合并 {#class-and-style-merging}

如果子组件的根元素上已有 `class` 或 `style` 属性，父组件再传入相同属性时，两者就会合并。将上述 `<MyButton>` 模板修改为：

```vue-html
<!-- template of <MyButton> -->
<button class="btn">点我</button>
```

最后渲染出来的 DOM 是：

```html
<button class="btn large">click me</button>
```

### `v-on` 监听器的继承

`v-on` 事件监听器也会进行属性合并：

```vue-html
<MyButton @click="onClick" />
```

`click` 事件监听器会追加到 `<MyButton>` 组件的根元素上，如果根元素是原生 `<button>` 元素，就追加到它上面，当原生 `<button>` 元素被点击时，就会触发父组件的 `onClick` 方法，如果原生 `<button>` 元素已有`click`事件监听器，两者监听器方法都会触发。

### 嵌套组件的继承 {#nested-component-inheritance}

如果子组件的根元素不是原生 HTML 元素而是另一个组件怎么办呢，比如，重写 `<MyButton>`，它的根元素也是一个需要被渲染的 `<BaseButton>` 组件。 

```vue-html
<!-- <MyButton/> 的模板只做渲染另一个组件的操作 -->
<BaseButton />
```

`<MyButton>` 接受到的跌落下来的属性会自动推送给 `<BaseButton>`。

注意：

1. `<MyButton>` 推送过去的属性不包括在它里面声明的 props 或者 `v-on` 监听器，声明的 props 和事件监听器都是 `<MyButton>` 内部使用的东西。

2. `<BaseButton>` 可以将接受到的属性声明为 props。Forwarded attributes may be accepted as props by `<BaseButton>`, if declared by it.

## 取消属性继承 {#disabling-attribute-inheritance}

If you do **not** want a component to automatically inherit attributes, you can set `inheritAttrs: false` in the component's options.

<div class="composition-api">

If using `<script setup>`, you will need to declare this option using a separate, normal `<script>` block:

```vue
<script>
// use normal <script> to declare options
export default {
  inheritAttrs: false
}
</script>

<script setup>
// ...setup logic
</script>
```

</div>

The common scenario for disabling attribute inheritance is when attributes need to be applied to other elements besides the root node. By setting the `inheritAttrs` option to `false`, you can take full control over where the fallthrough attributes should be applied.

These fallthrough attributes can be accessed directly in template expressions as `$attrs`:

```vue-html
<span>Fallthrough attributes: {{ $attrs }}</span>
```

The `$attrs` object includes all attributes that are not declared by the component's `props` or `emits` options (e.g., `class`, `style`, `v-on` listeners, etc.).

Some notes:

- Unlike props, fallthrough attributes preserve their original casing in JavaScript, so an attribute like `foo-bar` needs to be accessed as `$attrs['foo-bar']`.

- A `v-on` event listener like `@click` will be exposed on the object as a function under `$attrs.onClick`.

Using our `<MyButton>` component example from the [previous section](#attribute-inheritance) - sometimes we may need to wrap the actual `<button>` element with an extra `<div>` for styling purposes:

```vue-html
<div class="btn-wrapper">
  <button class="btn">click me</button>
</div>
```

We want all fallthrough attributes like `class` and `v-on` listeners to be applied to the inner `<button>`, not the outer `<div>`. We can achieve this with `inheritAttrs: false` and `v-bind="$attrs"`:

```vue-html{2}
<div class="btn-wrapper">
  <button class="btn" v-bind="$attrs">click me</button>
</div>
```

Remember that [`v-bind` without an argument](/guide/essentials/template-syntax.html#dynamically-binding-multiple-attributes) binds all the properties of an object as attributes of the target element.

## Attribute Inheritance on Multiple Root Nodes

Unlike components with a single root node, components with multiple root nodes do not have an automatic attribute fallthrough behavior. If `$attrs` are not bound explicitly, a runtime warning will be issued.

```vue-html
<CustomLayout id="custom-layout" @click="changeValue" />
```

If `<CustomLayout>` has the following multi-root template, there will be a warning because Vue cannot be sure where to apply the fallthrough attributes:

```vue-html
<header>...</header>
<main>...</main>
<footer>...</footer>
```

The warning will be suppressed if `$attrs` is explicitly bound:

```vue-html{2}
<header>...</header>
<main v-bind="$attrs">...</main>
<footer>...</footer>
```

## Accessing Fallthrough Attributes in JavaScript

<div class="composition-api">

If needed, you can access a component's fallthrough attributes in `<script setup>` using the `useAttrs()` API:

```vue
<script setup>
import { useAttrs } from 'vue'

const attrs = useAttrs()
</script>
```

If not using `<script setup>`, `attrs` will be exposed as a property of the `setup()` context:

```js
export default {
  setup(props, ctx) {
    // fallthrough attributes are exposed as ctx.attrs
    console.log(ctx.attrs)
  }
}
```

Note that although the `attrs` object here always reflect the latest fallthrough attributes, it isn't reactive (for performance reasons). You cannot use watchers to observe its changes. If you need reactivity, use a prop. Alternatively, you can use `onUpdated()` to perform side effects with latest `attrs` on each update.

</div>

<div class="options-api">

If needed, you can access a component's fallthrough attributes via the `$attrs` instance property:

```js
export default {
  created() {
    console.log(this.$attrs)
  }
}
```

</div>
