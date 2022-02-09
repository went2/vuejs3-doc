# 创建 Vue 应用 {#creating-a-vue-application}

## 应用实例{#the-application-instance}

Vue 应用始于 [`createApp`](/api/application#createapp) 函数创建的 **应用实例**：

```js
import { createApp } from 'vue'

const app = createApp({
  /* 根组件选项 */
})
```

## 根组件{#the-root-component}

我们传入 `createApp` 函数的参数对象，就叫一个组件。每个 Vue 应用必须有一个“根组件”来包容其他组件，其他组件叫根组件的子组件。

如果用的是单文件组件，则通常从其他文件中导入根组件。

```js
import { createApp } from 'vue'
// 从单文件组件 App 中导入根组件
import App from './App.vue'

const app = createApp(App)
```

尽管本指南中的很多示例只需用到一个组件，大多数真实的应用都会组织成一棵由可重用的组件嵌套形成的树结构。例如，一个 Todo 应用的组件树可能如下：

```
App (根组件)
├─ TodoList
│  └─ TodoItem
│     ├─ TodoDeleteButton
│     └─ TodoEditButton
└─ TodoFooter
   ├─ TodoClearButton
   └─ TodoStatistics
```

指南的后续章节会讨论组件的定义与组合，在那之前，我们先关注一个组件内部发生的事情。

## 挂载应用 {#mounting-the-app}

我们必须调用应用实例的 `.mount()` 方法才能将它渲染出来，该方法接收一个“容器”参数，容器参数可以是实际的 DOM 元素或者 CSS 选择器字符串：

```html
<div id="app"></div>
```

```js
app.mount('#app')
```

应用根组件的内容会渲染到容器元素里面，容器元素本身不属于 Vue 应用。

在整个应用的配置(app configurations)及资源注册(asset registrations)完成后，应当随即调用 `.mount()` 方法。注意该方法的返回值——不像资源注册方法——是一个根组件实例而不是应用实例。

### DOM 中的根组件模板{#in-dom-root-component-template}

在未使用构建流程的情况下使用 Vue 时，可以把根组件的模板直接写到挂载的根组件元素中：

```html
<div id="app">
  <button @click="count++">{{ count }}</button>
</div>
```

```js
import { createApp } from 'vue'

const app = createApp({
  data() {
    return {
      count: 0
    }
  }
})

app.mount('#app')
```

如果根组件没有设置 `template` 选项，Vue 会自动将容器元素 `innerHTML` 属性的值作为模板。 

## 应用配置{#app-configurations}

我们可以通过应用实例暴露出的 `.config` 对象来配置一些应用级别的选项，如定义一个应用级别的应用处理器，来捕获所有子组件上抛而未处理的错误：

```js
app.config.errorHandler = (err) => {
  /* handle error */
}
```

应用实例还提供了一些方法来注册应用范围内可用的资源，例如用 `.component` 注册一个组件：

```js
app.component('TodoDeleteButton', TodoDeleteButton)
```

这让我们可以在应用内部的任意地方使用 `TodoDeleteButton`。指南的后续章节会讨论关于组件和其他资源的注册。你也可以在 [API 参考](/api/application)中浏览全部的应用实例 API。

确保在挂载应用实例之前完成所有应用配置！

## 多个应用实例{#multiple-application-instances}

你可以在同一个页面中使用多个应用实例，`createApp` API 允许多个 Vue 应用共存于同一个页面上，每个应用都有独立的作用域，来处理配置和全局资源：

```js
const app1 = createApp({
  /* ... */
})
app1.mount('#container-1')

const app2 = createApp({
  /* ... */
})
app2.mount('#container-2')
```

如果你将 Vue 用于增强服务端渲染 HTML，并且只需 Vue 来控制大型页面中的一些具体部分，应避免用一个 Vue 应用实例挂载到整个页面的做法，应该创建多个小规模的应用实例，将它们分别挂载到所需的元素上去。

## 小结

- Vue 应用始于一个 **应用实例**。
- 创建应用实例需要一个根组件。
- 应用实例可进行配置与资源注册(app configurations and assets registration)。
- 应用实例需要挂载到 DOM 元素上。
