# 1、环境准备

由于ES6-10,有些代码游览器不支持

```bash
git clone https://github.com/cucygh/es-cli.git
cd es-cli
npm install
npm start
```

如果启动没有任何异常的话，直接在浏览器访问 http://localhost:8080 即可看到效果了。



# 2、es2015(es6)

### 1、作用域知识补充

##### 全局变量的2种方式

全局变量有两种方式,经过var声明的，和没有经过var声明

```js
var global_var = 1;
global_novar = 2;   // 不推荐
```

区别：

经过var声明的不能删除

没有经过var声明的，是作为了全局对象window的一个属性，可以删除

```js
// 删除变量
delete global_var;    // false
delete global_novar;    // true
```

> 疑问：global_var也是window的属性呀，不然为什么可以通过window.global_var来访问

##### 几种作用域

- 全局作用域
- 函数作用域: 函数
- 块级作用域：{}
- 动态作用域：this,同一函数执行的结果不同，是根据this的指向来决定。



### 2、let和const(待补充)

##### let和var的区别

- let有自己的作用域

  ```js
  {
  let a = 1
  }
  console.log(a); //Uncaught ReferenceError: a is not defined
  ```

- let不能重复定义一个变量

  ```js
  //es5
  var a = 1
  var a = 2
  console.log(a) //2
  
  //es6
  let a = 1
  let a = 2
  // VM131:1 Uncaught SyntaxError: Identifier 'a' has already been declared
  // at <anonymous>:1:1
  ```

- let不能进行变量提升

  ```js
  function test () {
  console.log(a)
  var a = 1
  }
  test() //undefined
  ```

  

- let不会作为一个属性挂载在window对象，通过window.不能访问

  ```js
  //es5
  var a = 1
  console.log(window.a); //1
  
  //es6
  let a = 1
  console.log(window.a); // undefined
  ```

##### const和var的区别

- 上面的注意事项都有
- 不能重新赋值

### 额外补充学习（this,作用域链）待学习

![image-20200420133950364](assets/image-20200420133950364.png)

![image-20200420134005242](assets/image-20200420134005242.png)

### 3、数组相关

#### 1、es5中数组遍历方法

for

##### forEach

```js
//forEach与for相比，不支持break和continue
```

##### every

```js
/**
arr.every(callback[, thisArg]),thisArg:回调函数this的指向
@desc: 方法测试一个数组内的所有元素是否都能通过某个指定函数的测试。它返回一个布尔值。
@return: 如果回调函数的每一次返回都为 truthy 值，返回 true ，否则返回 false。
*/


//fevery与for相比，不支持break和continue
const arr = [1,2,3,4,5];
arr.every(function(item){
  console.log(item) 
})
//上面代码只输出1，只有返回true才会继续执行

const arr = [1,2,3,4,5];
arr.every(function(item){
  console.log(item) 
    return true
})
//上面代码只输出1，2,3,4,5


想当元素等于2，退出循环，也就是要break效果,这里return false就是这个效果了
const arr = [1,2,3,4,5];
arr.every(function(item){
    if (item === 2) {
        return false
    }
  console.log(item) 
})
```

##### for in

```js
//for in,用来遍历对象的，但是支持遍历数组，数组也是对象
const arr = [1,2,3,4,5];
arr.a = 8
for (let index in arr) {
 	console.log(arr[index]) //1,2,3,4,5,8
}

//for in 支持break和continue
const arr = [1,2,3,4,5];
arr.a = 8
for (let index in arr) {
    //注意index是字符串
    if (+index === 2) {
       break;
    }
 	console.log(arr[index]) //1,2,3,4,5,8
}
```



#### 2、es6数组遍历方法

##### for of

```

```



#### 3、Array.from将伪数组转换为数组

##### 什么是伪数组

- 具备length属性
- 有索引

```js
let arr = {0: 'a', 1: 'b', length: 2};
Array.from(arr) //[a,b]
```

