# Краткий справочник по VueJS 3

## Раздел Компонеты

### Добавление компонента 
1. Сделать файл AppHeader.vue 
2. набрать и нажать TAB (для WebStorm)
> vbase
3. В том файле КУДА пишем 
```angular2html
<script> 
    import AppHeader from "./AppHeader"
```
4. Там же 
```angular2html
components:
  {
     AppHeader, //или 'app-header': AppHeader,
                //или 'AppHeader': AppHeader,  
                //или 'BlaBlaBla': AppHeader,
  },
```
5. В разметке пишем 
```angular2html
<app-header></app-header> //или <AppHeader />
```

### Вывод статики 
1. В script пишем:
```angular2html
data() { 
  return { 
    now: new Date().toLocateDateString(); 
  } 
}
```   
2. В месте вывода пишем:
```angular2html
{{ now }}
```   

### Переключение (например, статусы) 
```angular2html
isOpen = !isOpen
```

### Использование функции в элементе 
1. Добавить событие на элемент 
```angular2html
@click="funcName()"
```
если функцию можно в одну строку, то:
```angular2html
@click="isOpen = !isOpen"
```
2. Добавить функцию в methods: 
```angular2html
methods:
  { 
    funcName() { 
    ... 
    } 
  }
```

### Передача статических данных в компонент 
используем props - свойства для предоставления другим 
1. В родительском делаем свойство (параметр)
а) для статики:
```angular2html
<app-header title="Name of blabla"></app-header>
```
б) для динамики байндим: 
```angular2html
<app-header :title="item"></app-header>
```
и item берем из data()  
2. В дочернем (компоненте) делаем 
```angular2html
props: [ 'title' //название ожидаемого параметра ]
```
3. В дочернем для вывода пишем:
```angular2html
{{ title }} // -> Name of blabla
```

### Передача динамических данных в компонент 
1. В дочернем описать свойство компонента (см. Описание свойств компонента)  
2. Для кнопки заэмитеть событие 
```angular2html
<button @click="$emit('action')">Кликни</button>
```
3. Добавить эмит для события 
```angular2html
emits: ['open-news']
```
4. Импортировать компонент в родительский  
5. Добавить в описание компонентов:
```angular2html
components: { ... }
```
6. На месте вывода компонента 
```angular2html
<app-button color="primary" @action="open" :text="func() or ?:"></app-button>
// или <app-button :color="'primary'" ...
```

### Передать содержимое между тэгами из родительского в дочерний 
1. В дочернем 
```angular2html
<slot /> // или <slot><slot />
```   
2. В родительском
```angular2html
<button ... >ТО ЧТО БУДЕТ В ДОЧЕРНЕМ</button>
//или <button ... >{{ func() or ?: }}</button>
```   

### Передача (ПРОБРОС) параметров и свойств между компонентами 
Например, из родительского в дочерний 
1. Создать компонент 
2. Импортировать в родительском 
3. Добавить в components 
4. добавить для всех дочерних 
```angular2html
provide: { 'title' 'item': this.news // <- this НЕЛЬЗЯ!!! }
или 
```
```angular2html
provide() {
  return { 
    title: 'ddd', 
    items: this.news 
  } 
}
``` 
5. в дочернем 
```angular2html
inject: ['title', 'items']
```   

### Валидация 
1. В родительском 
```angular2html
props: [ 
  title: { 
    type: String, // Array | Number | Boolean | Proxy | Promise 
    required: false, 
    default: false, // если required false
    validator(value) { 
      return true // обязательно вернуть true | false 
    } 
  }, 
  isOpen: Boolean, 
] 
```
2. В дочернем берем из data
```angular2html
data() { 
  title: 'Title here', 
  isOpen: false 
}
```   
3. Для передачи в компонент 
```angular2html
<app-header :title="title" :is-open="isOpen"></app-header>
```   

### Из компонента в родительский 
через emit 
1. В дочернем указываем функцию 
```angular2html
<button @click='openNews'>Кликни</button>
```   
Если в одну функция в 1 строку, то: 
```angular2html
@click="$emit('open-news', id)" // без this!
```
2. добавить в эмит 
```angular2html
emits: ['open-news']
```   
3. Тут же пишем эту функцию 
```angular2html
methods: { 
  openNews() { 
    this.isNewOpen = !this.isNewOpen this.$emit('open-news', this.id) //this!! название события для отправки в родительский 
  } 
}
```
> ! УСЛОВИЯ ПОДСЧЕТА ДЕЛАТЬ ТУТ
> ! В эмит можно передавать сколько угодно данных   

