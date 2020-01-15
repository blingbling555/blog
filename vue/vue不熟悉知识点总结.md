## 1、浅析deep深度选择器

> 深度选择器不是只有在less和scss中使用。

用到element-ui，里面的表单input,想给input加样式 ,我是这样写的

```html
<el-form-item>
    <el-input v-model="loginForm.password" placeholder="password" class="login-input">       </el-input>
</el-form-item>
```

样式class="login-input"并没有加在input上，而是加在了el-form-item上面，怎么解决上面的问题呢？想到了深度选择期，/deep/,和>>> ,但是在vue-cli3上会报错，并没有生效

```js
 //报错
 /deep/.login-input  input {
     padding:0 15px 0px 30px;
 }
```

解决要下载

```js
npm install -D vue-loader

//配置 vue.config.js
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('vue')
      .use('vue-loader')
        .loader('vue-loader')
        .tap(options => {
          // 修改它的选项...
          return options
        })
  }
}
```

使用 ::v-deep，这里用/deep/还是不行，有时间来研究下

```
 ::v-deep .login-input  input {
     padding:0 15px 0px 30px;
 }
```

#### 用/deep/的解决方案

 vue-loader自己的sass-loader依赖，版本为  ^6.0.7 

```
npm i -D sass-loader@^6.0.7
```

重启服务就好了，但是>>>还是不能用

[参考vue-cli官网配置](https://cli.vuejs.org/zh/guide/webpack.html#%E9%93%BE%E5%BC%8F%E6%93%8D%E4%BD%9C-%E9%AB%98%E7%BA%A7 )   

[vue-loader深度作用选择官网参考](https://vue-loader.vuejs.org/zh/guide/scoped-css.html#%E5%AD%90%E7%BB%84%E4%BB%B6%E7%9A%84%E6%A0%B9%E5%85%83%E7%B4%A0)