伪数组不能调用数组的方法，比如不能用forEach

伪数组有哪些：arguments(es6废弃使用),NOdeList，set

```js
//es5
let args = [].slice.call(arguments);

//es6
let args = Array.from(arguments)
let imgs = Array.from(document.querySelectorAll('img'))

```

##### Array.from基本语法

```js
Array.from(arrayLike[, mapFunction[, thisArg]])
arrayLike：必传参数，想要转换成数组的伪数组对象或可迭代对象。
mapFunction：可选参数，mapFunction(item，index){…} 是在集合中的每个项目上调用的函数。返回的值将插入到新集合中。
thisArg：可选参数，执行回调函数 mapFunction 时 this 对象。这个参数很少使用。
```



`Array.from`还可以接受第二个参数，作用类似于数组的`map`方法，用来对每个元素进行处理，将处理后的值放入返回的数组。

```js
Array.from([1, 2, 3], (x) => x * x)
// [1, 4, 9]
```

#### 4、Array.of创建新数组

主要是弥补Array创建数组的差异性

```js
//es5
Array() // []
Array(3) // [, , ,]
Array(3, 11, 8) // [3, 11, 8]

//es6
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```

#### 5、Array.fill填充

填充空数组比较好

```js
['a', 'b', 'c'].fill(7) // [7, 7, 7]
let array = Array(5).fill(7) //[7,7,7,7,7]

//场景：把3,4换成8
let arr = [1,2,3,4,5]
arr.fill(8,2,4)//第一个是替换成的数，第二个开始下标，第三个参数结束下标（不包含）
```



#### 6、es5数组查找

##### filter

> @return
>
> 所有满足条件的元素，找到返回新数组(回调函数return true)
>
> 没有则返回[]
>
> filter() 不会改变原始数组。



filter() 方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。

简而言之，所有满足条件的元素，找到返回新数组，没有就返回[]

```js
var ages = [32, 33, 16, 40];

function checkAdult(age) {
    return age >= 18;
}
//[32,33,40]
```



#### 7、Array.find(关注是否有这个元素)

> @return 
>
>   找到则返回找到的第一个元素
>
>   没找到则返回undefined

数组实例的`find`方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为`true`的成员，然后返回该成员。如果没有符合条件的成员，则返回`undefined`。

```js
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10
```

接收第二个参数this

```js
function f(v){
  return v > this.age;
}
let person = {name: 'John', age: 20};
[10, 12, 26, 15].find(f, person);    // 26
```



#### 8、Array.findIndex

>@return 
>
>  找到则返回找到的第一个元素下标
>
>  没找到则返回-1

数组实例的`findIndex`方法的用法与`find`方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回`-1`。

```js
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2
```



### 4、class的基本语法

#### 1、es5类的声明

```js
let Animal = function(type) {
	this.type = type;
}
Animal.prototype.eat = function(){}
let dog = new Animal('dog');
let monkey = new Animal('monkey');
```

#### 2、es6基本用法

ES6 的`class`可以看作只是一个语法糖，跟上面的效果一样

```js
class Aminal {
    constructor(type) {
		this.type = type
    }
    eat() {
     console.log('i am eat food')
    }
}

let dog = new Animal('dog');
let monkey = new Animal('monkey');
```

#### 3、取值函数（getter）和存值函数（setter）

场景：把age保护起来，不让变化

```js
class Animal {
    constructor(type) {
		this.type = type
    }
    get age() {
        return this.rage
    }
    //不能为 this.age  
    set age(value) {
        this.rage = value
    }
}

let dog = new Animal('dog');
console.log(dog.age) //4
dog.age = 5;
```

#### 4、类的静态方法

##### 实例方法和静态方法的使用场景

方法内部有用到实例相关属性，就用实例方法

其他就用静态方法，拿不到实例对象属性

##### **es5**

