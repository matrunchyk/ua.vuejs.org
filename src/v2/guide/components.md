---
title: Компоненти
type: guide
order: 11
---

<div class="vueschool"><a href="https://vueschool.io/courses/vuejs-components-fundamentals?friend=vuejs" target="_blank" rel="sponsored noopener" title="Free Vue.js Components Fundamentals Course">Подивитись безкоштовний відеокурс на Vue School</a></div>

## Базовий приклад

Ось приклад компонента Vue:

```js
// Створимо новий компонент, що називається button-counter
Vue.component("button-counter", {
  data: function () {
    return {
      count: 0,
    };
  },
  template:
    '<button v-on:click="count++">Ви натиснули на кнопку {{ count }} кількість разів.</button>',
});
```

Компоненти - це іменовані екземпляри Vue, які можна перевикористовувати.
У даному випадку це компонент з іменем `<button-counter>`. Ми можемо використати цей компонент як нестандартний тег всередині основного екземпляру Vue, створеного за допомогою `new Vue`:

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

{% codeblock lang:js %}
new Vue({ el: '#components-demo' })
{% endcodeblock %}

{% raw %}

<div id="components-demo" class="demo">
  <button-counter></button-counter>
</div>
<script>
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count += 1">Ви натиснули на кнопку {{ count }} кількість разів.</button>'
})
new Vue({ el: '#components-demo' })
</script>
{% endraw %}

Компоненти - це просто екземпляри Vue, тому вони приймають ті ж самі параметри, що і базовий екземпляр, створений за допомогою `new Vue`. Наприклад, компонент може приймати такі параметри, як `data`, `computed`, `watch`, `methods` та хуки життєвого циклу. Єдині виключення - це декілька специфічних, для основного екземпляру, параметрів наприклад `el`.

## Перевикористання компонентів

Компоненти можуть бути використані стільки разів, скільки буде потрібно.

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

{% raw %}

<div id="components-demo2" class="demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
<script>
new Vue({ el: '#components-demo2' })
</script>
{% endraw %}

Зауважте, що кожна кнопка має свій `лічильник` натискань. Це відбувається тому, що кожного разу, коли ви використовуєте компонент - створються новий **екземпляр** цього компоненту.

### `data` Має бути функцією

При створенні компонента `<button-counter>`, ви могли помітити, що `data` не передається напряму як об'єкт, наприклад:

```js
data: {
  count: 0;
}
```

Замість цього, **параметр `data` має бути функцією**, таким чином кожен екземпляр компонента буде працювати з власною незалежною копією об'єкта data:

```js
data: function () {
  return {
    count: 0
  }
}
```

Якщо б Vue не дотримувався цього правила, то натискання на будь-яку з кнопок призводило б до змін у _всіх інших екземплярах_, як у прикладі нижче:

{% raw %}

<div id="components-demo3" class="demo">
  <button-counter2></button-counter2>
  <button-counter2></button-counter2>
  <button-counter2></button-counter2>
</div>
<script>
var buttonCounter2Data = {
  count: 0
}
Vue.component('button-counter2', {
  data: function () {
    return buttonCounter2Data
  },
  template: '<button v-on:click="count++">Ви натисли на кнопку {{ count }} кількість разів.</button>'
})
new Vue({ el: '#components-demo3' })
</script>
{% endraw %}

## Організація компонентів

Зазвичай компоненти організуються у вигляді дерева

![Component Tree](/images/components.png)

Наприклад, ви можете мати компонент для заголовка, бокової панелі та зони з основним контентом, кожен з яких зазвичай містить у собі інші компоненти - посилання, дописи блогу та ін.

Щоб використовувати дочірні компоненти у шаблонах - вони повинні бути зареєстровані так, щоб Vue міг з ними працювати. Існує 2 способи реєстрації компонента: **глобально** та **локально**. До цього моменту ми реєстрували компоненти лише глобально, використовуючи `Vue.component`:

```js
Vue.component("my-component-name", {
  // ... options ...
});
```

Компоненти, що зареєстровані глобально, можуть бути використані у шаблоні будь-якого кореневого екземпляру Vue (`new Vue`), створеного після реєстрації - і навіть у шаблонах усіх дочірніх компонентів цього екземпляру.

