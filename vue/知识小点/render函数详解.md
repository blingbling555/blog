# 1、render函数

### 1.1、层级说明

```
<father>
	<renderDemo></renderDemo>
</father>
```

### 1.2、说明

我也是第一次使用这个render函数，处在摸索的阶段，为学习这部分做个纪录，学习过程中遇到的坑，走了哪些弯路也会写下来

我看了官网以及网上的博客，千篇一律的都是这种形式,写在组件内容或者是写在组件里面，没有单独把render函数抽成一个文件，自己写的时候就想把它抽象成单独的文件，给大家说下我遇到的第一个坑

```js
Vue.component('anchored-heading', {
  render: function (createElement) {
    return createElement(
      'h' + this.level,   // 标签名称
      this.$slots.default // 子节点数组
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

我看着官方文档的这个例子, 用render来实现遇到的问题 ，简直是怀疑智商，[vue 渲染函数](https://cn.vuejs.org/v2/guide/render-function.html)

```html
<script type="text/x-template" id="anchored-heading-template">
  <h1 v-if="level === 1">
    <slot></slot>
  </h1>
  <h2 v-else-if="level === 2">
    <slot></slot>
  </h2>
  <h3 v-else-if="level === 3">
    <slot></slot>
  </h3>
  <h4 v-else-if="level === 4">
    <slot></slot>
  </h4>
  <h5 v-else-if="level === 5">
    <slot></slot>
  </h5>
  <h6 v-else-if="level === 6">
    <slot></slot>
  </h6>
</script>
```



### 1.3、学习render函数遇到的第一个坑

页面上什么都没显示出来，在render里打印，居然没打印出来，这是为什么呢？因为template的优先级要高于render函数。那怎么解决呢？

**目前我知道有两种方法**

1、不要写vue文件，写js文件就好，因为我们不需要写模板了

2、使用vue模板，把template去掉也可以

```vue
<script>
  export default {}
</script>

```

> 解决方案1 - 代码展示： 不要写vue文件，写js文件就好，因为我们不需要写模板了

```vue
// father.vue
<template>
  <div>
    <renderDemo :level="1">
      <p>这是slot里面的</p>
    </renderDemo>
  </div>
</template>

<script>
import renderDemo from './renderDemo'
export default {
  components: {
    renderDemo
  }
};
</script>
<style  scoped>
</style>
```

```vue
// renderDemo
<!--  -->
<template>
  <div class></div>
</template>

<script>
export default {
  render: function(createElement) {
   console.log(this)
   const _this = this.$parent
    return createElement(
      "h" + this.level,
      [
        "text", 
        _this.$slots.default,
        createElement("div", _header)
      ]
    );
  },
  // 接收父组件传来的 level
  props: {
    level: {
      type: Number,
      required: true
    }
  }
}
</script>
<style  scoped>
</style>
```

> 解决方案2 - 代码展示： 使用vue模板，把template去掉也可以

```vue
// father.vue
<template>
  <div>
    <renderDemo :level="1">
      <p class="c-red">这是传递进去的</p>
      <p slot="footer">这是footer</p>
    </renderDemo>
  </div>
</template>

<script>
import renderDemo from './renderDemo.js'
export default {
  components: {
    renderDemo
  }
};
</script>
<style  scoped>
.c-red {
  color: red;
}
</style>
```

```js
// renderDemo
export default {
  render: function(createElement) {
   console.log(this, this.level)
   const _this = this.$options.parent
    return createElement(
      "h" + this.level,
      [
        "text", 
        createElement('h1', '一则头条'),
        this.$slots.default,
        this.$slots.footer
      ]
    );
  },
  // 接收父组件传来的 level
  props: {
    level: {
      type: Number,
      required: true
    }
  }
}

```

