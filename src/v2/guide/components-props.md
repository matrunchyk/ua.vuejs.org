---
title: Вхідні параметри
type: guide
order: 102
---

> Цей розділ передбачає ваше розуміння [базових знань компонентів](components.html). Прочитайте його перед тим, як знайомитися з цим розділом.

<div class="vueschool"><a href="https://vueschool.io/lessons/reusable-components-with-props?friend=vuejs" target="_blank" rel="sponsored noopener" title="Дізнайтеся, як працюють вхідні параметри у Vue School">Дізнайтеся, як працюють вхідні параметри на безкоштовному уроці у Vue School</a></div>

## Регістр вхідних параметрів (camelCase чи kebab-case)

Імена HTML атрибутів не залежать від регістру, тому браузери розумітимуть будь-які символи ви верхньому регістрі як символи в нижньому. Це означає, що коли ви використовуєте шаблони, створені як частина DOM, імена властивостей, написані в стилі camelCase мають бути використані їхні kebab-case еквіваленти (розділені через дефіс):

``` js
Vue.component('blog-post', {
  // camelCase в JavaScript
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```

``` html
<!-- kebab-case в HTML -->
<blog-post post-title="hello!"></blog-post>
```

Знову ж таки, ці обмеження не впливають, якщо ви використовуєте рядкові шаблони.

## Типи вхідних параметрів

До цього моменту, ми тільки розглядали вхідні параметри, перелічені як масив рядкових величин:

```js
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```

Проте, зазвичай ви б хотіли, щоб кожен вхідний параметр мав якийсь специфічний тип очікуваного значення. В таких випадках ви можете перелічити вхідні параметри у вигляді об'єкту, де імена його властивостей та значень матимуть відповідно їхні імена та їхні типи:

```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // або будь-який інший конструктор
}
```

