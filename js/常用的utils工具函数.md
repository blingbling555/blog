### 1. 去掉字符串前后空格
```
 function trim(string) {
         return (string || '').replace(/^[\s\uFEFF]+|[\s\uFEFF]+$/g, '');
        };
```
### 2、类名，查找某个指定元素是否包含指定的className
```
 hasClass(el, cls) {
      if (!el || !cls) return fasle;
      if (cls.indexOf(" ") !== -1){
        // throw new Error("要查询的className不能包含空格!");
        return fasle
      }
        
      if (el.classList) {
        return el.classList.contains(cls);
      } else {
        return (el.className + "").indexOf("" + cls) > -1;
      }
    }

```

### 3、增加一个或者多个类名

```js
function addClass(el, cls) {
  if (!el) return;
  var curClass = el.className;
  var classes = (cls || '').split(' ');

  for (var i = 0, j = classes.length; i < j; i++) {
    var clsName = classes[i];
    if (!clsName) continue;

    if (el.classList) {
      el.classList.add(clsName);
    } else if (!hasClass(el, clsName)) {
      curClass += ' ' + clsName;
    }
  }
  if (!el.classList) {
    el.className = curClass;
  }
};
```
用法
```
addClass(wrapDom, 'dd ee') //添加多个类名
```

### 4、移除类名
> 1. 可以移除多个 removeClass(wrapDom, 'dd ee') //添加多个类名
> 2. 调用了其他方法
```
function removeClass(el, cls) {
  if (!el || !cls) return;
  var classes = cls.split(' ');
  var curClass = ' ' + el.className + ' ';

  for (var i = 0, j = classes.length; i < j; i++) {
    var clsName = classes[i];
    if (!clsName) continue;

    if (el.classList) {
      el.classList.remove(clsName);
    } else if (hasClass(el, clsName)) {
      curClass = curClass.replace(' ' + clsName + ' ', ' ');
    }
  }
  if (!el.classList) {
    el.className = trim(curClass);
  }
};
```



### 参考博客
[Element分析（工具篇）——Dom](https://www.jianshu.com/p/14225d0519ed)