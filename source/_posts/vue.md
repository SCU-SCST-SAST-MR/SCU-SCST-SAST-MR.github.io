---
title: VUE 入门攻略
date: 2020-02-08 21:11:59
tags: [vue, web]
category: Angel_zou
---

注：本教程仅限本人观点与见解，如有错误，欢迎指正。
本文代码并非完整代码，只帮助理解。
本教程需要少量web基础，如果不懂可以参考w3school的教程。

<!--more-->

## 介绍

vue.js 简单来说，是一套MVVM体系的渐进式框架，让开发者从繁琐的DOM操作中解脱出来。其被设计成自底向上逐层应用，这使得开发者可以取其所需，灵活多变。
vue其核心就是只关心视图层，以较为简洁的方式对数据进行操作。易上手，易扩展。

## 安装

vue可以通过很多方式引入：

1. 直接js引入：

```js
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

2. NPM

```shell
npm install vue
```

3. 脚手架工具
   官方提供了[vue-cli](https://github.com/vuejs/vue-cli)脚手架工具，可以快速搭建基于vue的SPA应用。
   新手在不了解node.js和webpack的前提下，慎重尝试。（但真的很好用

## 起步

### Hello World

老规矩，我们先来看一个简单的例子。

```html
<div id = "app">
    {{'{'}}{{'{'}}message{{'}'}}{{'}'}}
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:'Hello World'
    }
})
```

```
Hello World
```

虽然这个例子很简单，但实际上vue已经帮我们处理了很多事情。现在数据已经绑定了对应的DOM，我们只要改动改动，页面也就响应式的发生改变。
**试着改变message的值，比如:**

```js
var app = new Vue({
    el:'#app',
    data:{
        message:'Hello Vue'
    }
})
```

对应输出也会改成：

```
Hello Vue
```

可能有人会问{{'{'}}{{'{'}}message{{'}'}}{{'}'}}是个啥？为啥用两个括号，别急，往下看，自会有解答。

## 作用域绑定

可能有人会问了，为啥 *div* 一定要加上*id = "app"* 这个属性呢。
这里 *id = 'app'* 仅仅只是给 *div* 加了一个属性而已。重要的是js中的 *el: "#app"* ，这实际上是把vue实例和 id 为 app 的DOM进行绑定，这样 vue 实例所影响的作用域就在这个DOM之下进行。

## 模板语法

Vue自带一套与HTML兼容的模板语法，允许开发者声明式的将数据和DOM绑定。

### 1.插值

#### 1.1文本

数据绑定最简单直接的方法就是用“Mustache”语法，就是上面例子中的{{'{'}}{{'{'}} {{'}'}}{{'}'}}文本形式。
我们再来看一下上面的例子：

```html
<div id = "app">
    {{'{'}}{{'{'}}message{{'}'}}{{'}'}}
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:'Hello World'
    }
})
```

简单来说，被双大括号括起来的东西，对应了data下的属性的值，比如这里被括起来的message在最后渲染的时候会被data下的同名message属性对应的值：'Hello World'代替，相当于下面这串代码:

```html
<div>Hello World</div>
```

而当你改变message属性的值，相应的渲染也会动态改变。
当然，如果你不想改变，而只想一次性规定这个值，可以使用*v-once*这个指令：

```html
<div v-once>
    {{'{'}}{{'{'}}message{{'}'}}{{'}'}}
</div>
```

这样，之后再修改data下message的属性值，其渲染结果也不会变。

**但是**，用{{'{'}}{{'{'}} {{'}'}}{{'}'}}的时候，由于vue生命周期等原因，在一些特殊的情况下会导致一些奇怪的结果，推荐写成以下形式：

```html
<div id = "app" v-text="message">
    
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:'Hello World'
    }
})
```

*v-text*的作用，就相当于{{'{'}}{{'{'}} {{'}'}}{{'}'}}的作用。

#### 1.2 插入HTML

我们要首先搞清楚一件事情，{{'{'}}{{'{'}} {{'}'}}{{'}'}}语法和*v-text*只会把数据解析成文本，也就是字符串。
除此之外{{'{'}}{{'{'}} {{'}'}}{{'}'}}也可以接受js表达式。
例如：

```html
<div id = "app" >{{'{'}}{{'{'}}message{{'}'}}{{'}'}}</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:'<a>This is a message</a>'
    }
})
```

会对应渲染成：

```
<a>This is a message</a>
```

这个时候要用到*v-text*指令：

```html
<div id = "app" v-html = 'message'></div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
        message:'<a>This is a message</a>'
    }
})
```

对应渲染：

```
This is a message
```

#### 1.3 js表达式

上文说到{{'{'}}{{'{'}} {{'}'}}{{'}'}}也可以接受js表达式，不过注意，只能接受单个表达式。
例如：

```html
<div id = "app">
    {{number +1}}
