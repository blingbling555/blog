

#  插槽slot 2.6版本详解

 slot的出现是为了父组件可以堂而皇之地在子组件中加入内容 

## 1、插槽内容

### 1.1、一个插槽

默认情况下，在子组件里面插入内容是不会显示出来的，下面Son里面h1是没有显示出来的

- 父组件

```

<div class="father">
     <Son>
         <h1>在子组件插入内容</h1>
     </Son>
   </div>
```

- 子组件

```
<div class="son">dd</div>
```

怎么才能显示出来呢？这时候看到最开始那句话  **slot的出现是为了父组件可以堂而皇之地在子组件中加入内容** ，这时候就要用到slot，子组件做如下更改：

- 子组件更改

  > 用了slot，你在父组件里面加的任何标签，都会显示在子组件里面。

```
<div class="son">
    dd
    <slot></slot>
</div>
```



#### 1.2、多个插槽（具名插槽）

 具名插槽也很简单，比如有多个插槽，我作为父组件，肯定想区别子组件中的几个插槽，那就要用slot标签的name属性来标识了 

这里偷懒下，借鉴官方文档，其实也就是在子组件里面写了多个slot，你怎么知道你写的内容是哪个slot的，默认你不命名，每个slot都会把你写在子组件的内容拷贝一份。

- 子组件（没有命名的，其实有个默认的名字叫default）

```
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

- 父组件

  > 这里很明显了，父组件往子组件对应的位置填充内容时，用v-slot:名字
  >
  >  现在 ` 元素中的所有内容都将会被传入相应的插槽。任何没有被包裹在带有 `v-slot` 的 ` 中的内容都会被视为默认插槽的内容。 

  ```
  <base-layout>
    <template v-slot:header>
      <h1>Here might be a page title</h1>
    </template>
  
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  
    <template v-slot:footer>
      <p>Here's some contact info</p>
    </template>
  </base-layout>
  ```

## 2、作用域插槽（2.6）

 通常情况下普通的插槽是父组件使用插槽过程中传入东西决定了插槽的内容。但有时我们需要获取到子组件提供的一些数据，那么作用域插槽就排上用场了。 

### 2.1、使用

- 子组件

```
<slot :data="data"></slot>

 data () {
 return {
 data: { // 内部状态
 username: 'oli'
 }
 }
 }
```

- 父组件

  > 1、user你随意写名字，不重要
  >
  > 2、 也可以不书写 default 关键字，默认就是假定对应默认插槽 

```
<template v-slot:default="user">{{user.data.username}}</template>
```

 使用 v-slot 绑定一个命名空间 user，这样就可以通过 user 对象引用到子组件中传入的数据了 ，可以传多个数据，都在user这个对象里面可以引用到

### 2.2、解构插槽

 作用域插槽的内部工作原理是将你的插槽内容包括在一个传入单个参数的函数里 

 这意味着 `v-slot` 的值实际上可以是任何能够作为函数定义中的参数的 JavaScript 表达式。所以在支持的环境下 ([单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)或[现代浏览器](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#浏览器兼容))，你也可以使用 [ES2015 解构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#解构对象)来传入具体的插槽 prop，如下： 

- 父组件改造

  ```
  <template v-slot:default="{data}">{{data.username}}</template>
  ```

### 2.3、具名插槽的缩写

注意是具名插槽，这里把官网的搬过来

 跟 `v-on` 和 `v-bind` 一样，`v-slot` 也有缩写，即把参数之前的所有内容 (`v-slot:`) 替换为字符 `#`。例如 `v-slot:header` 可以被重写为 `#header`： 

```
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

然而，和其它指令一样，该缩写只在其有参数的时候才可用。这意味着以下语法是无效的：

```
<!-- 这样会触发一个警告 -->
<current-user #="{ user }">
  {{ user.firstName }}
</current-user>
```

如果你希望使用缩写的话，你必须始终以明确插槽名取而代之：

```
<current-user #default="{ user }">
  {{ user.firstName }}
</current-user>
```

# 参考链接

[vue作用域插槽，你真的懂了吗？](https://www.jianshu.com/p/e10baeff888d)

[官方链接](https://cn.vuejs.org/v2/guide/components-slots.html)

[详解Vue 匿名、具名和作用域插槽的使用方法](https://www.jb51.net/article/160043.htm)

