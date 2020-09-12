---
title: Форми
type: guide
order: 10
---

## Основне використання

Ви можете використовувати директиву `v-model` для створення двонаправленого зв'язування на полях вводу форми `input`, `textarea`, та елементів `select`. Вона автоматично визначає правильних шлях для оновлення елементу відповідно до його типу. Водночас, можливо дещо магічно, `v-model` є просто "приправою", яка оновлює дані при подіях на полях користувацького вводу і здійснює певну додаткова обробку крайніх випадків.

<p class="tip">`v-model` ігноруватиме початкові значення атрибутів `value`, `checked`, або `selected`, виявлених на будь-яких елементів форми. Вона завжди розглядатиме дані екземпляру Vue як "єдине джерело правди". Ви повинні оголошувати початкове значення на стороні JavaScript, всередині властивості `data` вашої компоненти.</p>

`v-model` всередині використовує різні властивості та видає різні події для різних полів вводу форми:
- текстові елементи `<input>` та `<textarea>` використовують властивість `value` та подію `input`;
- прапорцеві елементи та радіокнопки `<input>` використовують властивість `checked` та подію `change`;
- елементи вибору `<select>` використовують властивість `value` та подію `change`.

<p class="tip" id="vmodel-ime-tip">Для мов, які використовують [IME](https://en.wikipedia.org/wiki/Input_method) (Chinese, Japanese, Korean, etc.), ви побачите, що `v-model` не оновлюється при наборі IME. Якщо ви хочете задовольнити ці умови також, краще використовуйте подію `input`.</p>

### Текст

``` html
<input v-model="message" placeholder="відредагуй мене">
<p>Повідомлення: {{ message }}</p>
```

{% raw %}
<div id="example-1" class="demo">
  <input v-model="message" placeholder="відредагуй мене">
  <p>Повідомлення: {{ message }}</p>
</div>
<script>
new Vue({
  el: '#example-1',
  data: {
    message: ''
  }
})
</script>
{% endraw %}

### Багаторядковий текст

``` html
<span>Багаторядкове повідомлення:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="додайте кілька рядків"></textarea>
```

{% raw %}
<div id="example-textarea" class="demo">
  <span>Багаторядкове повідомлення:</span>
  <p style="white-space: pre-line;">{{ message }}</p>
  <br>
  <textarea v-model="message" placeholder="додайте кілька рядків"></textarea>
</div>
<script>
new Vue({
  el: '#example-textarea',
  data: {
    message: ''
  }
})
</script>
{% endraw %}

{% raw %}
<p class="tip">Інтерпоряція на елементах `<textarea>` (<code>&lt;textarea&gt;{{text}}&lt;/textarea&gt;</code>) не працюватиме. Використовуйте краще <code>v-model</code>.</p>
{% endraw %}

### Прапорці

Одинарний прапорець, булеве значення:

``` html
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```
{% raw %}
<div id="example-2" class="demo">
  <input type="checkbox" id="checkbox" v-model="checked">
  <label for="checkbox">{{ checked }}</label>
</div>
<script>
new Vue({
  el: '#example-2',
  data: {
    checked: false
  }
})
</script>
{% endraw %}

Кілька прапорців, при зв'язуванні з масивом:

``` html
<input type="checkbox" id="ivan" value="Іван" v-model="checkedNames">
<label for="ivan">Іван</label>
<input type="checkbox" id="serhii" value="Сергій" v-model="checkedNames">
<label for="serhii">Сергій</label>
<input type="checkbox" id="andrii" value="Андрій" v-model="checkedNames">
<label for="andrii">Андрій</label>
<br>
<span>Вибрані імена: {{ checkedNames }}</span>
```

``` js
new Vue({
  el: '...',
  data: {
    checkedNames: []
  }
})
```

{% raw %}
<div id="example-3" class="demo">
  <input type="checkbox" id="ivan" value="Іван" v-model="checkedNames">
  <label for="ivan">Іван</label>
  <input type="checkbox" id="serhii" value="Сергій" v-model="checkedNames">
  <label for="serhii">Сергій</label>
  <input type="checkbox" id="andrii" value="Андрій" v-model="checkedNames">
  <label for="andrii">Андрій</label>
  <br>
  <span>Вибрані імена: {{ checkedNames }}</span>
</div>
<script>
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
</script>
{% endraw %}

### Радіо кнопки

``` html
<input type="radio" id="one" value="Один" v-model="picked">
<label for="one">Один</label>
<br>
<input type="radio" id="two" value="Два" v-model="picked">
<label for="two">Два</label>
<br>
<span>Вибрано: {{ picked }}</span>
```
{% raw %}
<div id="example-4" class="demo">
  <input type="radio" id="one" value="Один" v-model="picked">
  <label for="one">Один</label>
  <br>
  <input type="radio" id="two" value="Два" v-model="picked">
  <label for="two">Два</label>
  <br>
  <span>Вибрано: {{ picked }}</span>
</div>
<script>
new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
})
</script>
{% endraw %}

### Список вибору

Одинарний список:

``` html
<select v-model="selected">
  <option disabled value="">Виберіть щось одне</option>
  <option>А</option>
  <option>Б</option>
  <option>В</option>
</select>
<span>Вибрано: {{ selected }}</span>
```
``` js
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```
{% raw %}
<div id="example-5" class="demo">
  <select v-model="selected">
    <option disabled value="">Виберіть щось одне</option>
    <option>А</option>
    <option>Б</option>
    <option>В</option>
  </select>
  <span>Вибрано: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-5',
  data: {
    selected: ''
  }
})
</script>
{% endraw %}

