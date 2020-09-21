---
title: Слоти
type: guide
order: 104
---

> Даний розділ передбачає, що ви вже знайомі [компонентами](components.html). Прочитайте його, якщо це не так.

> Починаючи з 2.6.0, ми додали підтримку нового синтаксису (директиву `v-slot`) для іменованих слотів та слотів з обмеженою областю видимості. Це замінює атрибути `slot` та `slot-scope`, які вже зараз є застарілими, хоча й _не_ видаленими та досі задокументованими [тут](#Застарілий-синтаксис). Раціональність цього нового синтаксису описано в [RFC (англ.)](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md).

## Вміст слотів

Vue впроваджує API передачі вмісту, натхненного [проектом специфікації Веб Компонентів](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), використовуючи елемент `<slot>`, що слугує як вхід для прийому вмісту.

Це дозволяє створювати компоненти наступним чином:

``` html
<navigation-link url="/profile">
  Ваш профіль
</navigation-link>
```

Тепер, в шаблоні компонента `<navigation-link>`, ви можете мати:

``` html
<a
  v-bind:href="url"
  class="nav-link"
>
  <slot></slot>
</a>
```

Під час відмальовування компонента, `<slot></slot>` буде замінено на "Ваш профіль". Слоти можуть містити будь-який код, включаючи HTML:

``` html
<navigation-link url="/profile">
  <!-- Додає іконку Font Awesome -->
  <span class="fa fa-user"></span>
  Ваш профіль
</navigation-link>
```

Та навіть інші компоненти:

``` html
<navigation-link url="/profile">
  <!-- Використовувати компонент, щоб додати іконку -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Ваш профіль
</navigation-link>
```

Якщо шаблон `<navigation-link>` **не** містить елемент `<slot>`, будь-який вміст, переданий між його відкриваючим та закриваючим тегами буде відкинуто.

## Область видимості компіляції

Якщо ви хочете використовувати дані всередині слота, як, наприклад:

``` html
<navigation-link url="/profile">
  Увійшли як {{ user.name }}
</navigation-link>
```

Цей слот має доступ до тих ж самих властивостей екземпляру (тобто, та ж сама область видимості), як і решта шаблону. Слот *не** має доступу до області видимості `<navigation-link>`. Наприклад, спроба отримати доступ до `url` була б невдалою:

``` html
<navigation-link url="/profile">
  Натискаючи тут, ви будете переадресовані до: {{ url }}
  <!--
  Тут, `url` буде `undefined`, оскільки вміст було передано
  _до_ <navigation-link>, аніж було визначено _всередині_ самого
  компонента <navigation-link>.
  -->
</navigation-link>
```

Запам'ятайте це як правило:

> Все, що в батьківському шаблоні компілюється з батьківською областю видимості; все, що в дочірньому шаблоні компілюється з дочірньою областю видимості.

## Вміст за замовчуванням

Є випадки, коли корисно задавати певний вміст як значення за замовчуванням для слота, який буде відмальовано лише, якщо не було передано жодного вмісту. Наприклад, в компонента `<submit-button>`:

```html
<button type="submit">
  <slot></slot>
</button>
```

Ми, можливо, хотіли, щоб в більшості випадків текст "Надіслати" відмальовувався всередині `<button>`. Щоб зробити "Надіслати" вмістом за замовчуванням, ми можемо розмістити його між тегами `<slot>`:

```html
<button type="submit">
  <slot>Надіслати</slot>
</button>
```

Тепер, якщо не передавати ніякий вміст в батьківському компоненті при використанні `<submit-button>`:

```html
<submit-button></submit-button>
```

відмалюється вміст за замовчуванням, "Надіслати":

```html
<button type="submit">
  Надіслати
</button>
```

Але якщо ж ми передаємо вміст:

```html
<submit-button>
  Зберегти
</submit-button>
```

Тоді буде відмальовано переданий вміст:

```html
<button type="submit">
  Зберегти
</button>
```

## Іменовані слоти

> Updated in 2.6.0+. [See here](#Застарілий-синтаксис) for the deprecated syntax using the `slot` attribute.

There are times when it's useful to have multiple slots. For example, in a `<base-layout>` component with the following template:

``` html
<div class="container">
  <header>
    <!-- We want header content here -->
  </header>
  <main>
    <!-- We want main content here -->
  </main>
  <footer>
    <!-- We want footer content here -->
  </footer>
</div>
```

For these cases, the `<slot>` element has a special attribute, `name`, which can be used to define additional slots:

``` html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

A `<slot>` outlet without `name` implicitly has the name "default".

To provide content to named slots, we can use the `v-slot` directive on a `<template>`, providing the name of the slot as `v-slot`'s argument:

```html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

Now everything inside the `<template>` elements will be passed to the corresponding slots. Any content not wrapped in a `<template>` using `v-slot` is assumed to be for the default slot.

However, you can still wrap default slot content in a `<template>` if you wish to be explicit:

```html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

Either way, the rendered HTML will be:

``` html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

Note that **`v-slot` can only be added to a `<template>`** (with [one exception](#Абревіатурний-синтаксис-для-відокремлених-слотів-за-замовчуванням)), unlike the deprecated [`slot` attribute](#Застарілий-синтаксис).

## Слоти з обмеженою зоною видимості

> Updated in 2.6.0+. [See here](#Застарілий-синтаксис) for the deprecated syntax using the `slot-scope` attribute.

Sometimes, it's useful for slot content to have access to data only available in the child component. For example, imagine a `<current-user>` component with the following template:

```html
<span>
  <slot>{{ user.lastName }}</slot>
</span>
```

We might want to replace this fallback content to display the user's first name, instead of last, like this:

``` html
<current-user>
  {{ user.firstName }}
</current-user>
```

That won't work, however, because only the `<current-user>` component has access to the `user` and the content we're providing is rendered in the parent.

To make `user` available to the slot content in the parent, we can bind `user` as an attribute to the `<slot>` element:

``` html
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
```

Attributes bound to a `<slot>` element are called **slot props**. Now, in the parent scope, we can use `v-slot` with a value to define a name for the slot props we've been provided:

``` html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

In this example, we've chosen to name the object containing all our slot props `slotProps`, but you can use any name you like.

### Абревіатурний синтаксис для відокремлених слотів за замовчуванням

In cases like above, when _only_ the default slot is provided content, the component's tags can be used as the slot's template. This allows us to use `v-slot` directly on the component:

``` html
<current-user v-slot:default="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

This can be shortened even further. Just as non-specified content is assumed to be for the default slot, `v-slot` without an argument is assumed to refer to the default slot:

``` html
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

Note that the abbreviated syntax for default slot **cannot** be mixed with named slots, as it would lead to scope ambiguity:

``` html
<!-- INVALID, will result in warning -->
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps">
    slotProps is NOT available here
  </template>
</current-user>
```

Whenever there are multiple slots, use the full `<template>` based syntax for _all_ slots:

``` html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>

  <template v-slot:other="otherSlotProps">
    ...
  </template>
</current-user>
```

### Деструктуризування вхідних параметрів слота

Internally, scoped slots work by wrapping your slot content in a function passed a single argument:

```js
function (slotProps) {
  // ... slot content ...
}
```

That means the value of `v-slot` can actually accept any valid JavaScript expression that can appear in the argument position of a function definition. So in supported environments ([single-file components](single-file-components.html) or [modern browsers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)), you can also use [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) to pull out specific slot props, like so:

``` html
<current-user v-slot="{ user }">
  {{ user.firstName }}
</current-user>
```

This can make the template much cleaner, especially when the slot provides many props. It also opens other possibilities, such as renaming props, e.g. `user` to `person`:

``` html
<current-user v-slot="{ user: person }">
  {{ person.firstName }}
</current-user>
```

You can even define fallbacks, to be used in case a slot prop is undefined:

``` html
<current-user v-slot="{ user = { firstName: 'Guest' } }">
  {{ user.firstName }}
</current-user>
```

## Динамічні імена слотів

> New in 2.6.0+

[Dynamic directive arguments](syntax.html#Dynamic-Arguments) also work on `v-slot`, allowing the definition of dynamic slot names:

``` html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

## Скорочення іменованих слотів

> New in 2.6.0+

Similar to `v-on` and `v-bind`, `v-slot` also has a shorthand, replacing everything before the argument (`v-slot:`) with the special symbol `#`. For example, `v-slot:header` can be rewritten as `#header`:

```html
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

However, just as with other directives, the shorthand is only available when an argument is provided. That means the following syntax is invalid:

``` html
<!-- This will trigger a warning -->
<current-user #="{ user }">
  {{ user.firstName }}
</current-user>
```

Instead, you must always specify the name of the slot if you wish to use the shorthand:

``` html
<current-user #default="{ user }">
  {{ user.firstName }}
</current-user>
```

## Інші приклади

**Slot props allow us to turn slots into reusable templates that can render different content based on input props.** This is most useful when you are designing a reusable component that encapsulates data logic while allowing the consuming parent component to customize part of its layout.

For example, we are implementing a `<todo-list>` component that contains the layout and filtering logic for a list:

```html
<ul>
  <li
    v-for="todo in filteredTodos"
    v-bind:key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```

Instead of hard-coding the content for each todo, we can let the parent component take control by making every todo a slot, then binding `todo` as a slot prop:

```html
<ul>
  <li
    v-for="todo in filteredTodos"
    v-bind:key="todo.id"
  >
    <!--
    We have a slot for each todo, passing it the
    `todo` object as a slot prop.
    -->
    <slot name="todo" v-bind:todo="todo">
      <!-- Fallback content -->
      {{ todo.text }}
    </slot>
  </li>
</ul>
```

Now when we use the `<todo-list>` component, we can optionally define an alternative `<template>` for todo items, but with access to data from the child:

```html
<todo-list v-bind:todos="todos">
  <template v-slot:todo="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

However, even this barely scratches the surface of what scoped slots are capable of. For real-life, powerful examples of scoped slot usage, we recommend browsing libraries such as [Vue Virtual Scroller](https://github.com/Akryum/vue-virtual-scroller), [Vue Promised](https://github.com/posva/vue-promised), and [Portal Vue](https://github.com/LinusBorg/portal-vue).

## Застарілий синтаксис

> The `v-slot` directive was introduced in Vue 2.6.0, offering an improved, alternative API to the still-supported `slot` and `slot-scope` attributes. The full rationale for introducing `v-slot` is described in this [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md). The `slot` and `slot-scope` attributes will continue to be supported in all future 2.x releases, but are officially deprecated and will eventually be removed in Vue 3.

### Іменовані слоти з атрибутом `slot`

> <abbr title="Still supported in all 2.x versions of Vue, but no longer recommended.">Deprecated</abbr> in 2.6.0+. See [here](#Іменовані-слоти) for the new, recommended syntax.

To pass content to named slots from the parent, use the special `slot` attribute on `<template>` (using the `<base-layout>` component described [here](#Іменовані-слоти) as example):

```html
<base-layout>
  <template slot="header">
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template slot="footer">
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

Or, the `slot` attribute can also be used directly on a normal element:

``` html
<base-layout>
  <h1 slot="header">Here might be a page title</h1>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <p slot="footer">Here's some contact info</p>
</base-layout>
```

There can still be one unnamed slot, which is the **default slot** that serves as a catch-all for any unmatched content. In both examples above, the rendered HTML would be:

``` html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

### Слоти обмеженої зони видимості з атрибутом `slot-scope`

> <abbr title="Still supported in all 2.x versions of Vue, but no longer recommended.">Deprecated</abbr> in 2.6.0+. See [here](#Слоти-з-обмеженою-зоною-видимості) for the new, recommended syntax.

To receive props passed to a slot, the parent component can use `<template>` with the `slot-scope` attribute (using the `<slot-example>` described [here](#Слоти-з-обмеженою-зоною-видимості) as example):

``` html
<slot-example>
  <template slot="default" slot-scope="slotProps">
    {{ slotProps.msg }}
  </template>
</slot-example>
```

Here, `slot-scope` declares the received props object as the `slotProps` variable, and makes it available inside the `<template>` scope. You can name `slotProps` anything you like similar to naming function arguments in JavaScript.

Here `slot="default"` can be omitted as it is implied:

``` html
<slot-example>
  <template slot-scope="slotProps">
    {{ slotProps.msg }}
  </template>
</slot-example>
```

The `slot-scope` attribute can also be used directly on a non-`<template>` element (including components):

``` html
<slot-example>
  <span slot-scope="slotProps">
    {{ slotProps.msg }}
  </span>
</slot-example>
```

The value of `slot-scope` can accept any valid JavaScript expression that can appear in the argument position of a function definition. This means in supported environments ([single-file components](single-file-components.html) or [modern browsers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)) you can also use [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) in the expression, like so:

``` html
<slot-example>
  <span slot-scope="{ msg }">
    {{ msg }}
  </span>
</slot-example>
```

Using the `<todo-list>` described [here](#Інші-приклади) as an example, here's the equivalent usage using `slot-scope`:

``` html
<todo-list v-bind:todos="todos">
  <template slot="todo" slot-scope="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```
