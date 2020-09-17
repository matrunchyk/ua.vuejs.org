---
title: Обробка подій
type: guide
order: 9
---

<div class="vueschool"><a href="https://vueschool.io/lessons/vuejs-user-events?friend=vuejs" target="_blank" rel="sponsored noopener" title="Дізнайся, як обробляти події на безкоштовному уроці Vue School">Дізнайся, як обробляти події на безкоштовному уроці Vue School</a></div>

## Прослуховування подій

We can use the `v-on` directive to listen to DOM events and run some JavaScript when they're triggered.
Ми можемо використовувати директиву `v-on` для прослуховування подій DOM і запуску методів JavaScript, коли ці події відбуваються.

Наприклад:

``` html
<div id="example-1">
  <button v-on:click="counter += 1">Додати 1</button>
  <p>На кнопку, що вище, клацнули {{ counter }} разів.</p>
</div>
```
``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

Результат:

{% raw %}
<div id="example-1" class="demo">
  <button v-on:click="counter += 1">Додати 1</button>
  <p>На кнопку, що вище, клацнули {{ counter }} разів.</p>
</div>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
</script>
{% endraw %}

## Методи обробників подій

Однак логіка для багатьох обробників подій буде більш складною, тому записати ваш JavaScript у значення атрибута `v-on` неможливе. Ось чому v-on також може прийняти назву методу, який ви хочете викликати.

На приклад:

``` html
<div id="example-2">
  <!-- `greet` є ім'ям методу визначеного нижче -->
  <button v-on:click="greet">Чудово</button>
</div>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js',
  },
  // визначити метод в об'єкті "методи"
  methods: {
    greet(event) {
      // `this` всередині методів вказує на екземпляр Vue
      alert('Привіт ' + this.name + '!');
      // `event` - рідна подія DOM
      if (event) {
        alert(event.target.tagName);
      }
    }
  }
})

// Ви також можете викликати методи в JavaScript
example2.greet() // => 'Hello Vue.js!'
```

Результат:

{% raw %}
<div id="example-2" class="demo">
  <button v-on:click="greet">Чудово</button>
</div>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js',
  },
  methods: {
    greet: function (event) {
      alert('Привіт ' + this.name + '!');
      if (event) {
        alert(event.target.tagName);
      }
    }
  }
})
</script>
{% endraw %}

## Методи в вбудованих обробниках

Замість прив'язки до імені методу, ми також можемо використовувати методи у вбудованому операторі JavaScript:

``` html
<div id="example-3">
  <button v-on:click="say('Привіт')">Скажи привіт</button>
  <button v-on:click="say('Що')">Скажи що</button>
</div>
```
``` js
new Vue({
  el: '#example-3',
  methods: {
    say(message) {
      alert(message);
    }
  }
})
```

Результат:
{% raw %}
<div id="example-3" class="demo">
  <button v-on:click="say('Привіт')">Скажи привіт</button>
  <button v-on:click="say('Що')">Скажи що</button>
</div>
<script>
new Vue({
  el: '#example-3',
  methods: {
    say(message) {
      alert(message);
    }
  }
})
</script>
{% endraw %}

Іноді нам також потрібно отримати доступ до вихідної події DOM у вбудованому обробнику. Ви можете передати його в метод за допомогою спеціальної змінної `$event`:

``` html
<button v-on:click="warn('Форму все ще не можна надіслати', $event)">
  Надіслати
</button>
```

``` js
// ...
methods: {
  warn(message, event) {
    // тепер ми маємо доступ до події
    if (event) {
      event.preventDefault();
    }
    alert(message);
  }
}
```

## Модифікатори подій

It is a very common need to call `event.preventDefault()` or `event.stopPropagation()` inside event handlers. Although we can do this easily inside methods, it would be better if the methods can be purely about data logic rather than having to deal with DOM event details.

Часто є необхідність викликати `event.preventDefault()` або `event.stopPropagation()` всередині обробників подій. Незважаючи на те, що ми можемо зробити це легко всередині методів, було б краще, якщо б методи використовували виключно логіку даних, а не мали справу з деталями подій DOM.

Для вирішення цієї проблеми Vue надає **модифікатори подій** для `v-on`. Нагадаємо, що модифікатори - це директивні постфікси, позначені крапкою.

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once`
- `.passive`

``` html
<!-- розповсюдження події кліку буде зупинено -->
<a v-on:click.stop="doThis"></a>

<!-- подія надсилання більше не буде перезавантажувати сторінку -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- модифікатори можуть бути об'єднані в ланцюжок -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- тільки модифікатор -->
<form v-on:submit.prevent></form>

<!-- використовувати режим перехоплення під час додавання прослуховувача подій -->
<!-- тобто подія, націлена на внутрішній елемент, обробляється тут перед обробкою цим елементом -->
<div v-on:click.capture="doThis">...</div>