Наразі це все, що вам потрібно знати про реєстрацію компонентів, але коли ви завершите читати цю сторінку і освоїте викладені концепції, ми рекомендуємо повернутись і прочитати повний посібник по роботі з компонентами [Реєстрація Компонентів](components-registration.html).

## Передача даних у дочірні компоненти за допомогою вхідних параметрів

Раніше ми згадували створення компонента для постів у блозі. Проблема у тому, що такий компонент буде не надто корисним, до того моменту, як ви зможете передавати дані всередину цього компонента, наприклад, заголовок і вміст конкретного допису блогу, який ми хочемо відобразити. Саме для цього нам і знадобляться вхідні параметри (`props`) компонента.

Вхідні параметри — це спеціальні атрибути, які можуть бути встановлені для компонента. Коли ми передаємо значення у вхідні параметри екземпляра, воно стає властивістю конкретного екземпляра.
Для того, щоб передати заголовок до нашого компонента з дописом, потрібно включити заголовок до списку вхідних параметрів, які даний компонент приймає, використовуючи `props`.

```js
Vue.component("blog-post", {
  props: ["title"],
  template: "<h3>{{ title }}</h3>",
});
```

Компонент може мати будь-яку кількість вхідних параметрів, і за замовчуванням будь-яке значення може бути передано до будь-якого параметру. У шаблоні нижче ви можете побачити, що ми можемо отримати доступ до цього значення аналогічним способом, який ми використовуємо для доступу до опції `data`.

Як тільки ви визначили вхідні параметри, то зможете передати дані всередину наступним чином.

```html
<blog-post title="Мій шлях з Vue"></blog-post>
<blog-post title="Блоги по Vue"></blog-post>
<blog-post title="Чому Vue такий цікавий"></blog-post>
```

{% raw %}

<div id="blog-post-demo" class="demo">
  <blog-post1 title="Мій шлях з Vue"></blog-post1>
  <blog-post1 title="Блоги по Vue"></blog-post1>
  <blog-post1 title="Чому Vue такий цікавий"></blog-post1>
