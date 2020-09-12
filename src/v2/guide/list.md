---
title: Списки
type: guide
order: 8
---

<div class="vueschool"><a href="https://vueschool.io/lessons/vuejs-loops?friend=vuejs" target="_blank" rel="sponsored noopener" title="Дізнайтеся, як відмальовувати списки у Vue School">Дізнайтеся, як відмальовувати списки на безкоштовному уроці у Vue School</a></div>


## Зіставлення масиву з елементами за допомогою `v-for`

Ви можете скористатися директивою `v-for` для відмальовування елементів списку на основі масиву. Директива `v-for` вимагає спеціального синтаксису у формі `item in items`, де `items` є вихідним масивом даних, та `item` є **псевдонімом** для масиву ітерованих елементів:

``` html
<ul id="example-1">
  <li v-for="item in items" :key="item.message">
    {{ item.message }}
  </li>
</ul>
```

``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Фуу' },
      { message: 'Бар' }
    ]
  }
})
```

Результат:

{% raw %}
<ul id="example-1" class="demo">
  <li v-for="item in items" :key="item.message">
    {{item.message}}
  </li>
</ul>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Фуу' },
      { message: 'Бар' }
    ]
  }
})
</script>
{% endraw %}

Всередині блоків `v-for` ми маємо повний доступ до властивостей батьківської області видимості. Також, `v-for` підтримує необов'язковий другий аргумент для індексації поточного елементу списку.

``` html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Батьківська область видимості',
    items: [
      { message: 'Фуу' },
      { message: 'Бар' }
    ]
  }
})
```

Результат:

{% raw%}
<ul id="example-2" class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Батьківська область видимості',
    items: [
      { message: 'Фуу' },
      { message: 'Бар' }
    ]
  }
})
</script>
{% endraw %}

Ви також можете використовувати `of` як розділювач замість `in`, що дуже схоже до синтаксису ітерацій JavaScript:

``` html
<div v-for="item of items"></div>
```

## `v-for` з об'єктом

Ви також можете використовувати `v-for` для ітерацій по властивостях об'єкту:

``` html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

``` js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'Як зробити списки на Vue',
      author: 'Сергій Іванович',
      publishedAt: '2020-09-11'
    }
  }
})
```

Результат:

{% raw %}
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
<script>
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'Як зробити списки на Vue',
      author: 'Сергій Іванович',
      publishedAt: '2020-09-11'
    }
  }
})
</script>
{% endraw %}

Ви також можете вказати другий аргумент для імені властивості (також відомий як ключ):

``` html
<div v-for="(value, name) in object">
  {{ name }}: {{ value }}
</div>
```

{% raw %}
<div id="v-for-object-value-name" class="demo">
  <div v-for="(value, name) in object">
    {{ name }}: {{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object-value-name',
  data: {
    object: {
      title: 'Як зробити списки на Vue',
      author: 'Сергій Іванович',
      publishedAt: '2020-09-11'
    }
  }
})
</script>
{% endraw %}

І ще один приклад з індексацією:

``` html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

{% raw %}
<div id="v-for-object-value-name-index" class="demo">
  <div v-for="(value, name, index) in object">
    {{ index }}. {{ name }}: {{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object-value-name-index',
  data: {
    object: {
      title: 'Як зробити списки на Vue',
      author: 'Сергій Іванович',
      publishedAt: '2020-09-11'
    }
  }
})
</script>
{% endraw %}

<p class="tip">При ітерації властивостей об'єкта, порядок оснований на порядку отримання його ключів через `Object.keys()`, що **не гарантує** консистентність в різних імплементаціях JavaScript двигунів.</p>

## Підтримка стану

Коли Vue оновлює список елементів, відмальованих за допомогою `v-for`, за замовчуванням він використовує так звану стратегію "заміна на місці" ("in-place patch"). В тому порядку, в якому елементи були змінені, замість того, щоб переміщати елементи DOM для збереження порядку елементів списку, Vue змінить їх на місці, переконавшись, що вони відповідають тому, що має буди відмальовано за конкретним індексом елементу. Це схоже до поведінки `track-by="$index"` у Vue 1.x.

Режим за замовчуванням є ефективним, але він **підходить лише коли відмальований список не покладається на стан дочірніх компонентів чи на тимчасовий стан DOM (наприклад, ввід з полів форми)**.

Для того, щоб підказати Vue стежити за ідентичністю кожного елементу, використовуючи та міняючи порядок наявних елементів, вам потрібно задати унікальний атрибут-ключ `key` для кожного з них:

``` html
<div v-for="item in items" v-bind:key="item.id">
  <!-- вміст -->