3. При изменении локального параметра сделать копию в data
```angular2html
data() { 
  return { 
    isNewOpen = this.isOpen // isOpend берем из принимающих props 
  } 
}
```   
4. Если п.3, то переключить изменяющийся параметр на эту функцию   
5. В родителе добавить 
```angular2html
<app-news v-on:open-news="openRate++"
...
// или @open-news="openCurrentNews"
```   
6. Тут же 
```angular2html
methods: { 
  openCurrentNews(data) { 
    // логика метода log(data)
    // значения после первого из $emit 
  } 
}
```   

### Валидация исходящего события 
для других разрабов, чтобы знать, что компонент отдает наверх 
```angular2html
emits: [ 'open-news' ]
```
или 
```angular2html
emits: { 
  'open-news': null, // не валидируем 
  'read-news'(data) { 
    if(!data) return false 
  } 
}
```

### Описание свойств компонента 
1. В компоненте 
```angular2html
<button :color="color">Кликни</button>
```   
2. В свойствах:
```angular2html
props: { 
  color: { 
    type: String, 
    default: '', 
    validator(value) {
      ... 
    } 
  } 
}
```   

### Изоляция стиля в компонентах 
```angular2html
<style scoped> 
    ... 
</style>
```

### Вывод текста по умолчанию в slot 
```angular2html
<slot>No text</slot>
```
или
```angular2html
<slot name="default">No text</slot>
```

### Несколько слотов - Именованный слот 
1. В родительском 
```angular2html
<template v-slot:header> 
    ... 
</template>
```   
или 
```angular2html
<template #header> 
    ... 
</template>
```
2. В дочернем 
```angular2html
<slot name="header"></slot>
```   

