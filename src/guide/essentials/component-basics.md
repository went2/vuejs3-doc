# 组件基础 {#componets-basics}

组件是种抽象，组件让我们把 UI 界面划分成独立、可重用的部分，处理各自的逻辑，一个 app 通常会组织成嵌套的组件树的形式：

![Component Tree](./images/components.png)

<!-- https://www.figma.com/file/qa7WHDQRWuEZNRs7iZRZSI/components -->

嵌套组件就像嵌套原生 HTML 标签，Vue 实现的组件模式能让开发者在每个组件内封装自定义的逻辑与内容，同时 Vue 还保持对Web 原生组件的兼容，有兴趣了解更多 Vue 组件和 Web 组件的关系，[参见这里](/guide/extras/web-components.html)

## 定义组件 {#defining-a-component}

如果项目有构建这一步，就通常把一个组件写成一个 `.vue` 文件，这叫 [单文件组件](/guide/scaling-up/sfc.html) (缩写 SFC):

<div class="options-api">

```vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">你点了 {{ count }} 次.</button>
</template>
```

</div>
<div class="composition-api">

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">你点了 {{ count }} 次.</button>
</template>
```

</div>

如果没有构建这一步，可以把 Vue 组件定义为一个普通 JavaScript 对象，该对象需包含特定的 Vue 选项作为属性：

<div class="options-api">

```js
export default {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      你点了 {{ count }} 次.
    </button>`
}
```

</div>
<div class="composition-api">

```js
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `
    <button @click="count++">
      你点了 {{ count }} 次.
    </button>`
  // or `template: '#my-template-element'`
}
```

</div>

上面 template 的值是JavaScript 内联字符串，Vue 会立时把它编译出来。template 也可以是一个元素（通常是原生`<template>` 元素）的 ID 选择器，Vue 会用元素的内容作为 template 内容来源。

上面的例子定义了一个组件，并导出为 `.js` 文件，可以默认导出，也可以通过命名，在同一个文件中导出多个组件。

## 使用组件 {#using-a-component}

:::tip
We will be using SFC syntax for the rest of this guide - the concepts around components are the same regardless of whether you are using a build step for not. The [Examples](/examples/) section shows component usage in both scenarios.
:::

先在父组件中导入子组件。
To use a child component, we need to import it in the parent component. Assuming we placed our counter component inside a file called `ButtonCounter.vue`, the component will be exposed as the file's default export:

<div class="options-api">

```vue
<script>
import ButtonCounter from './ButtonCounter.vue'

export default {
  components: {
    ButtonCounter
  }
}
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

为了将导入的组件暴露给模板使用，需要在 `components` 选项中进行[注册](/guide/components/registration.html)，然后可以在模板中跟使用标签一样使用注册后的组件名。

To expose the imported component to our template, we need to [register](/guide/components/registration.html) it with the `components` option. The component will then be available as a tag using the key it is registered under.

</div>

<div class="composition-api">

```vue
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

用 `<script setup>` 后，导入的组件无需注册就可直接在模板中使用。
With `<script setup>`, imported components are automatically made available to the template.

</div>

也可以全局注册一个组件，这样它能被在 App 的级别下所有组件使用而不用导入。
It's also possible to globally register a component, making it available to all components in a given app without having to import it. The pros and cons of global vs. local registration is discussed in the dedicated [Component Registration](/guide/components/registration.html) section.

Components can be reused as many times as you want:

```vue-html
<h1>Here are many child components!</h1>
<ButtonCounter />
<ButtonCounter />
<ButtonCounter />
```

<div class="options-api">

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmltcG9ydCBCdXR0b25Db3VudGVyIGZyb20gJy4vQnV0dG9uQ291bnRlci52dWUnXG4gIFxuZXhwb3J0IGRlZmF1bHQge1xuICBjb21wb25lbnRzOiB7XG4gICAgQnV0dG9uQ291bnRlclxuICB9XG59XG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuXHQ8aDE+SGVyZSBhcmUgbWFueSBjaGlsZCBjb21wb25lbnRzITwvaDE+XG5cdDxCdXR0b25Db3VudGVyIC8+XG5cdDxCdXR0b25Db3VudGVyIC8+XG5cdDxCdXR0b25Db3VudGVyIC8+XG48L3RlbXBsYXRlPiIsImltcG9ydC1tYXAuanNvbiI6IntcbiAgXCJpbXBvcnRzXCI6IHtcbiAgICBcInZ1ZVwiOiBcImh0dHBzOi8vc2ZjLnZ1ZWpzLm9yZy92dWUucnVudGltZS5lc20tYnJvd3Nlci5qc1wiXG4gIH1cbn0iLCJCdXR0b25Db3VudGVyLnZ1ZSI6IjxzY3JpcHQ+XG5leHBvcnQgZGVmYXVsdCB7XG4gIGRhdGEoKSB7XG4gICAgcmV0dXJuIHtcbiAgICAgIGNvdW50OiAwXG4gICAgfVxuICB9XG59XG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8YnV0dG9uIEBjbGljaz1cImNvdW50KytcIj5cbiAgICBZb3UgY2xpY2tlZCBtZSB7eyBjb3VudCB9fSB0aW1lcy5cbiAgPC9idXR0b24+XG48L3RlbXBsYXRlPiJ9)

