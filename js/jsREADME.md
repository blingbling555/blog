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



# [Symbol](symbol基础知识.md)

- 概述
- Symbol基本使用
- Symbol的常用技巧
- 属性名的遍历
  - `Object.getOwnPropertySymbols()`：返回该对象所有属性名为Symbols的数组
  - `Reflect.ownKeys()`：返回对象的所有属性，包括Symbols
- `Symbol.for()`：使用Symbol.for会在系统中将Symbol登记
- `Symbol.keyFor()`：根据使用`Symbol.for`登记的Symbol返回描述，如果找不到返回undefined 

# Set



# 参考链接

[后盾人js学习文档](http://houdunren.gitee.io/note/js/1%20%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.html)

[阮一峰ES6教程](https://es6.ruanyifeng.com/)