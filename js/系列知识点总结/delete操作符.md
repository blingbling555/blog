# delete操作符的理解

> 说明：以下都是在不严格模式下，所有代码都是验证过后的

**`delete `**操作符会从某个对象上移除指定属性。成功删除的时候回返回 `true`，否则返回 `false`。

```
delete expression；
```

**返回值：**

对于所有情况都是`true`，除非属性是一个[`自身的`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty) [`不可配置`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Errors/Cant_delete)的属性，在这种情况下，非严格模式返回 `false`。

> 1. 如果你试图删除的属性不存在，那么delete将不会起任何作用，但仍会返回true
> 2. 如果对象的原型链上有一个与待删除属性同名的属性，那么删除属性之后，对象会使用原型链上的那个属性（也就是说，delete操作只会在自身的属性上起作用）

### 基本使用

删除后访问就是undefined

```js
const Employee = {
  firstname: 'John',
  lastname: 'Doe'
};

console.log(Employee.firstname); // "John"

delete Employee.firstname;

console.log(Employee.firstname); // undefined

console.log(Emloyee) // {lastname: "Doe"}

```



### 删除一个不存在的属性

> 删除一个不存在的属性也是返回true

这里删除firstname，但firstname并不存在，在这里也是返回的true

```js
const Employee = {
  lastname: 'Doe'
};

console.log(Employee.firstname); // undefined

delete Employee.firstname; // true

console.log(Employee.firstname); // undefined

```