</div>
<div class="composition-api">

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCBCdXR0b25Db3VudGVyIGZyb20gJy4vQnV0dG9uQ291bnRlci52dWUnXG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuXHQ8aDE+SGVyZSBhcmUgbWFueSBjaGlsZCBjb21wb25lbnRzITwvaDE+XG5cdDxCdXR0b25Db3VudGVyIC8+XG5cdDxCdXR0b25Db3VudGVyIC8+XG5cdDxCdXR0b25Db3VudGVyIC8+XG48L3RlbXBsYXRlPiIsImltcG9ydC1tYXAuanNvbiI6IntcbiAgXCJpbXBvcnRzXCI6IHtcbiAgICBcInZ1ZVwiOiBcImh0dHBzOi8vc2ZjLnZ1ZWpzLm9yZy92dWUucnVudGltZS5lc20tYnJvd3Nlci5qc1wiXG4gIH1cbn0iLCJCdXR0b25Db3VudGVyLnZ1ZSI6IjxzY3JpcHQgc2V0dXA+XG5pbXBvcnQgeyByZWYgfSBmcm9tICd2dWUnXG5cbmNvbnN0IGNvdW50ID0gcmVmKDApXG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8YnV0dG9uIEBjbGljaz1cImNvdW50KytcIj5cbiAgICBZb3UgY2xpY2tlZCBtZSB7eyBjb3VudCB9fSB0aW1lcy5cbiAgPC9idXR0b24+XG48L3RlbXBsYXRlPiJ9)

</div>

Notice that when clicking on the buttons, each one maintains its own, separate `count`. That's because each time you use a component, a new **instance** of it is created.
每次在模板中使用一个组件，会新建一个该组件的**实例**。

In SFCs, it's recommended to use `PascalCase` tag names for child components to differentiate from native HTML elements. Although native HTML tag names are case-insensitive, Vue SFC is a compiled format so we are able to use case-sensitive tag names in it. We are also able to use `/>` to close a tag.

If you are authoring your templates directly in a DOM (e.g. as the content of a native `<template>` element), the template will be subject to the browser's native HTML parsing behavior. In such cases, you will need to use `kebab-case` and explicit closing tags for components:

```vue-html
<!-- if this template is written in the DOM -->
<button-counter></button-counter>
<button-counter></button-counter>
<button-counter></button-counter>
```

