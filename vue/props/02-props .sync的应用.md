## 1. 应用场景

- 一般情况下我们由父组件传递给子组件的对象并不想在子组件修改对象时改变父组件对象的原数据。
- 这种情况通常在watcher时是使用深度克隆对象供子组件使用，避免某些在数据改变时渲染bug问题（props引用问题文章中有提到）
- 但是有一些特殊的得情况需要更新父组件中的数据。这就是下面要说 .sync修饰符

##2. 实现

接着上文来说，当改变child2时，现在father父组件并没有更新，如果我们想让他跟新怎么办呢？

**在father传值时**

```
:testArr.sync="testObj.testArr
```

在子组件child2中更新

```
this.$emit('update:testArr',this.testData)  //testArr时props传过来的值，this.testData是跟新的数据
```



这样就实现了子组件像父组件传值。



## 3.下面来看下boolean类型和字符串类型

boolean类型经常使用子组件改变父组件的值（经测试**可以更改**）

```
//父组件 
<child2 :testArr.sync="testObj.testArr" :isFlag.sync="isFlag" :basic1="basic1"></child2>

//子组件
this.$emit('update:isFlag',false)
```

字符串类型，测试不能更改，不知道是不是操作有误，(经测试 **不可以更改**)

```
//父组件 
<child2 :testArr.sync="testObj.testArr" :isFlag.sync="isFlag" :basic1="basic1"></child2>

//子组件
this.$emit('update:basic1',"新的")
```