<!-- обробник прослуховувача, лише якщо event.target є самим елементом -->
<!-- тобто не з дочірнього елемента -->
<div v-on:click.self="doThat">...</div>
```

<p class="tip">Порядок має значення при використанні модифікаторів, оскільки відповідний код генерується в тому ж порядку. Тому використання `v-on:click.prevent.self` запобіжить **всім клікам**, тоді як` v-on:click.self.prevent` запобіжить лише клікам самого елемента.</p>

> Нове у 2.1.4+

``` html
<!-- подія кліку буде ініційована лише один раз -->
<a v-on:click.once="doThis"></a>
```

На відміну від інших модифікаторів, які є лише для власних подій DOM, модифікатор `.once` також може використовуватися на [компонентах подій] (components-custom-events.html). Якщо ви ще не читали про компоненти, наразі не хвилюйтеся про це.

> Нове у 2.3.0+

Vue також пропонує `.passive` модифікатор, відповідно до [`passive` опція до `addEventListener`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters).

``` html
<!-- відбудеться поведінка за замовчуванням для прокрутки (прокрутка) -->
<!-- негайно, замість того, щоб чекати завершення `onScroll` -->
<!-- у випадку, якщо він містить `event.preventDefault()` -->
<div v-on:scroll.passive="onScroll">...</div>
```

Модифікатор `.passive` особливо корисний для підвищення продуктивності мобільних пристроїв.

<p class="tip">Не використовуйте ".passive" та ".prevent" разом, оскільки ".prevent" буде проігноровано, і ваш браузер, ймовірно, покаже вам попередження. Пам'ятайте, `.passive` повідомляє браузеру, що ви **не бажаєте** запобігти поведінці за замовчуванням.</p>

## Модифікатори клавіш

Прослуховуючи події на клавіатурі, нам часто потрібно перевірити натискання певних клавіш. Vue дозволяє додавати модифікатори клавіш для `v-on` при прослуховуванні подій клавіатури чи миші:

``` html
<!-- `vm.submit()` спрацює тільки натиснута клавіша `Enter` -->
<input v-on:keyup.enter="submit">
```

Ви можете безпосередньо використовувати будь-які дійсні імена ключів, визначені у [`ПодіїКлавіатури.клавіші`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) як модифікатори перевівши їх у kebab-case.

``` html
<input v-on:keyup.page-down="onPageDown">
```

In the above example, the handler will only be called if `$event.key` is equal to `'PageDown'`.

### Коди клавіш

<p class="tip">Використання подій `keyCode` [застаріле](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode) і може не підтримуватися в нових браузерах.</p>

Також можна використовувати атрибути `keyCode`

``` html
<input v-on:keyup.13="submit">
```

Vue надає псевдоніми для найбільш часто використовуваних кодів клавіш, коли це необхідно для підтримки застарілих браузерів:

- `.enter`
- `.tab`
- `.delete` (перехоплює як клавіші "Delete" так і "Backspace")
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

<p class="tip">Кілька клавіш (`.esc` та всі клавіші зі стрілками) мають невідповідні значення `keyCode` в IE9, тому цим вбудованим псевдонімам слід віддати перевагу, якщо вам потрібна підтримка IE9.</p>

Ви також можете [визначити спеціальні псевдоніми модифікатора клавіш](../api/#keyCodes) через глобальний об'єкт `config.keyCodes`:

``` js
// enable `v-on:keyup.f1`
Vue.config.keyCodes.f1 = 112;
```

## Системні модифікатори клавіш

> Нове у 2.1.0+

Ви можете використовувати наступні модифікатори для запуску прослуховувачів подій миші або клавіатури лише тоді, коли натиснута відповідна клавіша модифікатора:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

> Note: На клавіатурах Macintosh мета є командною клавішею (⌘). На клавіатурах Windows мета - це клавіша Windows (⊞). На клавіатурах Sun Microsystems мета позначена як твердий алмаз (◆). На деяких клавіатурах, зокрема на клавіатурах машин MIT та Lisp, та їх наступниках, таких як клавіатура Knight, "space-cadet" клавіатура, мета позначена як "META". На клавіатурах Symbolics мета позначена як “META” або “Meta”.

На приклад:

```html
<!-- Alt + C -->
<input v-on:keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div v-on:click.ctrl="doSomething">робити щось</div>
```

<p class="tip">Зверніть увагу, що клавіші-модифікатори відрізняються від звичайних клавіш, і коли вони використовуються з подіями `keyup`, їх потрібно натискати, коли подія відбувається. Іншими словами, `keyup.ctrl` спрацьовує, лише якщо ви відпустите клавішу, утримуючи натиснутою` ctrl`. Це не спрацює, якщо ви відпустите клавішу `ctrl` самостійно. Якщо ви хочете такої поведінки, використовуйте замість `keyCode` для` ctrl`: `keyup.17`.</p>

### `.exact` Модифікатор

> Нове у 2.5.0+

Модифікатор `.exact` дозволяє контролювати точну комбінацію системних модифікаторів, необхідних для активації події.

``` html
<!-- це спрацює, навіть якщо натиснуто клавіші Alt або Shift -->
<button v-on:click.ctrl="onClick">A</button>

<!-- це спрацює лише тоді, коли натиснута клавіша Ctrl та жодної іншої -->
<button v-on:click.ctrl.exact="onCtrlClick">A</button>

<!-- це спрацює лише тоді, коли не натиснуті системні модифікатори -->
<button v-on:click.exact="onClick">A</button>
```

### Модифікатори клафіш миші

> New in 2.2.0+

- `.left`
- `.right`
- `.middle`

Ці модифікатори обмежують обробник подіями, викликаними певною кнопкою миші.

## Чому слухачі в HTML?

Ви можете бути стурбовані тим, що весь цей підхід до прослуховування подій порушує старі добрі правила щодо "відокремлення проблем". Будьте впевнені - оскільки всі функції та вирази обробника Vue суворо пов’язані з ViewModel, який обробляє поточний вигляд, це не спричинить труднощів з технічним обслуговуванням. Насправді використання "v-on" має кілька переваг:

1. Простіше знайти реалізації функції обробника в коді JS, обробивши шаблон HTML.

2. Оскільки вам не потрібно вручну підключати прослуховувачі подій у JS, ваш код ViewModel може бути чистою логікою та без DOM. Це полегшує тестування.

3. Коли ViewModel знищений, усі прослуховувачі подій автоматично видаляються. Вам не потрібно турбуватися про те, щоб видаляти їх самостійно.
