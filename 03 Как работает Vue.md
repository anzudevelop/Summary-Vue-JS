# 3 Как работает Vue

## 3.1 Свойство template

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="style.css">
    <title>Document</title>
</head>
<body>
    <div id="app" class="container noselect">
        
    </div>

    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <script src="app.js"></script>
</body>
</html>
```

```js
const app = Vue.createApp({
    data() {
        return {
            title: 'Это template'
        }
    },
    template: `
        <div class="card">
            <h1>{{ title }}</h1>
            <div class="btn" @click="title = 'Измененный title'">Изменить</div>
        </div>
    `
})

app.mount('#app')
```

<div style="text-align:center"><img src="img/03template_1.png" /></div>

## 3.2 Virtual DOM и render
В своем ядре vue использует концепт, который называется virtual dom. В js есть проблема, связка js и dom дерева очень дорога по времени. Ценность вреймворка именно в оптимизации этого процесса. 
Идея в том, что мы изначально создаем виртуальную модель dom дерева, далее делаем сам реальный dom. При этом есть промежуточнй этап, в виде объекта, который описывает все свойства реального dom дерева. При дальнейши изменениях мы сравниваем то, что у нас изменилось с виртуальным dom деревом и находим только то место, которое должны отрисовать. Точечно его изменяем и минимизируем взаимодействие с реальным dom деревом. 

Функция render() позволяет рендерить виртуальный дом. Напишем самостоятельно то, что делает template. Vue на самом деле берет шаблон template, парсит его и превращает в js код.

```js
const h = Vue.h

const app = Vue.createApp({
    data() {
        return {
            title: 'Это template'
        }
    },
    methods: {
        changeTitle() {
            this.title = 'Измененный title'
        },
    },
    // template: `
    //     <div class="card">
    //         <h1>{{ title }}</h1>
    //         <div class="btn" @click="title = 'Измененный title'">Изменить</div>
    //     </div>
    // `
    render() {
        return h('div', {
            class: 'card'
        }, [
            h('h1', {}, this.title),
            h('div', {
                class: 'btn',
                onclick: this.changeTitle
            }, 'Изменить')
        ])
    }
})

app.mount('#app')
```

<div style="text-align:center"><img src="img/03template_2.png" /></div>

## 3.3 Реактивность и Proxy
Механика работы vue:
В первую очередь мы создаем data, но потом обращаемся к this. Vue на самом деле берет data и совмещает его с другими компонентами, оборачивая в proxy, этот концепт называется реактивностью.
Мы можем самостоятельно обернуть какой-то объект в прокси, вообще это за нас делает vue.

```js
const data = {
    title: 'Vue 3',
    message: 'Заголовок это: Vue 3'
}
const proxy = new Proxy(data, {
    // get(target, p) {
    //     console.log(target)
    //     console.log(p)
    // },
    set(target, key, value) {
        if(key === 'title') {
            target.message = 'Заголовок это: ' + value
        }
        target[key] = value
    }
})
proxy.title = 'React'
console.log(proxy)
```
Меняя заголовок, у нас меняется сообщение. Это и есть реактивность, происходит все автоматически.

## 3.4 Жизненный цикл компонента
<div style="text-align:center"><img src="img/03template_3.png" /></div>

[Хуки жизненного цикла](https://ru.vuejs.org/guide/essentials/lifecycle.html)