</div>
```

```js
var app = new Vue({
    el:'#app',
    data:{
       number: 1
    }
})
```

渲染结果：

```
2
```

### 2.指令

可能有人已经注意到了 *v-text v-html* 这些是个什么鬼东西？
在 vue 中，我们把 *v-*前缀的属性统称为指令。指令的值预期是单个 JavaScript 表达式 (v-for 是例外情况)。指令会在表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。
有的指令后面会接参数，其中比较重要的两个指令：**v-bind v-on**

*v-bind* 用来绑定各种html属性，用来响应式的更新html属性
例子：

```html
<a v-bind:href = "url">this is a url </a>
```

```js 
data:{
    url:'www.angel-tg.club'
}
```

最终会渲染成：

```html
<a href = "www.angel-tg.club">this is a url</a>
```

这里 *v-bind* 是指令， *：( 冒号 )* 后面接的 *href* 就是 *v-bind* 的参数，参数的值绑定到 data 下的 *url*。

*v-on* 用来监听DOM事件，为各种原生事件增加各种方法
最常用的例子，监听 *click* 事件。

```html
<div id = "app">
    <div v-on:click = "count += 1> count:{{count}} </div>
</div>
```

```js
var app = new Vue({
    el:"#app",
    data{
        count:0
    },
    
})
```

点击之后count会加1

**实用小贴士：** 
*v-bind:HtmlAttribute* 可以简写成 *:HtmlAttribute*
*v-on:DomEvent* 可以简写成 *@DomEvent*

## 常见指令（v-if 与 v-for)

### v-if

v-if 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 true的时候被渲染。
相当于if语句

```html
<div id = "app">
    <div v-if = 'judge_show'> A show </div>
    <div v-if = 'judge_show != true'> B show </div>
</div>
```

```js
var app = new Vue({
    el: "#app",
    data:{
        judge_show: true
    }
})
```

渲染结果：

```
A show
```

当然，既然有了 *v-if* 自然有 *v-else-if* 和 *v-else*
其用法和各种语言中的if语句类似，大家可以自行尝试，不再赘述。放个例子：

```html
<div v-if = "judge_show === 'A'"> A show </div>
<div v-else-if = "judge_show == 'B'"> B show </div>
<div v-else> C show </div>
```

### v-for 

*v-for* 常常用于列表渲染
举个例子：

```html
<ul>
    <li v-for = "item in items">
        {{item.message}}
    </li>
</ul>
```

```js
data:{
    items:[
        {message:"A"},
        {message:"B"}
    ]
}
```

渲染结果：

```
A
B
```

想当年，我在这个地方光想item和items和下面data的属性items的关系就想了半天，咳咳。

**解释一下：**

*v-for = "item in items"* 中

item没有特别的含义，就是个自定义的变量名，可以瞎取，代表了每个数组中的元素（就是for循环里，我们喜欢用到那个变量i）。

items对应了data下items属性，这两个必须得一样的名字，而且data下的items必须是个数组对象。

**注意**:由于js的限制，更新数组的时候，视图是不会响应式更新的，解决这个问题可以使用 *Vue.set* 具体请查看官网。

## 方法 计算属性 监听器

### 方法

先来看一个例子：

```html
<div id = "app">
    <div @click = "demoMethod">点击</div>
</div>
```

```js
var app = new Vue({
    el: "#app",
    methods:{
        demoMethod: function(){
            console.log("Hello World");
        }
    }
})
```

点击之后，会在控制台输出：

```
Hello World
```

在这里， *v-on* 也就是 *@* 可以在监听DOM事件的时候调用一个方法，*"demoMethod"* 是方法名称，对应方法的具体实现，要写在 *methods* 属性下面。

### 计算属性

模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。例如：

```html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

