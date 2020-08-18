---
title: обчислювані властивості та спостерігачі
type: guide
order: 5
---

## Обчислювані властивості

<div class="vueschool"><a href="https://vueschool.io/lessons/vuejs-computed-properties?friend=vuejs" target="_blank" rel="sponsored noopener" title="Вивчи, як обчислювані властивості працюють з Vue School">Вивчіть, як обчислювані властивості працюють на безкоштовних уроках Vue School</a></div>

Шаблонні вирази дуже зручні, але вони призначені для простих операцій. Додавання великої кількості логіки до шаблонів може зробити їх переповненими і складними для підтримки. Наприклад:

``` html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

Тепер шаблон більше не простий і декларативний. Вам необхідно дивитися на нього протягом секунди щоб зрозуміти, що він показує `message` в зворотньому напрямку. Проблема укладнюється якщо ви хочете включити перевернуте повідомлення в шаблон більше одного разу.

Ось чому для будь якої комплексної логіки вам потрібно використовувати **обчислювані властивості**

### Простий приклад

``` html
<div id="example">
  <p>Оригінальне повідомлення: "{{ message }}"</p>
  <p>Обчислене перевернуте повідомлення: "{{ reversedMessage }}"</p>
</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // обчислювана властивість
    reversedMessage: function () {
      // `this` вказує на екземпляр vm
      return this.message.split('').reverse().join('')
    }
  }
})
```

Результат:

{% raw %}
<div id="example" class="demo">
  <p>Оригінальне повідомлення: "{{ message }}"</p>
  <p>Обчислене перевернуте повідомлення: "{{ reversedMessage }}"</p>
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    reversedMessage: function () {
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
{% endraw %}

Тут ми вказали обчислювану властивість `reversedMessage`. Функція, яку ми подали буде використовуватися як функція-читач для властивості `vm.reversedMessage`:

``` js
console.log(vm.reversedMessage) // => 'olleH'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // => 'eybdooG'
```

Ви можете відкрити консоль і гратися з vm з прикладу самостійно. Значення `vm.reversedMessage` завжди залежить від значення `vm.message`.

Ви можете прив'язатися до обчислюваних властивостей в шаблонах так само, як до звичайної властивості. Vue відомо, що `vm.reversedMessage` залежить від `vm.message`, тому буде оновлено кожна прив'язка, яка залежить від `vm.reversedMessage`, коли `vm.message` змінюється. І найкраще це те, що ми створили це відношення залежності декларативно: обчислювана функція-читач не має побічних впливів, що робить її тестування і розуміння простішими.

### Обчислене Кешування проти Методів

Ви могли помітити, що ми можемо досягнути того ж самого результату викликом методу у виразі:

``` html
<p>Перевернуте повідомлення: "{{ reverseMessage() }}"</p>
```

``` js
// в компоненті
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

Замість обчслюваної властивості, ми можемо задати таку ж саму функцію в якості методу. Для кінцевого результату два підходи дійсно однакові. Однак, різниця в тому, що **обчислювані властивості кешовані, основуючись на їх реактивних залежностях**. Обчислювана властивість буде перерахована тільки в тому випадку, якщо хоча б одна з його рективних залежностей змінилася. Тобто поки властивість `message` не змінилася, багаторазовий доступ до обчислюваної властивості `reversedMessage` буде одразу повертати попередньо обчислений результат без необхідності знову викликати функцію.

Це також означає, що наступна обчислювана властивість ніколи не буде оновлюватися, тому, що `Date.now()` не реактивна залежність:

``` js
computed: {
  now: function () {
    return Date.now()
  }
}
```

Для порівняння, виклик методу **завжди** запускатиме функцію під час повторного відображення.

Навіщо нам потрібне кешування? Уявіть, що у нас є складне обчислене поле **A**, яке вимагає обробки в циклі величезного масиву та проведення багатьох обчислень. Тоді можемо мати інші обчислювані властивості, які в свою чергу залежать від **A**. Без кешування ми б запускали отримувач **A** набагато більше разів, ніж потрібно! У випадках, коли вам не потрібне кешування, скористайтеся методом.

### Обчислювана проти Спостережуваної властивості

Vue надає більш загальний спосіб оглядати і реагувати на зміни даних в екземплярі Vue: **спостережувана властивість**. Якщо у вас є деякі дані, які повинні змінюватись основуючись на інших даних, це провокує перевикористання `watch` - особливо якщо ви прийшли з підгрунтям AngularJS. Однак, часто кращою ідеєю є використання обчислюваних властивостей аніж імперативна функція виклику `watch`. Розглянемо цей приклад:

``` html
<div id="demo">{{ fullName }}</div>
```

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

Вищевказаний код є імперативним та повторюваним. Порівняйте його з версією обчислюваної властивості:

``` js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

Набагато краще, чи не так?

### Обчислюваний задавач

Обчислювані властивості за замовчуванням використовуються лише для отримання, але ви також можете надати задавач, коли вам це потрібно:

``` js
// ...
computed: {
  fullName: {
    // отримувач
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // задавач
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```
Тепер, коли ви запустите `vm.fullName = 'John Doe'`, задавач буде викликано, і` vm.firstName` та `vm.lastName` буде оновлено відповідно.

## Спостерігачі

Хоча обчислювані властивості є більш доцільними у більшості випадків, є випадки, коли спеціальний спостерігач необхідний. Ось чому Vue пропонує більш загальний спосіб реагування на зміни даних за допомогою опції `watch`. Це найбільш корисно, коли ви хочете виконувати асинхронні чи складні операції у відповідь на зміну даних.

Наприклад:

``` html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

``` html
<!-- Оскільки вже існує багата екосистема бібліотек ajax    -->
<!-- і колекції методів загального призначення, база Vue    -->
<!-- здатна залишатися малою, не вигадуючи їх. Це також     -->
<!-- дає вам свободу використовувати те, що вам знайоме.    -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // ця функція буде запускатися кожного разу коли змінюватиметься `question`
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // _.debounce - це функція, надана lodash для обмеження частоти
    // з якою може бути запущена особливо складна операція.
    // У цьому випадку ми хочемо обмежити, як часто ми отримуємо доступ
    // до yesno.wtf/api, чекаючи, коли користувач повністю завершить
    // вводити текст, перш ніж робити ajax запит. Для
    // докладнішого вивчення про функцію _.debounce (та його двоюрідного брата
    // _.throttle), відвідайте: https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
```

Result:

{% raw %}
<div id="watch-example" class="demo">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
{% endraw %}

У цьому випадку використання `watch` дозволяє нам виконувати асинхронну операцію (доступ до API), обмежувати частоту виконання цієї операції та встановлювати проміжні стани, поки ми не отримаємо остаточну відповідь. Нічого з цього не було б можливим із обчислюваною властивістю.

Окрім `watch`, ви також можете використовувати імперативний [vm.$watch API](../api/#vm-watch).