Це не лише документує ваш компонент, але також попередить користувачів в консолі JavaScript браузера, якщо вони передадуть невірний тип. Ви дізнаєтесь більше про [перевірку типів та іншу валідацію вхідних параметрів](#Валідація-вхідних-параметрів) на цій сторінці трохи нижче.

## Передача статичних або динамічних вхідних параметрів

До цього моменту, ви тільки спостерігали за вхідними параметрами, переданими як статичне значення, наприклад:

```html
<blog-post title="Мій шлях з Vue"></blog-post>
```

Ми також бачили вхідні параметри, передані динамічно за допомогою `v-bind`, як в наступному прикладі:

```html
<!-- Динамічна передача значення через змінну -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- Динамічне передача значення через складний вираз -->
<blog-post
  v-bind:title="post.title + ', автор: ' + post.author.name"
></blog-post>
```

В цих двох прикладах, що зверху, нам вдавалося передавати рядкові значення, але насправді _будь-які_ типи значення можуть передані до вхідного параметра.

### Передача числа

```html
<!-- Хоч `42` є статичним, ми повинні використовувати v-bind, щоб вказати Vue, що -->
<!-- це є виразом JavaScript, а не рядкове значення. -->
<blog-post v-bind:likes="42"></blog-post>

<!-- Динамічна передача значення через змінну. -->
<blog-post v-bind:likes="post.likes"></blog-post>
```

### Передача булевого значення

```html
<!-- Додавання булевого вхідного параметру без значення означатиме булеве значення `true`. -->
<blog-post is-published></blog-post>

<!-- Хоча `false` і статичне, ми повинні використовувати v-bind, щоб вказати Vue, що -->
<!-- це є виразом JavaScript, а не рядкове значення. -->
<blog-post v-bind:is-published="false"></blog-post>

<!-- Динамічна передача значення через змінну. -->
<blog-post v-bind:is-published="post.isPublished"></blog-post>
```

### Передача масиву

```html
<!-- Хоча масив і статичний, ми повинні використовувати v-bind, щоб вказати Vue, що -->
<!-- це є виразом JavaScript, а не рядкове значення. -->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>

<!-- Динамічна передача значення через змінну. -->
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>
```

### Передача об'єкту

```html
<!-- Хоча об'єкт і статичний, ми повинні використовувати v-bind, щоб вказати Vue, що -->
<!-- це є виразом JavaScript, а не рядкове значення. -->
<blog-post
  v-bind:author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
></blog-post>

<!-- Динамічна передача значення через змінну. -->
<blog-post v-bind:author="post.author"></blog-post>
```

### Передача властивостей об'єкту

Якщо ви хотіли б передати всі властивості об'єкту, як вхідні параметри, ви можете скористатися `v-bind` без аргументу (`v-bind` замість `v-bind:prop-name`). Для прикладу, в нас є об'єкт `post`:

``` js
post: {
  id: 1,
  title: 'Мій шлях з Vue'
}
```

Наступний шаблон:

``` html
<blog-post v-bind="post"></blog-post>
```

Еквівалентний наступному:

``` html
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title"
></blog-post>
```

## Односторонній перебіг даних

Всі вхідні параметри формують **односторонній зв'язок** між дочірніми властивостями та батьківським: коли батьківська властивість змінюється, це перейде вниз аж до дочірніх, але не навпаки. Це запобігає дочірні компоненти випадково мутувати стан батьківських, щоб могло б зробити перебіг даних вашого застосунку складнішим для розуміння.

Крім того, щоразу, коли батьківський компонент оновлено, всі вхідні параметри дочірнього компоненту будуть оновлені автоматично новими значеннями. Це означає, що ви **не** повинні намагатися мутувати вхідний параметр усередині дочірньої компоненти. Якщо ж ви спробуєте це зробити, Vue попередить вас в консолі розробника.

Як правило, існує два випадки, де хочеться змінити вхідний параметр:

1. **The prop is used to pass in an initial value; the child component wants to use it as a local data property afterwards.** In this case, it's best to define a local data property that uses the prop as its initial value:

  ``` js
  props: ['initialCounter'],
  data: function () {
    return {
      counter: this.initialCounter
    }
  }
  ```

2. **The prop is passed in as a raw value that needs to be transformed.** In this case, it's best to define a computed property using the prop's value:

  ``` js
  props: ['size'],
  computed: {
    normalizedSize: function () {
      return this.size.trim().toLowerCase()
    }
  }
  ```

<p class="tip">Note that objects and arrays in JavaScript are passed by reference, so if the prop is an array or object, mutating the object or array itself inside the child component **will** affect parent state.</p>

## Prop Validation

Components can specify requirements for their props, such as the types you've already seen. If a requirement isn't met, Vue will warn you in the browser's JavaScript console. This is especially useful when developing a component that's intended to be used by others.

To specify prop validations, you can provide an object with validation requirements to the value of `props`, instead of an array of strings. For example:

``` js
Vue.component('my-component', {
  props: {
    // Basic type check (`null` and `undefined` values will pass any type validation)
    propA: Number,
    // Multiple possible types
    propB: [String, Number],
    // Required string
    propC: {
      type: String,
      required: true
    },
    // Number with a default value
    propD: {
      type: Number,
      default: 100
    },
    // Object with a default value
    propE: {
      type: Object,
      // Object or array defaults must be returned from
      // a factory function
      default: function () {
        return { message: 'hello' }
      }
    },
    // Custom validator function
    propF: {
      validator: function (value) {
        // The value must match one of these strings
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```

When prop validation fails, Vue will produce a console warning (if using the development build).

<p class="tip">Note that props are validated **before** a component instance is created, so instance properties (e.g. `data`, `computed`, etc) will not be available inside `default` or `validator` functions.</p>

### Type Checks

The `type` can be one of the following native constructors:

- String
- Number
- Boolean
- Array
- Object
- Date
- Function
- Symbol

In addition, `type` can also be a custom constructor function and the assertion will be made with an `instanceof` check. For example, given the following constructor function exists:

```js
function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```

You could use:

```js
Vue.component('blog-post', {
  props: {
    author: Person
  }
})
```

to validate that the value of the `author` prop was created with `new Person`.

## Non-Prop Attributes

A non-prop attribute is an attribute that is passed to a component, but does not have a corresponding prop defined.

While explicitly defined props are preferred for passing information to a child component, authors of component libraries can't always foresee the contexts in which their components might be used. That's why components can accept arbitrary attributes, which are added to the component's root element.

For example, imagine we're using a 3rd-party `bootstrap-date-input` component with a Bootstrap plugin that requires a `data-date-picker` attribute on the `input`. We can add this attribute to our component instance:

``` html
<bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
```

And the `data-date-picker="activated"` attribute will automatically be added to the root element of `bootstrap-date-input`.

### Replacing/Merging with Existing Attributes

Imagine this is the template for `bootstrap-date-input`:

``` html
<input type="date" class="form-control">
```

To specify a theme for our date picker plugin, we might need to add a specific class, like this:

``` html
<bootstrap-date-input
  data-date-picker="activated"
  class="date-picker-theme-dark"
></bootstrap-date-input>
```

In this case, two different values for `class` are defined:

- `form-control`, which is set by the component in its template
- `date-picker-theme-dark`, which is passed to the component by its parent

For most attributes, the value provided to the component will replace the value set by the component. So for example, passing `type="text"` will replace `type="date"` and probably break it! Fortunately, the `class` and `style` attributes are a little smarter, so both values are merged, making the final value: `form-control date-picker-theme-dark`.

### Disabling Attribute Inheritance

If you do **not** want the root element of a component to inherit attributes, you can set `inheritAttrs: false` in the component's options. For example:

```js
Vue.component('my-component', {
  inheritAttrs: false,
  // ...
})
```

This can be especially useful in combination with the `$attrs` instance property, which contains the attribute names and values passed to a component, such as:

```js
{
  required: true,
  placeholder: 'Enter your username'
}
```

With `inheritAttrs: false` and `$attrs`, you can manually decide which element you want to forward attributes to, which is often desirable for [base components](../style-guide/#Base-component-names-strongly-recommended):

```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      >
    </label>
  `
})
```

<p class="tip">Note that `inheritAttrs: false` option does **not** affect `style` and `class` bindings.</p>

This pattern allows you to use base components more like raw HTML elements, without having to care about which element is actually at its root:

```html
<base-input
  v-model="username"
  required
  placeholder="Enter your username"
></base-input>
```