```js
let Animal = function(type) {
	this.type = type;
    
}
//实例方法
Animal.prototype.eat = function(){
    Animal.walk ();
    console.log("我要吃")
}
//静态方法
Animal.walk = function() { //静态方法
    console.log('i am walking')
}
let dog = new Animal('dog');
dog.eat()
```

##### **es6**

```js
class Aminal {
    constructor(type) {
		this.type = type
    }
    eat() {
     //使用静态方法
     Aminal.walk();
     console.log('i am eat food')
    }
    //静态方法
    static walk() {
     console.log('i am walking')
    }
}
let dog = new Animal('dog');
dog.eat()
```

#### 5、如何继承一个类

##### es5

```js
let Animal = function(type) {
	this.type = type;
    
}
//实例方法
Animal.prototype.eat = function(){
    Animal.walk ();
    console.log("我要吃")
}
//静态方法
Animal.walk = function() { //静态方法
    console.log('i am walking')
}
let Dog = function() {
    //实现继承
    Animal.call(this, 'dog')
    this.run = function() {}
}
Dog.prototype = Animal.prototype;
let dog = new Animal('dog');
dog.eat()
```



##### es6

```js
class Aminal {
    constructor(type) {
		this.type = type
    }
    eat() {
     Aminal.walk();
     console.log('i am eat food')
    }
    static walk() {
     console.log('i am walking')
    }
}
/*
可以省略，如果没有额外的属性的话
class Dog extends Animal {
    constructor(type) {
        super(type)
    }
}
省略为
class Dog extends Animal {}
*/
//有额外属性必须写
class Dog extends Animal {
    constructor(type) {
        //super必须是构造函数的第一行
        super(type)
        this.age = 2;
    }
}
let dog = new Animal('dog');
dog.eat()
```



### 5、函数相关

#### 1、参数默认值

好处是：你可以很清晰的看出来哪些是必填，哪些是选填的

> 参数变量是默认声明的，所以不能用`let`或`const`再次声明。

```js
function f(x, y = 2, z = 3) {
  return x+y+z
}
f(1) //6
```

如果我只想传1,3个呢，第二个不传

```js
f(1, undefined, 3) //直接传underfined，就会用默认值
```

**参数可以是表达式**

```js
function f(x,y=7,z=x+y) {
  return x*10 + z
}
console.log(f(1, undefined, 2)) //12  传了值所以没运行
```

```js
function f(x,y=7,z=x+y) {
  return x*10 + z
}
console.log(f(1) //18  传了值所以没运行
f.length
```



#### 2、函数参数长度,处理不确定参数

##### es5

```js
function sum() {
    let num = 0;
    Array.from(arguments).forEach(function (item) {
        num += item*1//主要把item转换为数字类型
    })
    return num
}
```

注意：函数名.length 获取到没有默认值的参数个数

```js
function f(x,y=7,z=x+y) {
  return x*10 + z
}
console.log(f.length) //1  获取到没有默认值的参数个数
```

##### es6

获取所以函数参数 求和

```js
function sum(...nums) {
	let num = 0;
	nums.forEach(function(item){
	  num+= item*1
	})
    return num
}

sum(1,2,3,4) //10
```

第一个参数乘以2，后面的参数加起来

```js
function sum(base, ...nums) {
	let num = 0;
	nums.forEach(function(item){
	  num+= item*1
	})
    return base*2 + num
}

sum(10,2,3,4) //29
```

#### 3、箭头函数

练习：

如何用箭头函数来实现一个数组排序的问题

箭头函数对this的处理还有什么妙用



### 4、object相关知识点

#### 1、简写

```js
//es5
let name = "wangling"
let obj = {
    x: x，
    fun: function(){}
}
obj[name] = "person"


//es6
let name = "wangling"
let obj = {
    x, //相同的可以省略
    [name]: 'person', //动态的变量可以直接写在里面
    fun() {}, //省略function
    * hello() {} //可以添加异步的函数
}
```



### 5、Set

#### 1、创建

```js
let s = new Set();
let s2 = new Set({1,2,3,4})
```



