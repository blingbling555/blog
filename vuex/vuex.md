# 1、state

### 1.1、store基本使用

store我的理解相当于data，只是这个store是全局的，vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。 

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    count:1
  }
})
```

怎么使用

```
在html里
$store.state.count

在js里
this.$store.state.count
```

如果想改变count的值，怎么改变？

常规的想法可能是（错误的）

```js
 this.$store.state.count="haha"
```

这样也能改变，具体改变有什么问题，我现在也不清楚，官方是这样说的

>  再次强调，我们通过提交 mutation 的方式，而非直接改变 `store.state.count`，是因为我们想要更明确地追踪到状态的变化。这个简单的约定能够让你的意图更加明显，这样你在阅读代码的时候能更容易地解读应用内部的状态改变。此外，这样也让我们有机会去实现一些能记录每次状态改变，保存状态快照的调试工具。有了它，我们甚至可以实现如时间穿梭般的调试体验。 

### 1.2、常用

用计算属性监听变化

```js

  computed: {
    count () {
      return this.$store.state.count
    }
  }

```

### 1.3、辅助函数mapState

> 这里参照官方

 当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 `mapState` 辅助函数帮助我们生成计算属性，让你少按几次键： 

```js
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}

//意思是跟下面种是一样的
 computed: {
    count () {
      return this.$store.state.count
    }
  }
```

一般很少上面那种写法，一般组件也有其他computed，我们可以应用对象展开运算符

```js
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```



# 2、Mutation

### 2.1、mutation的用法

 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation , Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数： 

> 第一个参数是state
>
> 第二个参数你可以自己传值，注意这个第二个参数可以是一个对象
>
> 不能传第三个参数，传过去也接收不到，如果要传递多个参数，以对象的形式传递过去就可以

```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state,n) {
      // 变更状态
      state.count++
    }
  }
})
```

```js
//第一种传递一个正常的值
this.$store.commit('increment',10)
//第二种，有多个参数要传递，以对象的方式传递
this.$store.commit('increment',{
   name:'zhao',
   age:18
})
//第三种以对象方式
this.$store.commit({
  type: 'increment',
  amount: 10
})
/*
打印出来 
{
  type: 'increment',
  amount: 10
}
*/
//第四种传递多个参数
 this.$store.commit({
        type:'increment',
        name:10,
        age:20
 })

/*
打印出来 
{
  type:'increment',
  name:10,
  age:20
}
*/
```

### 2.2、使用常量来替代Mutation事件

> 感觉像是方便看有哪些mutations事件吧，方便管理

```js
// mutation-types.js

export default{
  SOME_MUTATION = 'SOME_MUTATION',
  UPDATE_HOLIDAY=' UPDATE_HOLIDAY'
} 
```

```js
// store.js
import Vuex from 'vuex'
import CONSTVARLIABLE from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [CONSTVARLIABLE.SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
```

### 2.3、Mutation 必须是同步函数

官方说： 现在想象，我们正在 debug 一个 app 并且观察 devtool 中的 mutation 日志。每一条 mutation 被记录，devtools 都需要捕捉到前一状态和后一状态的快照。然而，在上面的例子中 mutation 中的异步函数中的回调让这不可能完成：因为当 mutation 触发的时候，回调函数还没有被调用，devtools 不知道什么时候回调函数实际上被调用——实质上任何在回调函数中进行的状态的改变都是不可追踪的。

- 传统写法

```js
methods:{
  add() {
    this.$state.commit('add')
  },
  reduce() {
   this.$state.commit('reduce')
  }
}
```

- 用mapMutations

  可以是数组，可以对象形式

```js
 methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }

 ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    }),
     //是这个意思
    add(){
     this.$store.commit('increment')
    }
```





# 3、模块化

 当一个项目太大了，应用就会变得相当的臃肿和庞大，为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter 

我对应的模块在modules文件夹里面

```
我现在的目录结构
store	
	index.js
	modules
		user
		auth
		...
```

使用

```js
//store/index.js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

可以看出我们以后没添加一个文件，都要在store/index.js里面添加，相当麻烦，下面介绍优化的写法。

### 优化写法

他会自动读取modules下面的js文件

```js
//store/index.js

import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

// 自动找到modules文件下面的所有js文件
// https://webpack.js.org/guides/dependency-management/#requirecontext
const modulesFiles = require.context('./modules', true, /\.js$/)
// you do not need `import app from './modules/app'`
// it will auto require all vuex module from modules file
const modules = modulesFiles.keys().reduce((modules, modulePath) => {
  // set './app.js' => 'app'
  const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
  const value = modulesFiles(modulePath)
  modules[moduleName] = value.default

  return modules
}, {})

export default new Vuex.Store({
  modules
})

```

```js
//stroe/modules/user.js
const state = {
    username:""
}

export default {
    namespaced: true,
    state
}
```



## 参考链接

[**vuex的详细解析（一）**]( http://www.imooc.com/article/284060 )

[vuex官方链接](https://vuex.vuejs.org/zh/guide/)