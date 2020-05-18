### 1、call手写实现

>基本实现原理：
>
>1. 将函数设为对象的属性（第一个参数传进来的对象，如果不传就是window）
>2. 执行这个函数(在call2函数里面的this就是这个函数)
>3. 删除这个函数（严格说是删除属性 delete context.fn）
>

这里最难理解的就是这个context.fn=this,这个this了，理解下`fn.call()`,call是方法，为什么fn能调用方法，是不是fn也是即是一个函数也是一个对象，所以里面的this，其实就是fn函数本身

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

function fn() {}
fn.call()

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



### 2、apply手写实现

第二个参数传不是数组的会抛出错误

```js
Function.prototype.apply2 = function (context, arr) {
    //传的不是对象，强制转换为对象
    var context = Object(context) || window;
    context.fn = this;

    var result;
    if (!arr) {
        result = context.fn();
    }else if(arr && !Array.isArray(arr) ) {
       throw new TypeError("CreateListFromArrayLike called on non-object")  
    }
    else {
        var args = [];
        for (var i = 0, len = arr.length; i < len; i++) {
            args.push('arr[' + i + ']');
        }
        result = eval('context.fn(' + args + ')')
    }

    delete context.fn
    return result;
}
```



### 3、bind手写实现

`bind`方法会创建一个新函数。当这个新函数被调用时，`bind`的第一个参数将作为它运行时的`this`(该参数不能被重写), 之后的一序列参数将会在传递的实参前传入作为它的参数。
新函数也能使用`new`操作符创建对象：这种行为就像把原函数当成构造器，提供的`this`值被忽略