<p class="tip">Якщо початкове значення вашого виразу `v-model` яке не відповідає жодному елементу списку, тоді елемент `<select>` відмалює невибраний стан. В iOS, це унеможливить користувача вибрати перший елемент, оскільки iOS не викличе подію `change` у такому випадку. Тому радимо використовувати властивість `disabled` для порожнього елементу, як було продемонстровано у прикладі вище.</p>

Декілька списків вибору (прив'язаних до масиву):

``` html
<select v-model="selected" multiple>
  <option>А</option>
  <option>Б</option>
  <option>В</option>
</select>
<br>
<span>Вибрано: {{ selected }}</span>
```
{% raw %}
<div id="example-6" class="demo">
  <select v-model="selected" multiple>
    <option>А</option>
    <option>Б</option>
    <option>В</option>
  </select>
  <br>
  <span>Вибрано: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-6',
  data: {
    selected: []
  }
})
</script>
{% endraw %}

Динамічні списки, відмальованих через `v-for`:

``` html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Вибрано: {{ selected }}</span>
```
``` js
new Vue({
  el: '...',
  data: {
    selected: 'А',
    options: [
      { text: 'Один', value: 'А' },
      { text: 'Два', value: 'Б' },
      { text: 'Три', value: 'В' }
    ]
  }
})
```
{% raw %}
<div id="example-7" class="demo">
  <select v-model="selected">
    <option v-for="option in options" v-bind:value="option.value">
      {{ option.text }}
    </option>
  </select>
  <span>Вибрано: {{ selected }}</span>
</div>
<script>
new Vue({
  el: '#example-7',
  data: {
    selected: 'А',
    options: [
      { text: 'Один', value: 'А' },
      { text: 'Два', value: 'Б' },
      { text: 'Три', value: 'В' }
    ]
  }
})
</script>
{% endraw %}

## Зв'язування значень

Для радіо кнопок, прапорців та списків вибору, зв'язані значення `v-model` є, як правило, статичними рядковими значеннями (або булевими для прапорців):

``` html
<!-- `picked` є рядковим значенням "а", коли вибрано -->
<input type="radio" v-model="picked" value="а">

<!-- `toggle` є або true або false -->
<input type="checkbox" v-model="toggle">

<!-- `selected` є рядком "абв", коли вибрано перший елемент списку -->
<select v-model="selected">
  <option value="абв">АБВ</option>
</select>
```

Однак, інколи, нам можливо треба буде зв'язувати значення з динамічною властивістю на екземплярі Vue. Ми також можемо використовувати `v-bind` для досягнення цього. Додатково, використання `v-bind` дозволяє зв'язати значення вводу до не-рядкових значень.

### Прапорець

``` html
<input
  type="checkbox"
  v-model="toggle"
  true-value="так"
  false-value="ні"
>
```

``` js
// коли вибрано:
vm.toggle === 'так'
// коли не вибрано:
vm.toggle === 'ні'
```

<p class="tip">Атрибути `true-value` та `false-value` не впливають на атрибут поля вводу `value`, оскільки не відмічені прапорці не приймають участь в надісланих формах в браузерах. Для гарантування, що одне з двох значень буде надіслано, як частина форми (як-от "так" чи "ні"), краще використовуйте радіо кнопки.</p>

### Радіо кнопки

``` html
<input type="radio" v-model="pick" v-bind:value="а">
```

``` js
// коли вибрано:
vm.pick === vm.а
```

### Select Options

``` html
<select v-model="selected">
  <!-- лінійний літерал об'єкту -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
```

``` js
// коли вибрано:
typeof vm.selected // => 'object'
vm.selected.number // => 123
```

## Модифікатори

### `.lazy`

За замовчуванням, `v-model` синхронізує введені дані з даними Vue екземпляру після кожної події `input` (за винятком набору IME, як [згадано раніше](#vmodel-ime-tip)). Ви можете додати модифікатор `lazy` замість `sync` для оновлення даних _після_ подій `change`:

``` html
<!-- синхронізовано після "change" замість "input" -->
<input v-model.lazy="msg">
```

### `.number`

Якщо ви хочете, щоб користувацький ввід був автоматично переведений в числовий тип, ви можете вказати модифікатор `number` до ваших полів вводу, де є підтримка `v-model`:

``` html
<input v-model.number="age" type="number">
```

Це часто корисно, оскільки навіть з `type="number"`, значення з HTML поля вводу завжди повертає рядкову величину. Якщо значення не може бути розпізнано з `parseFloat()`, тоді повернеться оригінальне значення.

### `.trim`

Якщо ви хочете, щоб пробіли були автоматично видалені з користувацького вводу, ви можете додати модифікатор `trim` до ваших полів вводу, де є підтримка `v-model`:

```html
<input v-model.trim="msg">
```

## `v-model` з компонентами

> Якщо ви ще поки не знайомі з компонентами Vue, ви можете зараз пропустити цей розділ.

Типи вбудованих в HTML елементів вводу не завжди можуть задовольняти вашим умовам. На щастя, компоненти Vue дозволяють вам будувати власні повторно використовувані елементи вводу з повністю налаштовуваною поведінкою. Такі елементи навіть можуть працювати з `v-model`!

Щоб дізнатися більше, прочитайте про [користувацькі поля вводу](components.html#Використання-v-model-на-компонентах) у розділі компонентів.