这是很繁琐的，而且当你想多次引用此处的反转字符时，会更加难以处理。
这个时候，我们可以使用计算属性。

```html
<div id="app">
  <p>{{ reversedMessage }}</p>
</div>
```

```js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello'
  },
  computed: {
    reversedMessage: function () {
      // `this` 指向 app 实例 
      // 这是vue的一个小特性，实例中的 this 指针大部分情况始终指向实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

可能你已经注意到了，这不就是个method方法的调用吗。
没错，计算属性和方法挺像的，但是有点区别。

**计算属性是基于它们的响应式依赖进行缓存的。**

只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 message 没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。

而method方法，只要调用了，就必定会重新执行一次。

这在某些场合，比使用method更加高效。

### 侦听属性

当然，vue实际上也提供了一种更加通用的方式来监听vue实例上的数据变动：侦听属性。

```html
<div id="app">{{ Name }}</div>
```

```js
var app = new Vue({
  el: '#app',
  data: {
    Name: 'Foo',
  },
  watch: {
        Name: function (newVal,oldVal) {

            console.log(newVal,oldVal);
    }
  }
})
```

*watch* 属性下，对应的键是要监听的表达式，一般是 *data* 下的数据，当被监听对象发生变化，就会调用对应的函数。

对应调用的函数(也就是回调函数),有两个默认参数，第一个是新值，第二个是旧值。

**提醒：普通watch不能监听对象，想要跟踪监听对象的属性，请用deep深度监听，相关内容自行百度。**

## Class与Style绑定

在vue中，动态绑定样式是很简单的事情。

有两种方式：Class与Style

### Class

我们可以传给 v-bind:class 一个**对象**，以动态地切换 class：

```html
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

```js
data: {
  isActive: true,
  hasError: false
}
```

记住，在模板语法中用到*短横线 -* 都要用单引号括起来，例如上面的 *'text-danger'*

最后渲染结果:

```html
<div class = "static active">
```

*v-bind:class* 后面跟着的对象，键是样式的名称，值应该是个布尔值，代表是否渲染这个样式 。

### Style

v-bind:style 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript **对象**。CSS 属性名可以用驼峰式 或 短横线分隔 (记得用引号括起来) 来命名：

```html
    <div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data: {
    activeColor: 'red',
    fontSize: 30
}
```

*style* 是个对象，别忘了加大括号。

**style对象中对应的键是css属性名，值是属性值。不过引用的是data下对应的名称。**

vue中，最好将数据都写在data下，当然，在数据很复杂多变的情况下，官方推荐用vuex状态管理，不过这都是后话了。

## 组件初探

组件是vue的核心，他将一系列可复用的代码串分装成一个个组件，提供更高的复用性和可拓展性，降低代码的维护成本。

先举个例子：

```js
// 定义一个名为 button-counter 的新组件
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">{{ count }}</button>'
})
```

这样我们就注册里一个组件
现在尝试取使用它：

```html
<div id="app">
  <button-counter></button-counter>
</div>
```

```js
var app = new Vue({
    el:"#app"
})
```

最终渲染：

```html
<div id="app">
  <button v-on:click="count++">{{ count }}</button>
</div>
```

这里你可能已经注意到了，data变成了一个**函数**，而不是一个对象。

这是因为组件是可复用的，像这样：

```html
<div id="app">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

但是，组件的数据应该单独拷贝一份，如果data还是一个对象，**改变一个组件实例的数据，所有组件实例数据都会改变**。

所有数据都应该放在 *return* 下。

组件部分是vue的重中之重，非三言两语可以讲清的。

如果你有兴趣，可以去官网进行更深入的学习，官网提供了全面的教程和完整的API。

愿你有了上文的基础，能在vue的道路上少走弯路。

## 附录：部分资料来源

[MVVM](https://www.liaoxuefeng.com/wiki/1022910821149312/1108898947791072)：廖雪峰
[VUE](https://cn.vuejs.org)：vue.js官方网站
[Watch](https://www.jianshu.com/p/9626712bedec): watch的不同用法