</div>
```

Рекомендується задавати атрибут `key` при використанні з `v-for` де тільки це можливо, за винятком, якщо дані DOM, над якими проводиться ітерація досить прості, або якщо ви навмисно спираєтесь на поведінку за замовчуванням задля кращої швидкодії.

Хоч це є загальним механізмом для Vue при ідентифікації елементів, атрибут `key` також має інші використання, які не суто стосуються до `v-for`, як ми це побачимо пізніше у цьому посібнику.

<p class="tip">Не використовуйте не-примітивні значення, такі як об'єкти чи масиви в якості атрибуту `key` з `v-for`. Краще використовуйте рядкові або числові значення.</p>

Для детального розгляду атрибуту `key`, будь ласка, перегляньте [документацію по API для `key`](https://vuejs.org.ua/v2/api/#key).

## Відстеження зміни масиву

### Методи мутацій

Vue обгортає методи відстежуваного масиву таким чином, що його мутація спричинить й оновлення асоційованого вигляду. Перелік обгорнутих методів наступний:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Ви можете відкрити консоль та погратися з масивом `items` з попередніх прикладів, викликаючи їхні методи. Наприклад, `example1.items.push({ message: 'Баз' })`.

### Заміна масиву

Методи мутацій, як і підказує ім'я, змінюють оригінальний масив, на якому було викликано той чи інший метод. Для порівняння, є й інші не мутуючі масив методи, як, наприклад, `filter()`, `concat()` та `slice()`, які не змінюють оригінальний масив, але **завжди повертають новий**. Працюючи з не мутуючими масив методами, ви можете замінити старий масив новим:

``` js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Фуу/)
})
```

Ви можете подумати, що Vue викине наявний DOM та перемалює повний список — однак, на щастя, це не так. Vue застосовує деяку розумну евристику для максимізації перевикористання DOM елементів, тому заміна старого масиву новим, в яких є деякі ті ж самі елементи, буде досить ефективною операцією.

### Застереження

У зв'язку з обмеженнями в JavaScript, є певні типи змін, які Vue **не може відстежити** з масивами та об'єктами. Це було обговорено в розділі [реактивності](reactivity.html#Застереження щодо відстежування змін).

## Показ відфільтрованих/відсортованих результатів

Інколи ми хочемо показати відфільтровану або посортовану версію масиву без власне мутації або скидання оригінальних даних. В таких випадках, ви можете створити обчислювану властивість, яка повертає відфільтрований або відсортований масив.

Приклад:

``` html
<li v-for="n in evenNumbers">{{ n }}</li>
```

``` js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

В ситуаціях, де обчислювані властивості не підходять (наприклад, всередині вкладених циклів `v-for`), ви можете скористатися наступним методом:

```html
<ul v-for="set in sets">
  <li v-for="n in even(set)">{{ n }}</li>
</ul>
```

```js
data: {
  sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

## Діапазони у `v-for`

`v-for` може також приймати число. В цьому випадку шаблон буде повторюватися відповідну тому числу кількість разів.

``` html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

Результат:

{% raw %}
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
<script>
  new Vue({ el: '#range' })
</script>
{% endraw %}

## `v-for` з `<template>`

Подібно до `v-if`, ви також можете використовувати тег `<template>` з `v-for` для відмальовування одразу кількох елементів. Для прикладу:

``` html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` with `v-if`

<p class="tip">Варто зазначити, що **не** рекомендується використовувати `v-if` разом з `v-for`. Зверніться до [style guide](/v2/style-guide/#Уникайте-v-if-з-v-for-суттєво) для деталей.</p>

Впівіснуючи на тому ж елементі, `v-for` має вищий пріоритет, ніж `v-if`. Це означає, що `v-if` буде виконаний на кожній окремій ітерації циклу. Це може бути корисно, коли ви хочете відмальовувати елементи для _лише_ деяких елементів, як у прикладі нижче:

``` html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

Код, що вище, відмальовує лише не виконані елементи завдань.

Якщо навпаки — вашим наміром є пропустити виконання циклу за певною умовою, ви можете розмістити `v-if` на елементі-обгортці (або [`<template>`](conditional.html#Умовні-групи-з-v-if-в-lt-template-gt)). Приклад:

``` html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>Більше немає завдань!</p>
```

## `v-for` з компонентами

> Цей розділ вимагає знання [Компонентів](components.html). Ви можете пропустити його та повернутися до нього пізніше.

Ви можете безпосередньо використовувати `v-for` на власних компонентах, як на будь-яких інших елементах:

``` html
<my-component v-for="item in items" :key="item.id"></my-component>
```

> Починаючи з 2.2.0+, при використанні `v-for` з компонентами, атрибут [`key`](list.html#key) є обов'язковим.

Разом з тим, це не передасть автоматично ніяких даних до компонента, оскільки області видимості компонентів ізольована сама по собі. Для того, щоб передавати дані, які приймають участь в ітерації до компоненту, ми повинні також використовувати вхідні параметри:

``` html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

Причина такої не автоматичної передачі `item` до компоненту — тому що це зробило б компоненту тісно зв'язаною суто з тим, як працює `v-for`. Явно вказуючи те, звідки та як приходять дані, робить її повторно використовуваною для інших ситуацій.

До вашої уваги повний приклад для простого списку задач:

``` html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Додати задачу</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="Приклад: Нагодувати кота"
    >
    <button>Додати</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
```

<p class="tip">Зверніть увагу на атрибут `is="todo-item"`. Це робити необхідно в шаблонах DOM, оскільки елемент `<li>` дійсний лише в `<ul>`. Він робить таке ж, як і `<todo-item>`, але уникає потенційних помилок розбору шаблону браузером. Перегляньте [Застереження щодо розбору DOM-шаблону](components.html#Застереження-щодо-розбору-DOM-шаблону) для деталей.</p>

``` js
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Видалити</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Помити посуд',
      },
      {
        id: 2,
        title: 'Викинути сміття',
      },
      {
        id: 3,
        title: 'Покосити траву'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```

{% raw %}
<div id="todo-list-example" class="demo">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Додати задачу</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="Приклад: Нагодувати кота"
    >
    <button>Додати</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
<script>
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Видалити</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Помити посуд',
      },
      {
        id: 2,
        title: 'Викинути сміття',
      },
      {
        id: 3,
        title: 'Покосити траву'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
</script>
{% endraw %}