See [DOM template parsing caveats](#dom-template-parsing-caveats) for more details.

## Passing Props
## 传递 props

If we are building a blog, we will likely need a component representing a blog post. We want all the blog posts to share the same visual layout, but with different content. Such a component won't be useful unless you can pass data to it, such as the title and content of the specific post we want to display. That's where props come in.

props 是注册到组件上的自定义 attribute。要先声明这个组件能接受哪些 props（声明为一个列表）
Props are custom attributes you can register on a component. To pass a title to our blog post component, we must declare it in the list of props this component accepts, using the <span class="options-api">[`props`](/api/options-state.html#props) option</span><span class="composition-api">[`defineProps`](/api/sfc-script-setup.html#defineprops-defineemits) macro</span>:

<div class="options-api">

```vue
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title']
}
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

当给组件的 prop attribute 传值时，它就会成为这个组件实例的属性，和其他组件属性一样。它这个属性的值在组件模板、组件的 `this` 上下文中都能获取到。
When a value is passed to a prop attribute, it becomes a property on that component instance. The value of that property is accessible within the template and on the component's `this` context, just like any other component property.

</div>
<div class="composition-api">

```vue
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

`defineProps` is a compile-time macro that is only available inside `<script setup>` and does not need to be explicitly imported. Declared props are automatically exposed to the template. `defineProps` also returns an object that contains all the props passed to the component, so that we can access them in JavaScript if needed:

`defineProps` 会返回一个包含所有接收到的 props 的对象，有需要时可用 JavaScript 访问：

```js
const props = defineProps(['title'])
console.log(props.title)
```

See also: [为 props 定类型](/guide/typescript/composition-api.html#typing-component-props) <sup class="vt-badge ts" />

If you are not using `<script setup>`, props should be declared using the `props` option, and the props object will be passed to `setup()` as the first argument:

```js
export default {
  props: ['title'],
  setup(props) {
    console.log(props.title)
  }
}
```

</div>

A component can have as many props as you like and, by default, any value can be passed to any prop.

一个 prop 在子组件中注册后，就能像自定义 HTML attribute 一样对待它，给它传值：
Once a prop is registered, you can pass data to it as a custom attribute, like this:

```vue-html
<BlogPost title="My journey with Vue" />
<BlogPost title="Blogging with Vue" />
<BlogPost title="Why Vue is so fun" />
```

In a typical app, however, you'll likely have an array of posts in your parent component:

<div class="options-api">

```js
export default {
  // ...
  data() {
    return {
      posts: [
        { id: 1, title: 'My journey with Vue' },
        { id: 2, title: 'Blogging with Vue' },
        { id: 3, title: 'Why Vue is so fun' }
      ]
    }
  }
}
```

</div>
<div class="composition-api">

```js
const posts = ref([
  { id: 1, title: 'My journey with Vue' },
  { id: 2, title: 'Blogging with Vue' },
  { id: 3, title: 'Why Vue is so fun' }
])
```

</div>

Then want to render a component for each one, using `v-for`:

```vue-html
<BlogPost
  v-for="post in posts"
  :key="post.id"
  :title="post.title"
 />
```

<div class="options-api">

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmltcG9ydCBCbG9nUG9zdCBmcm9tICcuL0Jsb2dQb3N0LnZ1ZSdcbiAgXG5leHBvcnQgZGVmYXVsdCB7XG4gIGNvbXBvbmVudHM6IHtcbiAgICBCbG9nUG9zdFxuICB9LFxuICBkYXRhKCkge1xuICAgIHJldHVybiB7XG4gICAgICBwb3N0czogW1xuICAgICAgICB7IGlkOiAxLCB0aXRsZTogJ015IGpvdXJuZXkgd2l0aCBWdWUnIH0sXG4gICAgICAgIHsgaWQ6IDIsIHRpdGxlOiAnQmxvZ2dpbmcgd2l0aCBWdWUnIH0sXG4gICAgICAgIHsgaWQ6IDMsIHRpdGxlOiAnV2h5IFZ1ZSBpcyBzbyBmdW4nIH1cbiAgICAgIF1cbiAgICB9XG4gIH1cbn1cbjwvc2NyaXB0PlxuXG48dGVtcGxhdGU+XG5cdDxCbG9nUG9zdFxuICBcdHYtZm9yPVwicG9zdCBpbiBwb3N0c1wiXG5cdCAgOmtleT1cInBvc3QuaWRcIlxuICBcdDp0aXRsZT1cInBvc3QudGl0bGVcIlxuXHQ+PC9CbG9nUG9zdD5cbjwvdGVtcGxhdGU+IiwiaW1wb3J0LW1hcC5qc29uIjoie1xuICBcImltcG9ydHNcIjoge1xuICAgIFwidnVlXCI6IFwiaHR0cHM6Ly9zZmMudnVlanMub3JnL3Z1ZS5ydW50aW1lLmVzbS1icm93c2VyLmpzXCJcbiAgfVxufSIsIkJsb2dQb3N0LnZ1ZSI6IjxzY3JpcHQ+XG5leHBvcnQgZGVmYXVsdCB7XG4gIHByb3BzOiBbJ3RpdGxlJ11cbn1cbjwvc2NyaXB0PlxuXG48dGVtcGxhdGU+XG4gIDxoND57eyB0aXRsZSB9fTwvaDQ+XG48L3RlbXBsYXRlPiJ9)

</div>
<div class="composition-api">

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCB7IHJlZiB9IGZyb20gJ3Z1ZSdcbmltcG9ydCBCbG9nUG9zdCBmcm9tICcuL0Jsb2dQb3N0LnZ1ZSdcbiAgXG5jb25zdCBwb3N0cyA9IHJlZihbXG4gIHsgaWQ6IDEsIHRpdGxlOiAnTXkgam91cm5leSB3aXRoIFZ1ZScgfSxcbiAgeyBpZDogMiwgdGl0bGU6ICdCbG9nZ2luZyB3aXRoIFZ1ZScgfSxcbiAgeyBpZDogMywgdGl0bGU6ICdXaHkgVnVlIGlzIHNvIGZ1bicgfVxuXSlcbjwvc2NyaXB0PlxuXG48dGVtcGxhdGU+XG5cdDxCbG9nUG9zdFxuICBcdHYtZm9yPVwicG9zdCBpbiBwb3N0c1wiXG5cdCAgOmtleT1cInBvc3QuaWRcIlxuICBcdDp0aXRsZT1cInBvc3QudGl0bGVcIlxuXHQ+PC9CbG9nUG9zdD5cbjwvdGVtcGxhdGU+IiwiaW1wb3J0LW1hcC5qc29uIjoie1xuICBcImltcG9ydHNcIjoge1xuICAgIFwidnVlXCI6IFwiaHR0cHM6Ly9zZmMudnVlanMub3JnL3Z1ZS5ydW50aW1lLmVzbS1icm93c2VyLmpzXCJcbiAgfVxufSIsIkJsb2dQb3N0LnZ1ZSI6IjxzY3JpcHQgc2V0dXA+XG5kZWZpbmVQcm9wcyhbJ3RpdGxlJ10pXG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8aDQ+e3sgdGl0bGUgfX08L2g0PlxuPC90ZW1wbGF0ZT4ifQ==)

</div>

Notice how we can use `v-bind` to pass dynamic props. This is especially useful when you don't know the exact content you're going to render ahead of time.

That's all you need to know about props for now, but once you've finished reading this page and feel comfortable with its content, we recommend coming back later to read the full guide on [Props](/guide/components/props.html).

## Listening to Events
## 事件监听

As we develop our `<BlogPost>` component, some features may require communicating back up to the parent. For example, we may decide to include an accessibility feature to enlarge the text of blog posts, while leaving the rest of the page its default size.

In the parent, we can support this feature by adding a `postFontSize` <span class="options-api">data property</span><span class="composition-api">ref</span>:

（composition-api 与 options-api 中的 data property 对应的概念一律为 ref）

<div class="options-api">

```js{6}
data() {
  return {
    posts: [
      /* ... */
    ],
    postFontSize: 1
  }
}
```

</div>
<div class="composition-api">

```js{5}
const posts = ref([
  /* ... */
])

const postFontSize = ref(1)
```

</div>

Which can be used in the template to control the font size of all blog posts:

```vue-html{1,7}
<div :style="{ fontSize: postFontSize + 'em' }">
  <BlogPost
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
   />
</div>
```

Now let's add a button to the `<BlogPost>` component's template:

```vue{5}
<!-- BlogPost.vue, omitting <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button>Enlarge text</button>
  </div>
</template>
```

The button currently doesn't do anything yet - we want clicking the button to communicate to the parent that it should enlarge the text of all posts. To solve this problem, component instances provide a custom events system. The parent can choose to listen to any event on the child component instance with `v-on` or `@`, just as we would with a native DOM event:

组件实例有一个自定义事件系统，父组件可监听子组件实例的任何事件。

```vue-html{3}
<BlogPost
  ...
  @enlarge-text="postFontSize += 0.1"
 />
```

子组件要在它自己身上 emit 一个事件，通过调用内建的 `$emit` 方法，把事件名传进去。
Then the child component can emit an event on itself by calling the built-in [**`$emit`** method](/api/component-instance.html#emit), passing the name of the event:

```vue{5}
<!-- BlogPost.vue, omitting <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
```

Thanks to the `@enlarge-text="postFontSize += 0.1"` listener, the parent will receive the event and update the value of `postFontSize`.

<div class="options-api">

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmltcG9ydCBCbG9nUG9zdCBmcm9tICcuL0Jsb2dQb3N0LnZ1ZSdcbiAgXG5leHBvcnQgZGVmYXVsdCB7XG4gIGNvbXBvbmVudHM6IHtcbiAgICBCbG9nUG9zdFxuICB9LFxuICBkYXRhKCkge1xuICAgIHJldHVybiB7XG4gICAgICBwb3N0czogW1xuICAgICAgICB7IGlkOiAxLCB0aXRsZTogJ015IGpvdXJuZXkgd2l0aCBWdWUnIH0sXG4gICAgICAgIHsgaWQ6IDIsIHRpdGxlOiAnQmxvZ2dpbmcgd2l0aCBWdWUnIH0sXG4gICAgICAgIHsgaWQ6IDMsIHRpdGxlOiAnV2h5IFZ1ZSBpcyBzbyBmdW4nIH1cbiAgICAgIF0sXG4gICAgICBwb3N0Rm9udFNpemU6IDFcbiAgICB9XG4gIH1cbn1cbjwvc2NyaXB0PlxuXG48dGVtcGxhdGU+XG4gIDxkaXYgOnN0eWxlPVwieyBmb250U2l6ZTogcG9zdEZvbnRTaXplICsgJ2VtJyB9XCI+XG4gICAgPEJsb2dQb3N0XG4gICAgICB2LWZvcj1cInBvc3QgaW4gcG9zdHNcIlxuICAgICAgOmtleT1cInBvc3QuaWRcIlxuICAgICAgOnRpdGxlPVwicG9zdC50aXRsZVwiXG4gICAgICBAZW5sYXJnZS10ZXh0PVwicG9zdEZvbnRTaXplICs9IDAuMVwiXG4gICAgPjwvQmxvZ1Bvc3Q+XG4gIDwvZGl2PlxuPC90ZW1wbGF0ZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59IiwiQmxvZ1Bvc3QudnVlIjoiPHNjcmlwdD5cbmV4cG9ydCBkZWZhdWx0IHtcbiAgcHJvcHM6IFsndGl0bGUnXSxcbiAgZW1pdHM6IFsnZW5sYXJnZS10ZXh0J11cbn1cbjwvc2NyaXB0PlxuXG48dGVtcGxhdGU+XG4gIDxkaXYgY2xhc3M9XCJibG9nLXBvc3RcIj5cblx0ICA8aDQ+e3sgdGl0bGUgfX08L2g0PlxuXHQgIDxidXR0b24gQGNsaWNrPVwiJGVtaXQoJ2VubGFyZ2UtdGV4dCcpXCI+RW5sYXJnZSB0ZXh0PC9idXR0b24+XG4gIDwvZGl2PlxuPC90ZW1wbGF0ZT4ifQ==)

</div>
<div class="composition-api">

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCB7IHJlZiB9IGZyb20gJ3Z1ZSdcbmltcG9ydCBCbG9nUG9zdCBmcm9tICcuL0Jsb2dQb3N0LnZ1ZSdcbiAgXG5jb25zdCBwb3N0cyA9IHJlZihbXG4gIHsgaWQ6IDEsIHRpdGxlOiAnTXkgam91cm5leSB3aXRoIFZ1ZScgfSxcbiAgeyBpZDogMiwgdGl0bGU6ICdCbG9nZ2luZyB3aXRoIFZ1ZScgfSxcbiAgeyBpZDogMywgdGl0bGU6ICdXaHkgVnVlIGlzIHNvIGZ1bicgfVxuXSlcblxuY29uc3QgcG9zdEZvbnRTaXplID0gcmVmKDEpXG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuXHQ8ZGl2IDpzdHlsZT1cInsgZm9udFNpemU6IHBvc3RGb250U2l6ZSArICdlbScgfVwiPlxuICAgIDxCbG9nUG9zdFxuICAgICAgdi1mb3I9XCJwb3N0IGluIHBvc3RzXCJcbiAgICAgIDprZXk9XCJwb3N0LmlkXCJcbiAgICAgIDp0aXRsZT1cInBvc3QudGl0bGVcIlxuICAgICAgQGVubGFyZ2UtdGV4dD1cInBvc3RGb250U2l6ZSArPSAwLjFcIlxuICAgID48L0Jsb2dQb3N0PlxuICA8L2Rpdj5cbjwvdGVtcGxhdGU+IiwiaW1wb3J0LW1hcC5qc29uIjoie1xuICBcImltcG9ydHNcIjoge1xuICAgIFwidnVlXCI6IFwiaHR0cHM6Ly9zZmMudnVlanMub3JnL3Z1ZS5ydW50aW1lLmVzbS1icm93c2VyLmpzXCJcbiAgfVxufSIsIkJsb2dQb3N0LnZ1ZSI6IjxzY3JpcHQgc2V0dXA+XG5kZWZpbmVQcm9wcyhbJ3RpdGxlJ10pXG5kZWZpbmVFbWl0cyhbJ2VubGFyZ2UtdGV4dCddKVxuPC9zY3JpcHQ+XG5cbjx0ZW1wbGF0ZT5cbiAgPGRpdiBjbGFzcz1cImJsb2ctcG9zdFwiPlxuICAgIDxoND57eyB0aXRsZSB9fTwvaDQ+XG4gICAgPGJ1dHRvbiBAY2xpY2s9XCIkZW1pdCgnZW5sYXJnZS10ZXh0JylcIj5FbmxhcmdlIHRleHQ8L2J1dHRvbj5cbiAgPC9kaXY+XG48L3RlbXBsYXRlPiJ9)

