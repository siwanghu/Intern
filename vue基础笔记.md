# vue基础笔记（siwang.hu&nbsp;&nbsp;V1.0）  
> + 官方学习[网站](https://cn.vuejs.org/v2/guide/)  
## v-if&nbsp;指令  
> + 条件渲染指令  
> + 如果ok==true 界面显示Yes,否则显示No  
```
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```
> + 类似于 v-else，v-else-if 也必须紧跟在带 v-if 或者 v-else-if 的元素之后，否则它们将不会被识别  
```
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```
> + Vue会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染，Vue为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的key属性，**label**元素仍然会被高效地复用，因为它们没有添加key属性。如果不添加key属性，那么在上面的代码中切换 loginType 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素，**input**不会被替换掉——仅仅是替换了它的placeholder  
```
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```
>  
## v-show&nbsp;指令  
> 带有 v-show 的元素始终会被渲染并保留在 DOM 中。v-show 只是简单地切换元素的 CSS 属性 display  
```
<h1 v-show="ok">Hello!</h1>
```
> + v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建  
>  
> + v-show元素总是会被渲染，并且只是简单地基于 CSS 进行切换  
>  
> + v-if有更高的切换开销，而v-show有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用v-show较好；如果在运行时条件很少改变，则使用v-if较好  
## v-for&nbsp;指令  
> + v-for 指令根据一组数组的选项列表进行渲染，v-for 指令需要使用 **item in items**形式的特殊语法，items是源数据数组并且 item是数组元素迭代的别名  
```
<ul id="example">
  <li v-for="(item,index) in items">
    {{index+1}}-{{ item.message }}
  </li>
</ul>

var example = new Vue({
  el: '#example',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```
>  
> +  v-for可以迭代输出一个对象的属性  
```
<ul id="v-for-object" class="demo">
  <li v-for="(value, key) in object">
    {{ key }}: {{ value }}
  </li>
</ul>

new Vue({
  el: '#v-for-object',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
```
## v-on&nbsp;指令  
> + v-on指令监听DOM事件，并在触发时运行JavaScript代码,可以使用@简写  
```
<div id="example">
  <button v-on:click="greet">Greet</button>
</div>

var example = new Vue({
  el: '#example',
  data: {
    name: 'Vue.js'
  },

  methods: {
    greet: function (event) {
      alert('Hello ' + this.name + '!')
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})
```
## v-model&nbsp;指令
> + v-model 指令在表单 input、textarea及select等元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素，它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理  
```
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>

<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>

<div id="example-4">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>
</div>

new Vue({
  el: '#example-4',
  data: {
    picked: ''
  }
})
```
## v-bind&nbsp;指令  
> + 用来绑定元素属性,也就是Class与Style的绑定，可以简写为：  
```
<div v-bind:class="classObject"></div>
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```
## 计算属性  
> + 可以将fullname看成data域中的属性一样，不过fullname属性随着firstName与lastName的改变而改成，修改fullname也会相应改变firstName与lastName。另外需要注意的是：计算属性是基于它们的依赖进行缓存的，只在相关依赖发生改变时它们才会重新求值，也就是说只有firstName与lastName发生了改变，我们get获取fullname时才会重新执行函数获取新的fullname值。  
> *为什么需要缓存？假设我们有一个性能开销比较大的计算属性 A，它需要遍历一个巨大的数组并做大量的计算。然后我们可能有其他的计算属性依赖于 A 。如果没有缓存，我们将不可避免的多次执行 A 的 getter！*
```
<div id="demo">{{ fullName }}</div>

var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: {
        // getter
        get: function () {
        return this.firstName + ' ' + this.lastName
        },
        // setter
        set: function (newValue) {
        var names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
        }
    }
  }
})
```
## 侦听器  
> + 当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的  
```
<div id="demo">{{ fullName }}</div>

var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (newval,oldval) {
      this.fullName = newval + ' ' + this.lastName
    },
    lastName: function (newval,oldval) {
      this.fullName = this.firstName + ' ' + newval
    }
  }
})
```
> + watch和computed可以从缓存中读数据，性能较好，只要相关数据没有改变就不执行；方法性能不好，只要页面数据有改变，方法就会执行。对于某种功能，三者都能实现读情况下，优先选computed  
