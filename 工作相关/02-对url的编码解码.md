# url的编码解码详解

### 1、为什么要进行url编码

我们都知道Http协议中参数的传输是"key=value"这种简直对形式的，如果要传多个参数就需要用“&”符号对键值对进行分割。如"?name1=value1&name2=value2"，这样在服务端在收到这种字符串的时候，会用“&”分割出每一个参数，然后再用“=”来分割出参数值。

如果我们传的字符里面包括&或者=这些，就需要进行编码



### 2、用什么来进行编码和解码

- encodeURI: 编码
- encodeURIComponent： 编码，一般用来对url编码
- decodeURI: 对encodeURI编码的元素进行解码
- decodeURIComponent: 对encodeURIComponent编码的元素进行解码

 

### 3、encodeURI和encodeURIComponent

　　对URL编码是常见的事，所以这两个方法应该是实际中要特别注意的。
　　它们都是编码URL，唯一区别就是编码的字符范围，其中

​			- encodeURI方法不会对下列字符编码 ASCII字母、数字、~!@#$&

​			- encodeURIComponent方法不会对下列字符编码 ASCII字母、数字、~!*()'

​		也就是encodeURIComponent编码的范围更广，会将http://XXX中的//也编码，会导致URL不可用。

> 一般对url参数编码用encodeURIComponent，就是了

```js
const param = "中文";
let encodeURIParams = encodeURI(param);
let encodeURIComponentParams = encodeURIComponent(param);
console.log(encodeURIParams, encodeURIComponentParams) // %E4%B8%AD%E6%96%87 %E4%B8%AD%E6%96%87

console.log(decodeURI(encodeURIParams)) // 中文
console.log(decodeURIComponent(encodeURIComponentParams)) // 中文
const url = "http://localhost:85/Exam/settingAction_saveSettings.action?safeHatNumLength=测试";
console.log(encodeURI(url)); // http://localhost:85/Exam/settingAction_saveSettings.action?safeHatNumLength=%E6%B5%8B%E8%AF%95
console.log(encodeURIComponent(url)); // http%3A%2F%2Flocalhost%3A85%2FExam%2FsettingAction_saveSettings.action%3FsafeHatNumLength%3D%E6%B5%8B%E8%AF%95
```

```
总结:　　
　　如果只对参数编码用encodeURIComponent编码就可以，当然用 encodeURI也可以，如果不生效可以用两次编码。
　　如果是对整个URL进行编码最好用encodeURI，而且是两次编码 ，否则有可能不必生效。
　　判断是否生效只需要看带到后台的参数是否是编码后的格式，或者url的地址是否是编码之后的。
```





# 参考链接

https://www.cnblogs.com/qlqwjy/p/9934706.html

[[escape、encodeURI和encodeURIComponent的区别](https://www.cnblogs.com/qlqwjy/p/9934706.html)](https://www.cnblogs.com/qlqwjy/p/9934706.html)

