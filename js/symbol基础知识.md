[TOC]



## 1、概述

​      ES5 的对象属性名都是字符串，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。这就是 ES6 引入`Symbol`的原因。

​	ES6 引入了一种新的原始数据类型`Symbol`，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，前六种是：`undefined`、`null`、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。

> 注意，`Symbol`函数前不能使用`new`命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。也就是说，由于 Symbol 值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。



## 2、Symbol基本使用

**Symbol可以加字符串参数，主要为了打印在控制台好区分，如果不加参数，它们在控制台的输出都是Symbol()**

```js
let s1 = Symbol('foo');
let s2 = Symbol('bar');

console.log(s1) // Symbol(foo)
console.log(s2) // Symbol(bar)
```

**Symbol转换为字符串**

现在还没发现转为字符串的意义

```js
let s1 = Symbol('foo');
let s2 = Symbol('bar');
console.log(s1.toString()) // "Symbol(foo)"
console.log(s2.toString()) // "Symbol(bar)"
```

**symbol类型不能进行运算**

```js
let s1 = Symbol('foo');
console.log(s1+'aa')  //Uncaught TypeError: Cannot convert a Symbol value to a string
```

**symbol参数传入对象**

```js
//参入传入对象，对象会调用toString方法
const obj ={name:18}
const sym = Symbol(obj);
console.log(sym) //Symbol([object Object])
```

**每个symbol都是独一无二的**

传入相同参数Symbol也是独立唯一的，因为参数只是描述而已

```js
let s1 = Symbol();
let s2 = Symbol();
console.log(s1 === s2) //false
```

**使用`description`可以获取传入的描述参数**

```
let hd = Symbol("wangling");
console.log(hd.description); //wangling
```

## 3、Symbol常用技巧

在工作中，经常会跟同事协作开发，假如同事写了一个工具类暴露出来，里面有很多属性

```js
let obj = {
    name: '张三'，
    age: 18,
    ...
}
```

这时候你想往里面添加一个属性加name,这个时候就把上面的覆盖了

```js
obj.name ='aaa';
```

现在为了避免属性被覆盖，我们有symbol的值来作为属性

> 注意：Symbol作为属性时，访问不能有obj.s1这种形式，而应该用obj[s1],s1是变量

```js
let obj = {};
let s1 = Symbol('s1');

obj[s1] = 'zhao';
console.log(obj[s1]) //zhao
console.log(obj.s1) //undefined
```



## 4、属性名的遍历

#### **Object.getOwnPropertySymbols()**

- 当前对象的所有用作属性名的 Symbol 值
- 返回一个数组

Symbol 作为属性名，遍历对象的时候，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。

但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols()`方法，可以获取指定对象的所有 Symbol 属性名。该方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。

```js
const obj = {};
let a = Symbol('a');
let b = Symbol('b');

obj[a] = 'Hello';
obj[b] = 'World';

const objectSymbols = Object.getOwnPropertySymbols(obj);

objectSymbols
// [Symbol(a), Symbol(b)]
```

下面是另一个例子，`Object.getOwnPropertySymbols()`方法与`for...in`循环、`Object.getOwnPropertyNames`方法进行对比的例子。

```js
const obj = {};
const foo = Symbol('foo');

obj[foo] = 'bar';

for (let i in obj) {
  console.log(i); // 无输出
}

Object.getOwnPropertyNames(obj) // []
Object.getOwnPropertySymbols(obj) // [Symbol(foo)]
```

上面代码中，使用`for...in`循环和`Object.getOwnPropertyNames()`方法都得不到 Symbol 键名，需要使用`Object.getOwnPropertySymbols()`方法。



#### Reflect.ownKeys()

使用 `Reflect.ownKeys(obj)` 获取所有属性包括`Symbol`

另一个新的 API，`Reflect.ownKeys()`方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。

```js
let obj = {
  [Symbol('my_key')]: 1,
  enum: 2,
  nonEnum: 3
};

Reflect.ownKeys(obj)
//  ["enum", "nonEnum", Symbol(my_key)]
```



## 5、Symbol.for()，Symbol.keyFor()

#### Symbol.for()使用

有时，我们希望重新使用同一个 Symbol 值，`Symbol.for()`方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建一个以该字符串为名称的 Symbol 值，并将其注册到全局。

```js
let s1 = Symbol.for('foo');
let s2 = Symbol.for('foo');

console.log(s1 === s2) // true
```

#### Symbol.for()和Symbol()的区别

`Symbol.for()`与`Symbol()`这两种写法，都会生成新的 Symbol。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。`Symbol.for()`不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的`key`是否已经存在，如果不存在才会新建一个值。比如，如果你调用`Symbol.for("cat")`30 次，每次都会返回同一个 Symbol 值，但是调用`Symbol("cat")`30 次，会返回 30 个不同的 Symbol 值。

```js
Symbol.for("bar") === Symbol.for("bar")
// true

Symbol("bar") === Symbol("bar")
// false
```

#### Symbol.keyFor()

```js
let s1 = Symbol.for("foo");
console.log(Symbol.keyFor(s1)) // "foo"

let s2 = Symbol.for("foo")
console.log(Symbol.keyFor(s2)) //"foo"

let s3 = Symbol("foo")
console.log(Symbol.keyFor(s3)) //"undefined"
```

> 注意，`Symbol.for()`为 Symbol 值登记的名字，是全局环境的，不管有没有在全局环境运行。

```js
function foo() {
  return Symbol.for('bar');
}

const x = foo();
const y = Symbol.for('bar');
console.log(x === y); // true
```

上面代码中，`Symbol.for('bar')`是函数内部运行的，但是生成的 Symbol 值是登记在全局环境的。所以，第二次运行`Symbol.for('bar')`可以取到这个 Symbol 值。

`Symbol.for()`的这个全局登记特性，可以用在不同的 iframe 或 service worker 中取到同一个值。

```js
iframe = document.createElement('iframe');
iframe.src = String(window.location);
document.body.appendChild(iframe);

iframe.contentWindow.Symbol.for('foo') === Symbol.for('foo')
// true
```

上面代码中，iframe 窗口生成的 Symbol 值，可以在主页面得到。



## 6、实例操作

#### 1、缓存操作

使用`Symbol`可以解决在保存数据时由于名称相同造成的耦合覆盖问题。

```js
class Cache {
  static data = {};
  static set(name, value) {
    this.data[name] = value;
  }
  static get(name) {
    return this.data[name];
  }
}

let user = {
  name: "wangling",
  key: Symbol("缓存")
};

let cart = {
  name: "购物车",
  key: Symbol("购物车")
};

Cache.set(user.key, user);
Cache.set(cart.key, cart);
console.log(Cache.get(user.key));
```





