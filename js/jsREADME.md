[TOC]



# 基本类型

### 有哪些基本类型，哪些是引用类型

```
基本数据类型有这五种:Undefined、Null、String、Number、Boolean,Symbol。
引用类型有这几种：object、Array、RegExp、Date、Function、特殊的基本包装类型(String、Number、Boolean)以及单体内置对象(Global、Math)。
```

### [类型检测以及判断数组对象是否相等](类型检测以及判断数组对象是否相等.md)

- ✔typeof
- ✔instanceof
- ✔Object.prototype.toString.call()
- ✔如何判断数组相等
- ✘判断对象相等
- ✘深拷贝
- ✘浅拷贝

### ✘String

### ✘Boolean

### ✘Number

### ✘Math

### ✘Date


# Function

### *`Function.prototype.call()`*

> **注意：**该方法的语法和作用与 [`apply()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法类似，只有一个区别，就是 `call()` 方法接受的是**一个参数列表**，而 `apply()` 方法接受的是**一个包含多个参数的数组**。

##### 手写实现call

>基本实现原理：
>
>1. 将函数设为对象的属性（第一个参数传进来的对象，如果不传就是window）
>2. 执行这个函数(在call2函数里面的this就是这个函数)
>3. 删除这个函数（严格说是删除属性 delete context.fn）
>
>

```js
//es5版本
Function.prototype.call2 = function (context) {
    var context = context || window;
    //这个this,就是谁调用的这个函数 this就是谁,一般就是函数本身，不然怎么执行
    context.fn = this;

    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }

    var result = eval('context.fn(' + args +')');

    delete context.fn
    return result;
}

//es6版本
Function.prototype.call2 = function(context) {
    var context = context || window
    context.fn = this // this 也就是调用call的函数
    var args = [...arguments].slice(1)
    var result = context.fn(...args)
    delete context.fn
    return result
}
```

[call手写视频教程](https://www.bilibili.com/video/BV1zE411K7gd)

[运江大神手写系列](https://github.com/heyunjiang/Blog/blob/master/es/深入js-一些功能模拟实现.md)

[使用模拟实现的方式探究call 和 apply 的原理](https://www.jianshu.com/p/92b48caee4b2)

# Array



# Object

# 参考链接

[后盾人js学习文档](http://houdunren.gitee.io/note/js/1%20%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.html)

[阮一峰ES6教程](https://es6.ruanyifeng.com/)