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

You can also [define custom key modifier aliases](../api/#keyCodes) via the global `config.keyCodes` object:

``` js
// enable `v-on:keyup.f1`
Vue.config.keyCodes.f1 = 112
```

## System Modifier Keys

> New in 2.1.0+

You can use the following modifiers to trigger mouse or keyboard event listeners only when the corresponding modifier key is pressed:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

> Note: On Macintosh keyboards, meta is the command key (⌘). On Windows keyboards, meta is the Windows key (⊞). On Sun Microsystems keyboards, meta is marked as a solid diamond (◆). On certain keyboards, specifically MIT and Lisp machine keyboards and successors, such as the Knight keyboard, space-cadet keyboard, meta is labeled “META”. On Symbolics keyboards, meta is labeled “META” or “Meta”.

For example:

```html
<!-- Alt + C -->
<input v-on:keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div v-on:click.ctrl="doSomething">Do something</div>
```

<p class="tip">Note that modifier keys are different from regular keys and when used with `keyup` events, they have to be pressed when the event is emitted. In other words, `keyup.ctrl` will only trigger if you release a key while holding down `ctrl`. It won't trigger if you release the `ctrl` key alone. If you do want such behaviour, use the `keyCode` for `ctrl` instead: `keyup.17`.</p>

### `.exact` Modifier

> New in 2.5.0+

The `.exact` modifier allows control of the exact combination of system modifiers needed to trigger an event.

``` html
<!-- this will fire even if Alt or Shift is also pressed -->
<button v-on:click.ctrl="onClick">A</button>

<!-- this will only fire when Ctrl and no other keys are pressed -->
<button v-on:click.ctrl.exact="onCtrlClick">A</button>

<!-- this will only fire when no system modifiers are pressed -->
<button v-on:click.exact="onClick">A</button>
```

### Mouse Button Modifiers

> New in 2.2.0+

- `.left`
- `.right`
- `.middle`

These modifiers restrict the handler to events triggered by a specific mouse button.

## Why Listeners in HTML?

You might be concerned that this whole event listening approach violates the good old rules about "separation of concerns". Rest assured - since all Vue handler functions and expressions are strictly bound to the ViewModel that's handling the current view, it won't cause any maintenance difficulty. In fact, there are several benefits in using `v-on`:

1. It's easier to locate the handler function implementations within your JS code by skimming the HTML template.

2. Since you don't have to manually attach event listeners in JS, your ViewModel code can be pure logic and DOM-free. This makes it easier to test.

3. When a ViewModel is destroyed, all event listeners are automatically removed. You don't need to worry about cleaning it up yourself.