</div>

不用内联的方式，可以用 `emits` option or `defineEmits` api 声明需要 emit 的事件
We can optionally declare emitted events using the <span class="options-api">[`emits`](/api/options-state.html#emits) option</span><span class="composition-api">[`defineEmits`](/api/sfc-script-setup.html#defineprops-defineemits) macro</span>:

<div class="options-api">

```vue{5}
<!-- BlogPost.vue -->
<script>
export default {
  props: ['title'],
  emits: ['enlarge-text']
}
</script>
```

</div>
<div class="composition-api">

```vue{4}
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
defineEmits(['enlarge-text'])
</script>
```

</div>

This documents all the events that a component emits and optionally [validates them](/guide/components/events.html#events-validation). It also allows Vue to avoid implicitly applying them as native listeners to the child component's root element.

<div class="composition-api">

Similar to `defineProps`, `defineEmits` is also only usable in `<script setup>` and doesn't need to be imported. It returns an `emit` function that can be used to emit events in JavaScript code:

```js
const emit = defineEmits(['enlarge-text'])

emit('enlarge-text')
```

See also: [Typing Component Emits](/guide/typescript/composition-api.html#typing-component-emits) <sup class="vt-badge ts" />

If you are not using `<script setup>`, you can declare emitted events using the `emits` option. You can access the `emit` function as a property of the setup context (passed to `setup()` as the second argument):

```js
export default {
  emits: ['enlarge-text'],
  setup(props, ctx) {
    ctx.emit('enlarge-text')
  }
}
```

</div>

That's all you need to know about custom component events for now, but once you've finished reading this page and feel comfortable with its content, we recommend coming back later to read the full guide on [Custom Events](/guide/components/events).

读一下 [自定义事件](/guide/components/events)。

## Content Distribution with Slots
## 用 Slots 做 Content Distribution

Just like with HTML elements, it's often useful to be able to pass content to a component, like this:

```vue-html
<AlertBox>
  Something bad happened.
</AlertBox>
```

Which might render something like:

:::danger This is an Error for Demo Purposes
Something bad happened.
:::

This can be achieved using Vue's custom `<slot>` element:

```vue{4}
<template>
  <div class="alert-box">
    <strong>Error!</strong>
    <slot />
  </div>
</template>

<style scoped>
.alert-box {
  /* ... */
}
</style>
```

Vue 提供的 `<slot>` 元素是一个占位符，它最终会被组件的内容（标签之内的东西）取代。
As you'll see above, we use the `<slot>` as a placeholder where we want the content to go – and that's it. We're done!

<div class="options-api">

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmltcG9ydCBBbGVydEJveCBmcm9tICcuL0FsZXJ0Qm94LnZ1ZSdcbiAgXG5leHBvcnQgZGVmYXVsdCB7XG4gIGNvbXBvbmVudHM6IHsgQWxlcnRCb3ggfVxufVxuPC9zY3JpcHQ+XG5cbjx0ZW1wbGF0ZT5cblx0PEFsZXJ0Qm94PlxuICBcdFNvbWV0aGluZyBiYWQgaGFwcGVuZWQuXG5cdDwvQWxlcnRCb3g+XG48L3RlbXBsYXRlPiIsImltcG9ydC1tYXAuanNvbiI6IntcbiAgXCJpbXBvcnRzXCI6IHtcbiAgICBcInZ1ZVwiOiBcImh0dHBzOi8vc2ZjLnZ1ZWpzLm9yZy92dWUucnVudGltZS5lc20tYnJvd3Nlci5qc1wiXG4gIH1cbn0iLCJBbGVydEJveC52dWUiOiI8dGVtcGxhdGU+XG4gIDxkaXYgY2xhc3M9XCJhbGVydC1ib3hcIj5cbiAgICA8c3Ryb25nPkVycm9yITwvc3Ryb25nPlxuICAgIDxici8+XG4gICAgPHNsb3QgLz5cbiAgPC9kaXY+XG48L3RlbXBsYXRlPlxuXG48c3R5bGUgc2NvcGVkPlxuLmFsZXJ0LWJveCB7XG4gIGNvbG9yOiAjNjY2O1xuICBib3JkZXI6IDFweCBzb2xpZCByZWQ7XG4gIGJvcmRlci1yYWRpdXM6IDRweDtcbiAgcGFkZGluZzogMjBweDtcbiAgYmFja2dyb3VuZC1jb2xvcjogI2Y4ZjhmODtcbn1cbiAgXG5zdHJvbmcge1xuXHRjb2xvcjogcmVkOyAgICBcbn1cbjwvc3R5bGU+In0=)

</div>
<div class="composition-api">

[Try it in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCBBbGVydEJveCBmcm9tICcuL0FsZXJ0Qm94LnZ1ZSdcbjwvc2NyaXB0PlxuXG48dGVtcGxhdGU+XG5cdDxBbGVydEJveD5cbiAgXHRTb21ldGhpbmcgYmFkIGhhcHBlbmVkLlxuXHQ8L0FsZXJ0Qm94PlxuPC90ZW1wbGF0ZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59IiwiQWxlcnRCb3gudnVlIjoiPHRlbXBsYXRlPlxuICA8ZGl2IGNsYXNzPVwiYWxlcnQtYm94XCI+XG4gICAgPHN0cm9uZz5FcnJvciE8L3N0cm9uZz5cbiAgICA8YnIvPlxuICAgIDxzbG90IC8+XG4gIDwvZGl2PlxuPC90ZW1wbGF0ZT5cblxuPHN0eWxlIHNjb3BlZD5cbi5hbGVydC1ib3gge1xuICBjb2xvcjogIzY2NjtcbiAgYm9yZGVyOiAxcHggc29saWQgcmVkO1xuICBib3JkZXItcmFkaXVzOiA0cHg7XG4gIHBhZGRpbmc6IDIwcHg7XG4gIGJhY2tncm91bmQtY29sb3I6ICNmOGY4Zjg7XG59XG4gIFxuc3Ryb25nIHtcblx0Y29sb3I6IHJlZDsgICAgXG59XG48L3N0eWxlPiJ9)

</div>

That's all you need to know about slots for now, but once you've finished reading this page and feel comfortable with its content, we recommend coming back later to read the full guide on [Slots](/guide/components/slots).

读一下 [Slots](/guide/components/slots)。

## Dynamic Components
## 动态组件

Sometimes, it's useful to dynamically switch between components, like in a tabbed interface:
动态在组件之间切换很有用，如 tab 页

<div class="options-api">

[Open example in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmltcG9ydCBIb21lIGZyb20gJy4vSG9tZS52dWUnXG5pbXBvcnQgUG9zdHMgZnJvbSAnLi9Qb3N0cy52dWUnXG5pbXBvcnQgQXJjaGl2ZSBmcm9tICcuL0FyY2hpdmUudnVlJ1xuICBcbmV4cG9ydCBkZWZhdWx0IHtcbiAgY29tcG9uZW50czoge1xuICAgIEhvbWUsXG4gICAgUG9zdHMsXG4gICAgQXJjaGl2ZVxuICB9LFxuICBkYXRhKCkge1xuICAgIHJldHVybiB7XG4gICAgICBjdXJyZW50VGFiOiAnSG9tZScsXG4gICAgICB0YWJzOiBbJ0hvbWUnLCAnUG9zdHMnLCAnQXJjaGl2ZSddXG4gICAgfVxuICB9XG59XG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8ZGl2IGNsYXNzPVwiZGVtb1wiPlxuICAgIDxidXR0b25cbiAgICAgICB2LWZvcj1cInRhYiBpbiB0YWJzXCJcbiAgICAgICA6a2V5PVwidGFiXCJcbiAgICAgICA6Y2xhc3M9XCJbJ3RhYi1idXR0b24nLCB7IGFjdGl2ZTogY3VycmVudFRhYiA9PT0gdGFiIH1dXCJcbiAgICAgICBAY2xpY2s9XCJjdXJyZW50VGFiID0gdGFiXCJcbiAgICAgPlxuICAgICAge3sgdGFiIH19XG4gICAgPC9idXR0b24+XG5cdCAgPGNvbXBvbmVudCA6aXM9XCJjdXJyZW50VGFiXCIgY2xhc3M9XCJ0YWJcIj48L2NvbXBvbmVudD5cbiAgPC9kaXY+XG48L3RlbXBsYXRlPlxuXG48c3R5bGU+XG4uZGVtbyB7XG4gIGZvbnQtZmFtaWx5OiBzYW5zLXNlcmlmO1xuICBib3JkZXI6IDFweCBzb2xpZCAjZWVlO1xuICBib3JkZXItcmFkaXVzOiAycHg7XG4gIHBhZGRpbmc6IDIwcHggMzBweDtcbiAgbWFyZ2luLXRvcDogMWVtO1xuICBtYXJnaW4tYm90dG9tOiA0MHB4O1xuICB1c2VyLXNlbGVjdDogbm9uZTtcbiAgb3ZlcmZsb3cteDogYXV0bztcbn1cblxuLnRhYi1idXR0b24ge1xuICBwYWRkaW5nOiA2cHggMTBweDtcbiAgYm9yZGVyLXRvcC1sZWZ0LXJhZGl1czogM3B4O1xuICBib3JkZXItdG9wLXJpZ2h0LXJhZGl1czogM3B4O1xuICBib3JkZXI6IDFweCBzb2xpZCAjY2NjO1xuICBjdXJzb3I6IHBvaW50ZXI7XG4gIGJhY2tncm91bmQ6ICNmMGYwZjA7XG4gIG1hcmdpbi1ib3R0b206IC0xcHg7XG4gIG1hcmdpbi1yaWdodDogLTFweDtcbn1cbi50YWItYnV0dG9uOmhvdmVyIHtcbiAgYmFja2dyb3VuZDogI2UwZTBlMDtcbn1cbi50YWItYnV0dG9uLmFjdGl2ZSB7XG4gIGJhY2tncm91bmQ6ICNlMGUwZTA7XG59XG4udGFiIHtcbiAgYm9yZGVyOiAxcHggc29saWQgI2NjYztcbiAgcGFkZGluZzogMTBweDtcbn1cbjwvc3R5bGU+IiwiaW1wb3J0LW1hcC5qc29uIjoie1xuICBcImltcG9ydHNcIjoge1xuICAgIFwidnVlXCI6IFwiaHR0cHM6Ly9zZmMudnVlanMub3JnL3Z1ZS5ydW50aW1lLmVzbS1icm93c2VyLmpzXCJcbiAgfVxufSIsIkhvbWUudnVlIjoiPHRlbXBsYXRlPlxuICA8ZGl2IGNsYXNzPVwidGFiXCI+XG4gICAgSG9tZSBjb21wb25lbnRcbiAgPC9kaXY+XG48L3RlbXBsYXRlPiIsIlBvc3RzLnZ1ZSI6Ijx0ZW1wbGF0ZT5cbiAgPGRpdiBjbGFzcz1cInRhYlwiPlxuICAgIFBvc3RzIGNvbXBvbmVudFxuICA8L2Rpdj5cbjwvdGVtcGxhdGU+IiwiQXJjaGl2ZS52dWUiOiI8dGVtcGxhdGU+XG4gIDxkaXYgY2xhc3M9XCJ0YWJcIj5cbiAgICBBcmNoaXZlIGNvbXBvbmVudFxuICA8L2Rpdj5cbjwvdGVtcGxhdGU+In0=)

</div>
<div class="composition-api">

[Open example in the Playground](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdCBzZXR1cD5cbmltcG9ydCBIb21lIGZyb20gJy4vSG9tZS52dWUnXG5pbXBvcnQgUG9zdHMgZnJvbSAnLi9Qb3N0cy52dWUnXG5pbXBvcnQgQXJjaGl2ZSBmcm9tICcuL0FyY2hpdmUudnVlJ1xuaW1wb3J0IHsgcmVmIH0gZnJvbSAndnVlJ1xuIFxuY29uc3QgY3VycmVudFRhYiA9IHJlZignSG9tZScpXG5cbmNvbnN0IHRhYnMgPSB7XG4gIEhvbWUsXG4gIFBvc3RzLFxuICBBcmNoaXZlXG59XG48L3NjcmlwdD5cblxuPHRlbXBsYXRlPlxuICA8ZGl2IGNsYXNzPVwiZGVtb1wiPlxuICAgIDxidXR0b25cbiAgICAgICB2LWZvcj1cIihfLCB0YWIpIGluIHRhYnNcIlxuICAgICAgIDprZXk9XCJ0YWJcIlxuICAgICAgIDpjbGFzcz1cIlsndGFiLWJ1dHRvbicsIHsgYWN0aXZlOiBjdXJyZW50VGFiID09PSB0YWIgfV1cIlxuICAgICAgIEBjbGljaz1cImN1cnJlbnRUYWIgPSB0YWJcIlxuICAgICA+XG4gICAgICB7eyB0YWIgfX1cbiAgICA8L2J1dHRvbj5cblx0ICA8Y29tcG9uZW50IDppcz1cInRhYnNbY3VycmVudFRhYl1cIiBjbGFzcz1cInRhYlwiPjwvY29tcG9uZW50PlxuICA8L2Rpdj5cbjwvdGVtcGxhdGU+XG5cbjxzdHlsZT5cbi5kZW1vIHtcbiAgZm9udC1mYW1pbHk6IHNhbnMtc2VyaWY7XG4gIGJvcmRlcjogMXB4IHNvbGlkICNlZWU7XG4gIGJvcmRlci1yYWRpdXM6IDJweDtcbiAgcGFkZGluZzogMjBweCAzMHB4O1xuICBtYXJnaW4tdG9wOiAxZW07XG4gIG1hcmdpbi1ib3R0b206IDQwcHg7XG4gIHVzZXItc2VsZWN0OiBub25lO1xuICBvdmVyZmxvdy14OiBhdXRvO1xufVxuXG4udGFiLWJ1dHRvbiB7XG4gIHBhZGRpbmc6IDZweCAxMHB4O1xuICBib3JkZXItdG9wLWxlZnQtcmFkaXVzOiAzcHg7XG4gIGJvcmRlci10b3AtcmlnaHQtcmFkaXVzOiAzcHg7XG4gIGJvcmRlcjogMXB4IHNvbGlkICNjY2M7XG4gIGN1cnNvcjogcG9pbnRlcjtcbiAgYmFja2dyb3VuZDogI2YwZjBmMDtcbiAgbWFyZ2luLWJvdHRvbTogLTFweDtcbiAgbWFyZ2luLXJpZ2h0OiAtMXB4O1xufVxuLnRhYi1idXR0b246aG92ZXIge1xuICBiYWNrZ3JvdW5kOiAjZTBlMGUwO1xufVxuLnRhYi1idXR0b24uYWN0aXZlIHtcbiAgYmFja2dyb3VuZDogI2UwZTBlMDtcbn1cbi50YWIge1xuICBib3JkZXI6IDFweCBzb2xpZCAjY2NjO1xuICBwYWRkaW5nOiAxMHB4O1xufVxuPC9zdHlsZT4iLCJpbXBvcnQtbWFwLmpzb24iOiJ7XG4gIFwiaW1wb3J0c1wiOiB7XG4gICAgXCJ2dWVcIjogXCJodHRwczovL3NmYy52dWVqcy5vcmcvdnVlLnJ1bnRpbWUuZXNtLWJyb3dzZXIuanNcIlxuICB9XG59IiwiSG9tZS52dWUiOiI8dGVtcGxhdGU+XG4gIDxkaXYgY2xhc3M9XCJ0YWJcIj5cbiAgICBIb21lIGNvbXBvbmVudFxuICA8L2Rpdj5cbjwvdGVtcGxhdGU+IiwiUG9zdHMudnVlIjoiPHRlbXBsYXRlPlxuICA8ZGl2IGNsYXNzPVwidGFiXCI+XG4gICAgUG9zdHMgY29tcG9uZW50XG4gIDwvZGl2PlxuPC90ZW1wbGF0ZT4iLCJBcmNoaXZlLnZ1ZSI6Ijx0ZW1wbGF0ZT5cbiAgPGRpdiBjbGFzcz1cInRhYlwiPlxuICAgIEFyY2hpdmUgY29tcG9uZW50XG4gIDwvZGl2PlxuPC90ZW1wbGF0ZT4ifQ==)

</div>

The above is made possible by Vue's `<component>` element with the special `is` attribute:
上面的效果是用 Vue 的`<component>` 元素与一个特殊的 attribute `is` 实现的

<div class="options-api">

```vue-html
<!-- currentTab 改变时组件跟着改变 -->
<component :is="currentTab"></component>
```

</div>
<div class="composition-api">

```vue-html
<!-- currentTab 改变时组件跟着改变 -->
<component :is="tabs[currentTab]"></component>
```

</div>

In the example above, the value passed to `:is` can contain either:

- the name string of a registered component, OR
- the actual imported component object

`:is` 的值可以是：
 - 一个已注册组件的名称字符串
 - 实际导入的组件对象

You can also use the `is` attribute to create regular HTML elements.

When switching between multiple components with `<component :is="...">`, a component will be unmounted when it is switched away from. We can force the inactive components to stay "alive" with the built-in [`<KeepAlive>` component](/guide/built-ins/keep-alive.html).

用 `<component :is="...">` 在多个组件之间切换时，被切走的组件会执行 unmounted 操作，我们用内建的 [`<KeepAlive>` 组件](/guide/built-ins/keep-alive.html)，强制让处于非激活状态的组件 stay alive。

## DOM Template Parsing Caveats
## DOM 模板解析限制

If you are writing your Vue templates directly in the DOM, Vue will have to retrieve the template string from the DOM. This leads to some caveats due to browsers' native HTML parsing behavior.
在 DOM 中直接写 Vue 模板的一些约束。

:::tip
It should be noted that the limitations discussed below only apply if you are writing your templates directly in the DOM. They do NOT apply if you are using string templates from the following sources:

- Single-File Components
- Inlined template strings (e.g. `template: '...'`)
- `<script type="text/x-template">`
  :::

### Case Insensitivity
### 不区分大小写

HTML 标签和 attribute 不区分大小写，浏览器把任何大写字母转成小写。
HTML tags and attribute names are case-insensitive, so browsers will interpret any uppercase characters as lowercase. That means when you’re using in-DOM templates, PascalCase component names and camelCased prop names or `v-on` event names all need to use their kebab-cased (hyphen-delimited) equivalents:

```js
// camelCase in JavaScript
const BlogPost = {
  props: ['postTitle'],
  emits: ['updatePost']
  template: `
    <h3>{{ postTitle }}</h3>
  `
}
```

```vue-html
<!-- kebab-case in HTML -->
<blog-post post-title="hello!" @update-post="onUpdatePost"></blog-post>
```

### Self Closing Tags
### 自闭合标签

Vue 模板中可用自闭合标签，DOM 模板则必须写成对的标签。
We have been using self-closing tags for components in previous code samples:

```vue-html
<MyComponent />
```

This is because Vue's template parser respects `/>` as an indication to end any tag, regardless of its type.

In DOM templates, however, we must always include explicit closing tags:

```vue-html
<my-component></my-component>
```

This is because the HTML spec only allows [a few specific elements](https://html.spec.whatwg.org/multipage/syntax.html#void-elements) to omit closing tags, the most common being `<input>` and `<img>`. For all other elements, if you omit the closing tag, the native HTML parser will think you never terminated the opening tag. For example, the following snippet:

```vue-html
<my-component /> <!-- we intend to close the tag here... -->
<span>hello</span>
```

will be parsed as:

```vue-html
<my-component>
  <span>hello</span>
</my-component> <!-- but the browser will close it here. -->
```

### Element Placement Restrictions
### 元素位置限制

Some HTML elements, such as `<ul>`, `<ol>`, `<table>` and `<select>` have restrictions on what elements can appear inside them, and some elements such as `<li>`, `<tr>`, and `<option>` can only appear inside certain other elements.

This will lead to issues when using components with elements that have such restrictions. For example:

```vue-html
<table>
  <blog-post-row></blog-post-row>
</table>
```

The custom component `<blog-post-row>` will be hoisted out as invalid content, causing errors in the eventual rendered output. We can use the special [`is` attribute](/api/built-in-special-attributes.html#is) as a workaround:

```vue-html
<table>
  <tr is="vue:blog-post-row"></tr>
</table>
```

:::tip
When used on native HTML elements, the value of `is` must be prefixed with `vue:` in order to be interpreted as a Vue component. This is required to avoid confusion with native [customized built-in elements](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example).
:::

That's all you need to know about DOM template parsing caveats for now - and actually, the end of Vue's _Essentials_. Congratulations! There's still more to learn, but first, we recommend taking a break to play with Vue yourself - build something fun, or check out some of the [Examples](/examples/) if you haven't.

Once you feel comfortable with the knowledge you've just digested, move on with the guide to learn more about components in depth.