</div>
<script>
Vue.component('blog-post1', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
new Vue({ el: '#blog-post-demo' })
</script>
{% endraw %}

Зазвичай, ви захочете мати масив дописів всередині `data`

```js
new Vue({
  el: "#blog-post-demo",
  data: {
    posts: [
      { id: 1, title: "Мій шлях з Vue" },
      { id: 2, title: "Блоги по Vue" },
      { id: 3, title: "Чому Vue такий цікавий" },
    ],
  },
});
```

А потім ви захочете промалювати компонент для кожного з постів:

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
></blog-post>
```

Вище ви можете побачити, що ми використовуємо `v-bind` для динамічної передачі вхідних параметрів. Це надзвичайно корисно коли ви не знаєте заздалегідь, який саме контент ви збираєтесь промалювати, наприклад у ситуації коли ми [отримуємо дописи з API](https://codesandbox.io/s/github/vuejs/vuejs.org/tree/master/src/v2/examples/vue-20-component-blog-post-example).

Це все що вам потрібно знати про вхідні параметри наразі, але коли ви прочитаєте й освоїте всю інформацію на цій сторінці, ми рекомендуємо повернутись і прочитати повне керівництво по [Вхідних параметрах](components-props.html).

## Один кореневий елемент

При створенні компоненту на кшталт `<blog-post>`, ваш шаблон згодом буде містити більше ніж один тег з заголовком:

```html
<h3>{{ title }}</h3>
```

Як мінімум ви захочете додати контент самого допису:

```html
<h3>{{ title }}</h3>
<div v-html="content"></div>
```

Якщо ви спробуєте використати такий шаблон, Vue вкаже на помилку яка каже про те, що **кожен компонент має мати один кореневий елемент (тег)**. Ви можете виправити цю помилку просто загорнувши ваш шаблон в один кореневий елемент, наприклад таким чином:

```html
<div class="blog-post">
  <h3>{{ title }}</h3>
  <div v-html="content"></div>
</div>
```

З часом, наш компонент буде ставати більш об'ємним і нам знадобляться не тільки заголовок та контент допису, але й дата публікації, коментарі та ін. Створювати вхідні параметри для кожного пов'язаного шматочка інформації може бути доволі незручно.

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
  v-bind:content="post.content"
  v-bind:publishedAt="post.publishedAt"
  v-bind:comments="post.comments"
></blog-post>
```

То ж зараз, вдалий момент для рефакторингу нашого `<blog-post>` компонента таким чином, що він буде приймати один вхідний параметр `post`.

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:post="post"
></blog-post>
```

```js
Vue.component("blog-post", {
  props: ["post"],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <div v-html="post.content"></div>
    </div>
  `,
});
```


<p class="tip">Приклад вище і деякі наступні використовують [літерал шаблону JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) для того, щоб зробити багаторядкові шаблони зручнішими для читання. Ця функція не підтримується у Internet Explorer (IE), тож якщо вам важлива підтримка IE, і ви не використовуєте Babel чи TypeScript, скористайтеся [записом зі зворотнім слешем для багаторядкових шаблонів](https://css-tricks.com/snippets/javascript/multiline-string-variables-in-javascript/).</p>

Тепер, коли новий вхідний параметр доданий до `post` об'єктів, він буде автоматично доступний всередині `<blog-post>`.

## Прослуховування подій від дочірнього елемента

Згодом наш компонент `<blog-post>` може потребувати можливості комунікувати з батьківським компонентом. Наприклад, ми можемо додати можливість збільшити шрифт тексту у дописі, залишаючи решту сторінки незмінною.

У батьківському елементі ми можемо додати підтримку такої можливості за допомогою створення змінної `postFontSize` всередині `data`.

```js
new Vue({
  el: "#blog-posts-events-demo",
  data: {
    posts: [
      /* ... */
    ],
    postFontSize: 1,
  },
});
```

Ця змінна може бути використана у шаблоні для зміни розміру шрифту у всіх дописах.

```html
<div id="blog-posts-events-demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      v-bind:key="post.id"
      v-bind:post="post"
    ></blog-post>
  </div>
</div>
```

Тепер додамо кнопку, яка буде збільшувати шрифт, прямо перед вмістом кожного допису.

```js
Vue.component("blog-post", {
  props: ["post"],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <button>
        Збільшити текст
      </button>
      <div v-html="post.content"></div>
    </div>
  `,
});

```

Проблема у тому, що наразі кнопка нічого не робить:

```html
<button>
  Збільшити текст
</button>
```

Коли ми натискаємо на кнопку, вона повинна комунікувати з батьківським компонентом, який у свою чергу, має збільшити розмір шрифту. На щастя, екземпляр Vue має спеціальну систему подій для роз цієї проблеми. Батьківський компонент може реагувати на будь-яку подію у дочірньому компоненті за допомогою директиви `v-on`, так само як це відбувається зі звичайною подією DOM:

```html
<blog-post ... v-on:enlarge-text="postFontSize += 0.1"></blog-post>
```

Дочірній компонент може сам генерувати події, викликаючи вбудований [**$emit** метод](../api/#vm-emit) та передаючи йому назву події:

```html
<button v-on:click="$emit('enlarge-text')">
  Збільшити текст
</button>
```

Завдяки прослуховуванню події `v-on:enlarge-text="postFontSize += 0.1"`, батьківський елемент простежить цю подію й оновить значення `postFontSize`.

{% raw %}

<div id="blog-posts-events-demo" class="demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      v-bind:key="post.id"
      v-bind:post="post"
      v-on:enlarge-text="postFontSize += 0.1"
    ></blog-post>
  </div>
</div>
<script>
Vue.component('blog-post', {
  props: ['post'],
  template: '\
    <div class="blog-post">\
      <h3>{{ post.title }}</h3>\
      <button v-on:click="$emit(\'enlarge-text\')">\
        Збільшити текст\
      </button>\
      <div v-html="post.content"></div>\
    </div>\
  '
})
new Vue({
  el: '#blog-posts-events-demo',
  data: {
    posts: [
      { id: 1, title: 'Мій шлях з Vue', content: '...content...' },
      { id: 2, title: 'Блоги по Vue', content: '...content...' },
      { id: 3, title: 'Чому Vue такий цікавий', content: '...content...' }
    ],
    postFontSize: 1
  }
})
</script>
{% endraw %}

### Передача даних разом з подією

Інколи буває корисно генерувати спеціальне значення разом з подією. Наприклад, ми захочемо щоб компонент `<blog-post>` відповідав за те, наскільки збільшити розмір шрифту. У цьому випадку ми можемо використати другий параметр у методі `$emit` для передачі значення.

```html
<button v-on:click="$emit('enlarge-text', 0.1)">
  Збільшити текст
</button>
```

Тоді прослуховуючи цю подію у батьківському компоненті ми можемо отримати доступ до переданого разом з подією значення через `$event`

```html
<blog-post ... v-on:enlarge-text="postFontSize += $event"></blog-post>
```

Або наступним чином, якщо обробкою події займається метод:

```html
<blog-post ... v-on:enlarge-text="onEnlargeText"></blog-post>
```

В такому разі значення буде передано у першому параметрі цього методу.

```js
methods: {
  onEnlargeText: function (enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

### Використання `v-model` на компонентах

Визначені користувачем події можуть бути також використані для створення спеціального вводу, який працює з `v-model`. Згадайте, що:

```html
<input v-model="searchText" />
```

еквівалентно наступному:

```html
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
/>
```

Якщо використати `v-model` на компоненті, буде виконано наступне:

```html
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event"
></custom-input>
```

Щоб це дійсно спрацювало, `<input>` всередині компонента має задовільняти наступному:

- Зв'язувати значення атрибута `<value>` з вхідним параметром `<value>`
- При виникненні події `<input>` генерувати визначену користувачем подію `<input>` з новим значенням

Ось як це працює:

```js
Vue.component("custom-input", {
  props: ["value"],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `,
});
```

Тепер директива `v-model` повинна ідеально працювати з цим компонентом:

```html
<custom-input v-model="searchText"></custom-input>
```

Наразі це все, що вам потрібно знати про нестандартні події у компонентах, але коли ви прочитаєте і розберетесь з усією інформацією на цій сторінці, ми рекомендуємо повернутись і прочитати повне керівництво по [Нестандартним подіям](components-custom-events.html).


## Передача контенту у слоти

Як і зі звичайними HTML-елементами, часто буває корисно передати певний вміст у компонент, наприклад:

```html
<alert-box>
  Сталося щось недобре.
</alert-box>
```

Що може виглядати приблизно таким чином:

{% raw %}

<div id="slots-demo" class="demo">
  <alert-box>
    Сталося щось недобре.
  </alert-box>
</div>
<script>
Vue.component('alert-box', {
  template: '\
    <div class="demo-alert-box">\
      <strong>Помилка!</strong>\
      <slot></slot>\
    </div>\
  '
})
new Vue({ el: '#slots-demo' })
</script>
<style>
.demo-alert-box {
  padding: 10px 20px;
  background: #f3beb8;
  border: 1px solid #f09898;
}
</style>
{% endraw %}

На щастя, це завдання може бути виконано дуже просто за допомогою нестандартних елементів `<slot>`:

```js
Vue.component("alert-box", {
  template: `
    <div class="demo-alert-box">
      <strong>Помилка!</strong>
      <slot></slot>
    </div>
  `,
});
```

Як ви можете бачити, ми просто додали слот там, де нам потрібно — і це все. Справу зроблено.

Наразі це все, що вам потрібно знати про слоти, але коли ви прочитаєте і розберетесь з усією інформацією на цій сторінці, ми рекомендуємо повернутись і прочитати повне керівництво по [Слотах](components-slots.html)

## Динамічне перемикання між компонентами

Інколи буває необхідно динамічно перемикатись між компонентами, як наприклад в інтерфейсі з вкладками:

{% raw %}

<div id="dynamic-component-demo" class="demo">
  <button
    v-for="tab in tabs"
    v-bind:key="tab"
    class="dynamic-component-demo-tab-button"
    v-bind:class="{ 'dynamic-component-demo-tab-button-active': tab === currentTab }"
    v-on:click="currentTab = tab"
  >
    {{ tab }}
  </button>
  <component
    v-bind:is="currentTabComponent"
    class="dynamic-component-demo-tab"
  ></component>
</div>
<script>
Vue.component('tab-home', { template: '<div>Головний компонент</div>' })
Vue.component('tab-posts', { template: '<div>Компонент з блогом</div>' })
Vue.component('tab-archive', { template: '<div>Компонент архіву</div>' })
new Vue({
  el: '#dynamic-component-demo',
  data: {
    currentTab: 'Home',
    tabs: ['Home', 'Posts', 'Archive']
  },
  computed: {
    currentTabComponent: function () {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})
</script>
<style>
.dynamic-component-demo-tab-button {
  padding: 6px 10px;
  border-top-left-radius: 3px;
  border-top-right-radius: 3px;
  border: 1px solid #ccc;
  cursor: pointer;
  background: #f0f0f0;
  margin-bottom: -1px;
  margin-right: -1px;
}
.dynamic-component-demo-tab-button:hover {
  background: #e0e0e0;
}
.dynamic-component-demo-tab-button-active {
  background: #e0e0e0;
}
.dynamic-component-demo-tab {
  border: 1px solid #ccc;
  padding: 10px;
}
</style>
{% endraw %}

Показане вище можливе завдяки елементу Vue `<component>`, який має спеціальний атрибут `<is>`:

```html
<!-- Компонент змінюється currentTabComponent, змінюється і компонент -->
<component v-bind:is="currentTabComponent"></component>
```

У прикладі вище `currentTabComponent` може містити:

- ім'я зареєстрованого компонента, або
- об'єкт з налаштуваннями для компонента

Подивіться на [цей приклад](https://codesandbox.io/s/github/vuejs/vuejs.org/tree/master/src/v2/examples/vue-20-dynamic-components) щоб поекспериментувати з повним кодом або на [інший приклад](https://codesandbox.io/s/github/vuejs/vuejs.org/tree/master/src/v2/examples/vue-20-dynamic-components-with-binding)), що містить прив'язку до об'єкта з налаштуваннями компонента замість зареєстрованого імені.

Майте на увазі, що цей атрибут може бути використаний зі звичайними HTML елементами, разом з тим вони будуть розглянуті як компоненти, що означатиме, що усі атрибути **будуть прив'язані як атрибути DOM**. Щоб деякі властивостей, такі як `value` працювали, як ви цього очікуєте, вам потрібно зв'язати їх, використовуючи [модифікатор `.prop`](../api/#v-bind).

Наразі це все, що вам потрібно знати про динамічне перемикання між компонентами, але коли ви прочитаєте і розберетесь з усією інформацією на цій сторінці, ми рекомендуємо повернутись і прочитати повне керівництво по [Динамічним і асинхронним компонентам](components-dynamic-async.html).

## Особливості парсингу DOM-шаблона

Деякі HTML елементи як то `<ul>`, `<ol>`, `<table>` та `<select>` мають обмеження щодо того, які елементи можуть з'являтися всередині них, а деякі елементи як то `<li>`, `<tr>`, та `<option>` можуть з'являтися лише всередині певних елементів.

Це призводить до певних проблем при використанні компонентів, які містять такі елементи. Наприклад:

```html
<table>
  <blog-post-row></blog-post-row>
</table>
```

Нестандартний компонент `<blog-post-row>` буде розглядатись як недійсний контент, спричиняючи помилки на кінцевому етапі відмальовування. На щастя, спеціальний атрибут `is` пропонує вихід:

```html
<table>
  <tr is="blog-post-row"></tr>
</table>
```

Треба зазначити, що **ці обмеження __не застосовуються__ якщо ви використовуєте шаблони з одного із цих джерел**:

- Строкові шаблони(наприклад `template: '...'`)
- [Однофайлові (`.vue`) компоненти](single-file-components.html)
- [`<script type="text/x-template">`](components-edge-cases.html#X-Templates)

Наразі це все ще вам потрібно знати про особливості розбору DOM-шаблонів, і, власне, це завершення розділу _Основи_ Vue. Вітаємо. Є ще матеріал для вивчення, але спочатку ми рекомендуємо зробити невеличку перерву і спробувати трохи погратися з Vue, створивши щось цікаве самостійно.

Коли ви будете впевнені, що засвоїли отримані знання, ми рекомендуємо повернутись і прочитати повний посібник по [Динамічним та асинхронним компонентам](components-dynamic-async.html), а також інші сторінки з розділу Компоненти поглиблено на боковій панелі.