### Условия для несколько слотов - Именованный слот
Если выводим группу и получается пустой div 
```angular2html
<div v-if="$slots.footer">
    <slot name="footer"></slot>
</div>
```
, где $slots - системный объект (для просмотра можно использовать
```angular2html
mounted() { 
  log(this.$slots } // -> footer - имя слота 
```

### Передача параметров в слот 
#### Передача структуры в итерируемый объект 
1. В дочернем 
```angular2html
<slot :iter="item"></slot>
```
2. В родительском 
```angular2html
<template #default="slotProps"> //название слота и объект
    <span style="color: red;">{{ slotProps.iter }}</span>
</template>
```
или 
```angular2html
<template #default="{{ iter }}"> //название слота и объект <span style="color: red;">{{ iter }}</span> </template>
```

### Динамические компоненты 
1. В родительском 
```angular2html
<component :is="AppTextOne"> //название компонента
</component>
```
! НО лучше 
1a. добавляем
```angular2html
computed: { 
  componentName() { 
    return 'app-text-one' //НАЗВАНИЕ ЗАРЕГ КОМПОНЕНТА 
  } 
}
```
 2. В родительском 
```angular2html
<component :is="componentName"> //название компонента
```    

### Сохранение данных в динамических формах 
компоненты по умолчанию перерисовывается! 
чтобы избежать:
```angular2html
<keep-alive>
    <component :is="componentName"></component> 
</keep-alive>
```

### get set в computed 
Использование только для объекта!! 
```angular2html
componentName: { 
  get() { 
    return 'app-text-' + this.active 
  },
  set(value) { 
    console.log('componentName', value) 
  } 
},
```

### Доступ до компонента 
ref 
Вызов метода у компонента 
1. Добавляем в родительском к компоненту референцию 
```angular2html
<app-button 
  ref="myBtn" //референция на компонент 
  :color="oneColor" 
  @action="active = 'one'" >One</app-button>
```   
2. В дочернем создаем метод 
```angular2html
methods: { 
  btnLog() { 
    ... 
  } 
}
```   
3. В computed
```angular2html
this.$refs.myBtn.btnLog()
```
   
### Асинхронные компоненты 
Для оптимизации загрузки компонента 
1. Создать компонент 
2. не импортировать в App.vue (или др. компоненты)
3. в main.js 
```angular2html
import { createApp, defineAsyncComponent } from 'vue'
```
4. добавить как компонент в приложение 
```angular2html
app.component('async-component', defineAsyncComponent(() => {
  return import('./AppAsyncComponent') 
}))
```
, где async-component - название компонента 
      defineAsyncComponent - импортированная функция Vue должна вернуть импорт компонента 
     './AppAsyncComponent' - путь до компонента 
5. убедиться
   > консоль-network-js-0.js(?)-Preview-Cntrl+f (слово из компонента)
```angular2html
-> должно найти слово из компонента
```   

### Форма Для отправки нормы 
1. Для формы чтобы страница не обновлялась
```angular2html
<form @submit.prevent='submitHandler'
```
2. Тут же в <script> 
```angular2html
methods: { 
  submitMethods() {  
    ...
  } 
}
```

### Получение данных из input 
1. Добавить в инпут 
```angular2html
<input v-model.trim="name" // то же самое :value="" @input="" двухстороннее связывание
```   
2. добавить свойство в data 
```angular2html
data() { 
  return { 
    name: '', 
  } 
}
```   
3. Получаем в слушателе: 
```angular2html
submitHandler() { 
  console.log('Name', this.name) 
}
```   

или  
1. Указываем ref
```angular2html
<input ref="nameInput"
```
2. В консоли доступ до значения
```angular2html
console.log('Name ref', this.$refs.nameInput.value)
```   

### Данные из input число 
1. Указываем модель
```angular2html
<input type="number" id="age" max="70" v-model.number="age" // type - чтобы было число
```   
2. В data можно указать дефолтное значение
```angular2html
data() { 
  retrun { 
    age: 23 //дефолтное значение 
  } 
} 
```   

### Обработка select 
1. Добавить в инпут модель
```angular2html
<select id="city" v-model="city">
```
2. Добавить в дата 
```angular2html
data() { 
  return { 
    city: "nsk" //значение по умолчанию 
  } 
}
```   

### Данные из радиобоксов 
1. В КАЖДЫЙ БЛОК добавить - v-model и name должны совпадать! 
```angular2html
<input type="radio" v-model="relocate" name="trip"/>
```
2. Обязательно в этом же интупе добавить value="yes" в другом другое value="no"
3. В дата внести 
```angular2html
data() { 
  return { 
    relocate: 'yes', 
  } 
} 
```

### Данные из чекбоксов 
1. В инпуть добавить v-model, value, name 
```angular2html
<input type="checkbox" v-model="skills" value="vuex" name="skills"
```
2. В дата внести массив если группа 
```angular2html
data() { 
  return { 
    skills: [] //для сингла можно false-true 
  } 
}
```   

### Валидация формы 
1. Программно 
1.1. добавить метод 
```angular2html
formIsValide() { 
  let isValid = true 
  if(this.name.length === 0) { 
    isValid = false 
  } else { 
    this.errors.name = null 
  }  
  return isValid; 
},
```   
2. Добавить в дата объект для сбора ошибок 
```angular2html
data() { 
  return { 
    errors: { 
      name: null //ошибок у поля по умолчанию нет 
    } 
  } 
}
```   
3. Добавить место вывода ошибок внутри дива 
```angular2html
<small v-if="errors.name">{{ errors.name }}</small> 
```   
4. В валидируемом поле добавить динамическое свойство :class 
```angular2html
<div class="form-control" :class="{invalid: errors.name}"></div>
```
5. Добавить классы в стили 
```angular2html
.form-control small { color: #cc3867; }
.form-control.invalid input { border-color: #cc3867; }
```   

### Создание своего элемента формы 
Для универсальности и использования в разных местах кастомность! 
1. Создать компонент  
2. vbase -> <TAB>  
3. в <tepmlate> внести верстку  
4. сделать import 
```angular2html
import AppInput from "./AppInput";
```
5. добавить в компоненты
```angular2html
components: {AppInput}
```   
6. в props внести недостающие данные 
```angular2html
props: { 
  error: String, 
  label: String, 
  placeholder: String, 
}
```   
7. Необходимые для кастомизации пропсы байндим 
```angular2html
<input type="text" id="" :placeholder="placeholder" //новое из пункта 6 v-model.trim="name"
```   
8. В дата добавляем генерируемые по умолчанию значения 
```angular2html
data() { 
  return { 
    id: 'input-' + Math.random() 
  } 
} 
```   
9. Добавляем в пропс спецзарезервированное слово для модели 
```angular2html
props: { modelValue: String } 
```
10. Указываем прослушку события в emits 
```angular2html
emits: ['update:modelValue']
```    
11. В инпуте добавляем значение и название метода прослушки 
```angular2html
<input ... :value="modelValue" @input="change"
```    
12. Добавляем метод 
```angular2html
methods: { 
  change(event) { 
    this.$emit('update:modelValue', event.target.value) 
  } 
}
```    
13. modelValue можно изменить на кастомное
  а) В отправке компонента указать v-model:value="name" 
  б) В компоненте слушать emits: ['update:value'] 
  в) В методе использовать указатель того же эмита 