#### 2、添加

会把重复的去掉

```js
let s = new Set();
s.add("hello");
s.add("hello").add('world') //Set(2) {"hello", "world"}
```

对象可以添加一样的

```js

let n = new Set();
n.add({name:'wangling'}).add({age: 13}).add({name: 'wangling'}) 
n.length //3
```

#### 3、删除

delete

```js
let s = new Set();
s.add("hello").add('world') 
s.delete('hello') //true
s // Set(1) {"world"}
```

clear

```js
let s = new Set();
s.add("hello").add('world') 
s.clear() //true
s //Set(0) {}
```

#### 4、遍历

- `Set.prototype.keys()`：返回键名的遍历器
- `Set.prototype.values()`：返回键值的遍历器
- `Set.prototype.entries()`：返回键值对的遍历器
- `Set.prototype.forEach()`：使用回调函数遍历每个成员
- for-of
- ...  转换为数组
- Array.from //转换为数组

```js
let s = new Set();
s.add("hello").add('world') 
s.keys() //SetIterator {"hello", "world"}
s.values() //SetIterator {"hello", "world"}
s.entries() //SetIterator {"hello" => "hello", "world" => "world"}

s.forEach(item=> {console.log(item)}) //hello,world 更遍历数组一样

for(let item of s) {
    console.log(item)
}

let arr = [...s] // ["hello", "world"]
let arr = Array.from(s) // ["hello", "world"]
```



#### 5、查找

has

```js
let s = new Set();
s.add("hello").add('world') 
s.has("hello")
```

#### 6、长度

size

```
let s = new Set();
s.add("hello").add('world') 
s.size
```



#### 7、应用

去重

```js
let arr = [3, 5, 2, 2, 5, 5];
let unique = [...new Set(arr)]; // [3, 5, 2]
```



### 6、map

#### 1、创建

new Map([[a,b]]) 这个a不能重复，后面的值会覆盖前面的值

```js
let map = new Map([[1,2], [3,4]])
console.log(map)  //Map(2) {1 => 2, 3 => 4}
```

#### 2、添加set

除了添加还有修改

```js
let map = new Map([[1,2], [3,4]])
map.set(1,2)
map.set(3,2)
console.log(map) //Map(2) {1 => 2, 3 => 2}

let map = new Map([[1,2], [3,4]])
map.set(1,1)
map.set(1,1)
console.log(map) //Map(2)  {1 => 1, 3 => 4}
```

#### 3、删除delete

删除索引

```
let map = new Map()
map.set(1,2)
map.set(3,2)
map.delete(1)
console.log(map) //Map(1) {3 => 2}
```

全部删除

```
let map = new Map()
map.set(1,2)
map.set(3,2)
map.clear()
console.log(map) //Map(0) {}
```

#### 4、长度size

size

```js
let map = new Map()
map.set(1,2)
map.set(3,2)
console.log(map.size) //2
```

#### 5、查询

##### has

查的索引值

```js
let map = new Map()
map.set(1,2)
map.set(3,2)
console.log(map.has(3)) //true
```

##### get

```js
let map = new Map()
map.set(1,2)
map.set(3,2)
console.log(map.get(3)) //2
```

##### keys,values,entries

```js
let map = new Map()
map.set(1,2)
map.set(3,2)
console.log(map.keys(3)) //MapIterator {1, 3}
console.log(map.values(3)) //MapIterator {2, 2}
console.log(map.entries(3)) //MapIterator {1 => 2, 3 => 2} 可遍历对象
```

##### forEach

注意value和key的顺序

```
let map = new Map()
map.set(1,2)
map.set(3,2)

map.forEach((value,key)=> {
console.log(value,key) //2 1, 2 3
})
```

##### for of

注意value和key的顺序

```js
let map = new Map()
map.set(1,2)
map.set(3,2)
for(let [key, value] of map) {
console.log(value,key) //2 1, 2 3
}
```

