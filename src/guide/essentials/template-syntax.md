# 模板语法 {#template-syntax}

Vue 使用一种基于 HTML 的模板语法，使开发者可以声明式地将已渲染的 DOM 绑定到底层组件实例的数据。所有 Vue 的模板都是合法的 HTML，能被与标准兼容的浏览器或 HTML 解析器解析。

Vue 会在底层将模板编译成高度优化后的 JavaScript 代码，结合响应式系统，当应用的状态改变时，Vue 能智能地计算出需要重新渲染的组件的最小数量，并在执行最少次数的 DOM 操作。

如果你熟悉虚拟 DOM 概念并偏好原生 JavaScript 的能力，你可以不用模板，[直接写渲染函数](/guide/extras/render-function.html)，选用 JSX 语法。但请注意它们并不与模板共享相同级别的编译时优化。

## 文本插值 {#text-interpolation}

最基础的数据绑定形式是用 "Mustache" 语法(即双大括号)<sup>[[1]](#footnote-1)</sup>进行文本插值操作：

```vue-html
<span>Message: {{ msg }}</span>
```

mustache 标签中的内容会被替换成对应组件实例上的 `msg` 属性的值，无论何时`msg` 属性发生变化，插值处的内容会同步更新。

<small>
<div id="footnote-1">[1] Mustache 是一种轻逻辑的模板语法，用于创建动态内容，如 HTML、配置文件等。它有不同语言的实现，参考 js 的实现 [mustache.js](https://github.com/janl/mustache.js/)。</div>
</small>

## 原生 HTML {#raw-html}

双大括号会把里面的数据当作纯文本而不是 HTML 处理，如果要输出真正的 HTML，需要使用[`v-html` 指令](/api/built-in-directives.html#v-html):

```vue-html
<p>使用文本插值: {{ rawHtml }}</p>
<p>使用 v-html 指令: <span v-html="rawHtml"></span></p>
```

<script setup>
  const rawHtml = '<span style="color: red">This should be red.</span>'
</script>

<div class="demo">
  <p>使用文本插值: {{ rawHtml }}</p>
  <p>使用 v-html 指令: <span v-html="rawHtml"></span></p>
</div>

这里出现了些新东西，这个 `v-html` 属性叫做 **指令**， 指令是 Vue 提供的以 `v-` 开头的特殊属性，你可能可以猜到，它们对渲染出来的 DOM 施加特殊的响应式行为。这里我们做的事情是：将 span 元素的 innerHTML 属性的值与当前活跃实例的 `rawHtml` 属性保持一致。

`span` 元素的内容会被替换为 `rawHtml` 属性的值，插值为纯 HTML，数据绑定将会被忽略。注意你不能通过拼接 `v-html` 来组合出一个模板来，因为 Vue 不是基于字符串的模板引擎，应该使用组件作为 UI 重用和组合的基本单元。

:::warning 安全警告
将任意 HTML 动态渲染到你的网站上是非常危险的，这非常容易造成 [XSS 漏洞](https://en.wikipedia.org/wiki/Cross-site_scripting)。只对受信的内容使用 `v-html`，**绝对不要**将它用在用户输入的内容上。
:::

## 属性的绑定 {#attribute-bindings}

Mustaches 语法无法作用到 HTML 属性上，遇到这种情况使用 [`v-bind` 指令](/api/built-in-directives.html#v-bind):

```vue-html
<div v-bind:id="dynamicId"></div>
```

`v-bind` 指令告诉 Vue 将 div 元素的 `id` 属性和组件的 `dynamicId` 属性的值保持一致。如果所绑订的值是 `null` 或 `undefined`, 则 `id` 属性会从最终渲染出来的元素中移除。

### 缩写 {#shorthand}

由于 `v-bind` 特别常用，它有特定的缩写语法：

```vue-html
<div :id="dynamicId"></div>
```

以 `:` 开头的属性看起来和一般的 HTML 属性不同，但它也是合法的 HTML 属性名，能被所有支持 Vue 的浏览器正确解析。此外，它们不会出现在最终渲染出来的标签中。缩写语法是可选的，随着你深入了解它的作用，你会庆幸拥有它。

> 在接下来的指南中，我们将在示例代码中使用缩写语法，这也是大多数 Vue 开发者的用法。

### 布尔值属性 {#boolean-attributes}

[布尔值属性](https://html.spec.whatwg.org/multipage/common-microsyntaxes.html#boolean-attributes) 是通过其在元素上的出现与否来表示值为 true / false 的属性<sup>[[2]](#footnote-2)</sup>。例如, [`disabled`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/disabled) 是最常用的布尔值属性之一。

`v-bind` 的使用有一点不同：

```vue-html
<button :disabled="isButtonDisabled">Button</button>
```

如果 `isButtonDisabled` 的值为 [真值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)或空字符串(即 `<button disabled="">`)时，`disabled` 属性会出现在元素上<sup>[[3]](#footnote-3)</sup>，如果它的值为假值，则 `disabled` 会被忽略。

<small>
译者注：
<div id="footnote-2">[2] 出现该属性表示该属性的值为 true</div>
<div id="footnote-3">[3] 同时 <button /> 上出现 disabled 表示该标签的 disabled 属性为 true</div>
</small>

### 动态绑定多个属性 {#dynamically-binding-multiple-attributes}

如果你想用一个 JavaScript 对象来表示多个属性，如：

<div class="composition-api">

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper'
}
```

</div>
<div class="options-api">

```js
data() {
  return {
    objectOfAttrs: {
      id: 'container',
      class: 'wrapper'
    }
  }
}
```

</div>

你可以用不带参数的 `v-bind` 把它们绑定到一个元素上：

```vue-html
<div v-bind="objectOfAttrs"></div>
```

## 使用 JavaScript 表达式 {#using-javascript-expressions}

目前为止我们仅
So far we've only been binding to simple property keys in our templates. But Vue actually supports the full power of JavaScript expressions inside all data bindings:

```vue-html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

These expressions will be evaluated as JavaScript in the data scope of the current component instance.

In Vue templates, JavaScript expressions can be used in the following positions:

- Inside text interpolations (mustaches)
- In the attribute value of any Vue directives (special attributes that start with `v-`)

### Expressions Only

Each binding can only contain **one single expression**, so the following will **NOT** work:

```vue-html
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```

### Calling Functions

It is possible to call a component-exposed method inside a binding expression:

```vue-html
<span :title="toTitleDate(date)">
  {{ formatDate(date) }}
</span>
```

:::tip
Functions called inside binding expressions will be called every time the component updates, so they should **not** have any side effects, such as changing data or triggering asynchronous operations.
:::

### Restricted Globals Access

Template expressions are sandboxed and only have access to a [restricted list of globals](https://github.com/vuejs/core/blob/main/packages/shared/src/globalsWhitelist.ts#L3). The list exposes commonly used built-in globals such as `Math` and `Date`.

Globals not explicitly included in the list, for example user-attached properties on `window`, will not be accessible in template expressions. You can, however, explicitly define additional globals for all Vue expressions by adding them to [`app.config.globalProperties`](/api/application.html#app-config-globalproperties).

## Directives

Directives are special attributes with the `v-` prefix. Vue provides a number of [built-in directives](/api/built-in-directives.html), including `v-html` and `v-bind` which we have introduced above.

Directive attribute values are expected to be single JavaScript expressions (with the exception of `v-for`, `v-on` and `v-slot`, which will be discussed in their respective sections later). A directive's job is to reactively apply updates to the DOM when the value of its expression changes. Take [`v-if`](/api/built-in-directives.html#v-if) as an example:

```vue-html
<p v-if="seen">Now you see me</p>
```

Here, the `v-if` directive would remove / insert the `<p>` element based on the truthiness of the value of the expression `seen`.

### Arguments

Some directives can take an "argument", denoted by a colon after the directive name. For example, the `v-bind` directive is used to reactively update an HTML attribute:

```vue-html
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```

Here `href` is the argument, which tells the `v-bind` directive to bind the element's `href` attribute to the value of the expression `url`. In the shorthand, everything before the argument (i.e. `v-bind:`) is condensed into a single character, `:`.

Another example is the `v-on` directive, which listens to DOM events:

```vue-html
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

Here the argument is the event name to listen to: `click`. `v-on` is one of the few directives that also have a corresponding shorthand, with its shorthand character being `@`. We will talk about event handling in more detail too.

### Dynamic Arguments

It is also possible to use a JavaScript expression in a directive argument by wrapping it with square brackets:

```vue-html
<!--
Note that there are some constraints to the argument expression,
as explained in the "Dynamic Argument Expression Constraints" section below.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- shorthand -->
<a :[attributeName]="url"> ... </a>
```

Here `attributeName` will be dynamically evaluated as a JavaScript expression, and its evaluated value will be used as the final value for the argument. For example, if your component instance has a data property, `attributeName`, whose value is `"href"`, then this binding will be equivalent to `v-bind:href`.

Similarly, you can use dynamic arguments to bind a handler to a dynamic event name:

```vue-html
<a v-on:[eventName]="doSomething"> ... </a>

<!-- shorthand -->
<a @[eventName]="doSomething">
```

In this example, when `eventName`'s value is `"focus"`, `v-on:[eventName]` will be equivalent to `v-on:focus`.

#### Dynamic Argument Value Constraints

Dynamic arguments are expected to evaluate to a string, with the exception of `null`. The special value `null` can be used to explicitly remove the binding. Any other non-string value will trigger a warning.

#### Dynamic Argument Syntax Constraints

Dynamic argument expressions have some syntax constraints because certain characters, such as spaces and quotes, are invalid inside HTML attribute names. For example, the following is invalid:

```vue-html
<!-- This will trigger a compiler warning. -->
<a :['foo' + bar]="value"> ... </a>
```

If you need to pass a complex dynamic argument, it's probably better to use to a [computed property](./computed.html), which we will cover shortly.

When using in-DOM templates (templates directly written in an HTML file), you should also avoid naming keys with uppercase characters, as browsers will coerce attribute names into lowercase:

```vue-html
<a :[someAttr]="value"> ... </a>
```

The above will be converted to `:[someattr]` in in-DOM templates.
If your component has a "someAttr" property instead of "someattr",
your code won't work.

### Modifiers

Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bound in some special way. For example, the `.prevent` modifier tells the `v-on` directive to call `event.preventDefault()` on the triggered event:

```vue-html
<form @submit.prevent="onSubmit">...</form>
```

You'll see other examples of modifiers later, [for `v-on`](./event-handling.html#event-modifiers) and [for `v-model`](./forms.html#modifiers), when we explore those features.

And finally, here's the full directive syntax visualized:

![directive syntax graph](./images/directive.png)

<!-- https://www.figma.com/file/BGWUknIrtY9HOmbmad0vFr/Directive -->