```angular2html
this.$emit('update:value', event.target.value)
```    

## Mixins Миксины
Необходимы для вынося повторяющего функционала
1. Создать файл миксина src/alertMixin.js
2. Вынести в него повторяющие методы, данные
```angular2html
export default {
  data() {
    return {
      alert: false
    }
  },
  methods: {
    toggleAlert() {
      this.alert = !this.alert
    }
  }
}
```
3. импортировать миксин в файл, где его нужно применить
```angular2html
import alertMixin from "@/alertMixin";
```
4. добавить в экспорт
```angular2html
mixins: [alertMixin],
```
5. использование методов и данных в данном файле
```angular2html
<button class="btn primary" @click="toggleAlert">{{ alert ? 'Hide info' : 'Show info' }}</button>
```

## Directives Директивы
Создание своих директив помимо стандратных v-if, v-for и т.д.
#### В глобальной области видимости
1. В main.js добавить метод директивы к прлиожению
```angular2html
.directive('focus', { //будет v-focus
  mounted(el) { //хуки
    el.focus()
  }
})
```
2. Добавить директиву (v-focus) для элемента
```angular2html
<input v-focus id="inp" type="text">
```

#### Локальная область видимости
1. В используемом файле добавить
```angular2html
directives: {
  focus: { //ключ - название директивы
    mounted(el) {
      el.focus();
    }
  }
},
```
2. Добавить директиву (v-focus) для элемента
```angular2html
<input v-focus id="inp" type="text">
```

#### В отдельном файле
1. Создать файл (focusDirective.js)
2. Добавить в этот файл описание директивы
```angular2html
export default {
  mounted(el) {
    el.focus();
  }
}
```
3. Импортировать в используемый файл:
```angular2html
import focusDirective from './focusDirective';
```
4. Добавить там же в директивы
```angular2html
directives: {
  focus: focusDirective,
}
```
5. Добавить директиву (v-focus) для элемента
```angular2html
<input v-focus id="inp" type="text">
```

### Жизненные циклы директивы
1. created // хук до добавления элемента
2. beforeMount // перед монтированием элемента
3. mounted // элемент в DOM-структуре документа
4. beforeUpdate // перед обновлением элемента
5. update // обновление элемента
6. beforeUnmount // перед удалением элемента из DOM-дерева
7. unmounted // удаление элемента (уничтожение)

### Mounted
mounted(el, binding) {
, где el - текущий элемент
      binding - объект Proxy

#### Изменение динамических параметров
1. для доступа к значению
```angular2html
binding.value
```
2. изменение значения элемента:
```angular2html
el.style.color = binding.value
```
В mounted событие произойдет только один раз, поэтому для многократного применения используется updated(el, binding)

### Передача параметров в директиву
1. К директиве добавить
```angular2html
v-color:[type] // появление arg в Proxy объекта
```
2. Обращение к аргументу
```angular2html
el.style[binding.arg] = binding.value
```
3. Изменение аргумента снаружи
```angular2html
<button class="btn" @click="type = type === 'color' ? 'backgroundColor' : 'color'">Change type</button>
```
4. Добавить в дата
```angular2html
data() {
  return {
    type: 'color', //type - переменная для параметра, color - навзание аргумента
  }
},
```

### Modifiers Модификаторы
По факту метод к директиве, может быть несколько
1. Добавить к директиве модификатор после точки
```angular2html
<h2 v-color:[type].blink.hover="myColor">Talk about directives</h2>
```
2. Проверить существование в описаниии директивы
```angular2html
mounted(el, binding) {
  if(binding.modifiers.blink) {
```
3. Добавить логику после проверки

> ### Добавление мигания элемента
> ```angular2html
> if(binding.modifiers.blink) {
>   let flag = true
>     interval = setInterval(() => {
>       el.style.color = flag ? '#fff' : binding.value
>       flag = !flag
>     }, 500)
>  }
> ```

### Добавление события к модификатору
1. Создать отдельный метод с основной логикой и для корректного удаления слушателя, так как после unmounted элемента нет, а слушатель остается
```angular2html
const mouseover = event => {
    console.log('mouseover')
    event.target.style.color = 'red'
}
const mouseout = event => {
    console.log('mouseout')
    event.target.style.color = defaultColor
}
```
, где target - элемент для прослушки

2. В unmouted(el) добавляем удаление слушателя
```angular2html
el.removeEventListener('mouseover', mouseover)
el.removeEventListener('mouseout', mouseout)
```
3. При mounted после проверки существования модификатора устанавливаем слушателя
```angular2html
if(binding.modifiers.hover) {
  el.addEventListener('mouseover', mouseover) //модификатор + функция
  el.addEventListener('mouseout', mouseout)
}
```

## Плагины
Для расширения базового функционала
### Создание плагина
1. Создаем файл плагина (translatePlugin.js)
2. Импортируем в main.js
```angular2html
import translatePlugin from "@/translatePlugin";
```
3. Вызвать в приложении метод use
```angular2html
const app = createApp(App)
app.use(translatePlugin)
app.mount('#app')
```
4. В созданном файле плагина реализуем минимальный метод install
```angular2html
export default {
    install(app, options) { //app - приложение, options - параметры, объект {test: 1}
        console.log(app);
        console.log(options);
    }
}
```

### Создание метода в плагине
1. в install(app, options) 
```angular2html
app.config.globalProperties.$alert = text =>{ // создание глобальных свойств ($alert)
  window.alert(text)
}
```
2. Вызов метода
```angular2html
<button class="btn" @click="$alert('Wow! it is working!')">Click</button>
```
-- или
```angular2html
mounted() {
  this.$alert('Wow! it is method!')
}
```
-- или (для сложного варианта)
3. Создать метод в плагине:
```angular2html
app.config.globalProperties.$i18n = key => {
  return key.split('.').reduce((words, k) => {
    return words[k] || '=== UNKNOWN ==='
  }, options[current])
}
```
4. Передать его между плагинами с помощью provide
```angular2html
app.provide('changeI18N', changeLanguage)
```
5. Создать там же метод, передаваемый в provide
```angular2html
const changeLanguage = name => {
  console.log(name)
  return name === 'en' ? 'ru' : 'en'
}
```
6. Принять в нужном файле через inject
```angular2html
inject: ['changeI18N'],
```
7. Создать метод для вызова
```angular2html
methods: {
  changeLang() {
    console.log('B', this.lang)
    this.lang = this.changeI18N(this.lang) //вызывается инжектированный метод
    console.log('A', cthis.lang)
    this.$forceUpdate() // и принудительно обновляется
  }
 }
}
```
8. Проверить наличие в методе принудильного обноволения
```angular2html
this.$forceUpdate()
```
9. Вызвать созданный метод
```angular2html
<button class="btn" @click="changeLang">{{ $i18n('app.switchBtn') }}</button>
```

## Teleport
Для семантического красоты кода путем переноса кода внутри html-документа
1. Вставить в html 
```angular2html
<teleport to="body"> //указать элемент для переноса внутреннего содержимого или #modal
  ...
</teleport>
```
2. Исключить телепорт
```angular2html
<teleport to="body" disabled> //не переносит, например для видео
  ...
</teleport>
```



## Routers Роутеры
Для перехода по "страницам" приложения

1. Для начала нужно установить часть экосистемы вью - роутер
```angular2html
npm install vue-router@4
```
> //(см. версии)
2. создаем файл router.js в корне проекта
```angular2html
import { createRouter, createWebHistory } from "vue-router";
import Login from "@/views/Login";

export default createRouter( {
    history: createWebHistory(), //мод для сохранения истории, этот - обычно для веб-приложений
    routes: [
        { path: '/login', component: Login } //domain.ru:port/login
    ]
})
```
3. Для каждого роутера добавить path
4. В main.js имортируем роутер
```angular2html
import router from './router'
```
5. Там же используем как компонент
```angular2html
createApp(App)
  .use(router)  // <- использование роутера в проекте как плагин
  .mount('#app')
```


### Использование роутера
1. В верстке добавляем тэг
```angular2html
<router-view></router-view>
```

### Указание дополнительных путей
alias
Используется для перенаправления на нужные пути или, например, для страницы по умолчанию

1. Указать в роутере alias
```angular2html
{ path: '/login', component: Login, alias: '/' },
```


### Динамические переходы
Для SPA используется router-link

1. В нужном месте указать тэг
```angular2html
<router-link to="/forget">Восстановить пароль</router-link>
```


### Название класса для активных ссылок
1. Добавить в router.js
```angular2html
linkActiveClass: 'active',
linkExactActiveClass: 'active',
```


### Запомнить урл при выходе
Работа с query параметрами

1. В методе можно передать объект
```angular2html
logout() {
      this.isAuth = false
      this.$router.push({
        path: '/login',  // <- путь (как было раньше)
        query: {         // <- формирование квери-парамтеров
          page: this.$route.path  // <- квери-параметр с текущим урлом
        }
      })
    }
```
2. В методе логина проверить существование квери и перекинуть пользователя по этому адресу:
```angular2html
if(this.$route.query.page) {
        this.$router.push(this.$route.query.page)
      }
```


### Динамические параметры в URL
1. Добавить в router.js название передаваемого параметра после двоеточия
```angular2html
{ path: '/mail/:mailId?', component: Mail }, //<- ? показывает, что параметр не обязательный
```
2. Указать в принимающем файле в пропсах его название
```angular2html
props: ['mailId']
```
3. Отправить его в вызывающем компоненте
```angular2html
<app-email-body :mail-id="$route.params.mailId"></app-email-body>
```
4. Для получения данных в компоненте-получателе можно добавить computer-свойство:
```angular2html
computed: {
  email() {
    return this.emails.find(e => e.id == this.mailId)
  }
```
5. На выходе шага 4 получаем объект, в котором можем использовать свойства
```angular2html
<h2>{{ email.theme }}</h2>
```


### Вложенные роуты
1. В router.js добавляем childern и переносим параметры на уровень ниже
```angular2html
{ path: '/mail', component: Mail, children: [
                { path: ':mailId', component: AppEmailBody}
        ]},
```
2. Заменяем вывод компонента на конструкцию
```angular2html
<router-view></router-view>
```
3. Удалить регистрацию этого компонента
4. В роутере передать разрешить использовать props
```angular2html
{ path: ':mailId', component: AppEmailBody, props: true } <- использование props: ['mailId'] в компоненте
```
5. Добавить маркер "?" необязательного параметра для отработки пути без параметра
```angular2html
{ path: ':mailId?', component: AppEmailBody, props: true } 
```

### Ручное управление состоянием ссылки
Например, для подсвечивания пункта меню при выборе вложенного урл

1. Добавить кастомизацию и v-slot в роутер 
```angular2html
<router-link custom v-slot="{ navigate, href }" to="/mail">
```
2. Добавить ссылку с @click и биндингом класса элемента:
```angular2html
<a href="#" @click="navigate" :class="{
          ... // логика подсчета  
          }">Почта</a>
```
3. Реализовать логику подсчета
```angular2html
active: $route.path.indexOf(href) !== -1
```


### 404 ошибка (переход на несуществую страницу)
1. Указать в router.js путь:
```angular2html
{ path: '/:notFound(.*)', redirect: '/login'} //<- редирект на необходимую страницу
```
-- или
1. Указать компонент вместо редиректа после его создания и импорта
```angular2html
{ path: '/:notFound(.*)', component: NotFound}
```


### 


### 
