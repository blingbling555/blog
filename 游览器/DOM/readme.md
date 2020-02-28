# DOM常用操作

[TOC]



### 1、获取dom元素

> NodeList:数组，可以用forEach
>
> 类数组：不可以用forEach.
>
> 问题：
>
> 1. 哪些可以操作动态创建的Dom，哪些不可以
> 2. 什么是类数组，
> 3. document.body和document.documentElement有何区别

- **getElementById()** :通过id来获取元素，只能获取第一个id
- **getElementsByTagName(类数组)**:通过标签名来选中元素。得到**类数组**，没有对应的元素，也会得到一个空的类数组
- **getElementsByClassName(类数组)**：通过类名来选中元素。得到**类数组**，没有对应的元素，也会得到一个空的类数组
- **querySelector**：满足条件的第一个元素，括号内的写法与CSS选择器的写法一样
- **querySelectorAll**：满足条件的所有元素，括号内的写法与CSS选择器的写法一样，值(NodeList)
- **getElementsByName**:通过name属性来获取表单元素。一般也只用表单元素。值(NodeList)
- **document.title**:获取页面标题
- **document.body**:获取body元素
- **document.documentElement**:获取html元素



### 2、操作元素（内容,样式，属性）

可以通过api来操作元素的内容，样式，以及属性

- 操作元素的内容

  -  `element.innerText`:不识别html标签，非标准（火狐早期版本不支持)，在2016年被所有主要游览器采用,去除空格和换行
  - `elementinnerHTML`：识别html标签，w3c标准，保留空格和换行
  - `element.outerHTML`：包括本身
  - `element.textContent`：对比innerText，后面再来完善

  ​

- [操作元素的样式](操作元素的样式.md)

  - `element.style`
  - `element.className` 设置和获取字符串，获取的类名是字符串形式
  - `element.classList`用法
  - 封装了工具类方法（element-ui源码里面的方法）：
     是否包含指定类名
     	添加一个或者多个类名
     	删除一个或者多个类名

  ​

- [操作元素的属性](操作元素的属性.md)

  - 1.系统内置：
    - `element.属性名`：系统内置属性，比如 e.title,e.className,e.style,e.value，获取自定义属性时为                       undefined
  - 2.自定义属性(h5一般自定义属性都是data-开头的)
    - `element.getAttribute('属性名')`：获取自定义的属性以及系统内置属性的值
    - `element.getAttributeNode('属性名')`:方法从当前元素中通过名称获取属性节点。比如id="div1",如果只想获取属性值请使用 [getAttribute](https://www.runoob.com/jsref/met-element-getattribute.html) 方法。
    - `element.setAttribute('属性名','属性值')`：设置属性值
    - `element.deleteAttribute('属性名')`：删除属性
    - `var result = element.hasAttribute(attName)`:是否有该属性,result 为返回的布尔值：true 或 false。
    - `attributes`:类数组 attributes 是字符串形式的名/值对，每一对名/值对对应一个属性节点。
  - 3.h5里面新增获取自定义属性
    - `element.dataset.属性名`

  ```html
     <body>
       <div id="div1" data-index="1"></div>
     </body>
     <script>
       let a = document.getElementById('div1');
       console.log(a.getAttribute('data-index'),a.dataset.index) //1,1
     </script>
  ```


### 3、节点

#### 3.1、节点属性（nodeName,nodeValue,nodeType）

- nodeName（节点名称） 
  - 元素节点的 `nodeName` 是标签名称（大写）
  - 属性节点的 `nodeName` 是属性名称
  - 文本节点的 `nodeName` 永远是 `#text`
  - 文档节点的 `nodeName` 永远是 `#document`
- nodeValue（节点值）
  - 对于文本节点，`nodeValue` 属性包含文本。
  - 对于属性节点，`nodeValue` 属性包含属性值。
  - 文档节点和元素节点，`nodeValue` 属性的值始终为 `null`或者`undefined`。
- nodeType（节点类型）
  - 如果节点是一个元素节点，nodeType 属性返回 1。
  - 如果节点是属性节点, nodeType 属性返回 2。
  - 如果节点是一个文本节点，nodeType 属性返回 3。
  - 如果节点是一个注释节点，nodeType 属性返回 8。


```js
<div id="box">hello nodeType~</div>

 <script type="text/javascript">
	var element = document.getElementById("box");
	var attr = document.getElementById("box").getAttributeNode("id");
	var text = document.getElementById("box").firstChild;
    
    //节点类型：元素1，属性2，文本3
	console.log(element.nodeType);     //1
	console.log(attr.nodeType);        //2
	console.log(text.nodeType);        //3
	
	//节点名称：nodeName
	console.log(document.nodeName)    //#document
	console.log(element.nodeName);    //DIV
	console.log(attr.nodeName);       //id
	console.log(text.nodeName);       //#text

   //节点值：nodeValue
   	console.log('文档节点'+document.nodeValue)    //null
	console.log('元素节点'+element.nodeValue);    //null
	console.log('属性节点'+attr.nodeValue);       //box
	console.log('文本节点'+text.nodeValue);       //hello nodeType~

  </script>
```



#### 3.2、[节点的增删改查其他方法](节点增删改查.md)

- 创建节点

  - `document.createElement()`:创建元素节点
    - 跟innerHTML的区别：innerHTML创建多个元素效率更高，（注意不要使用拼接字符串，采用数组形式拼接），其实用哪种都可以，差距不是特别大
  - `document.createTextNode()`:创建文本节点
  - `document.cloneNode()`:克隆节点。如果参数为空或者为false,即只克隆复制节点本身，不克隆里面的子节点

- 插入节点

  - `appendChild()`:把节点插入到父节点的末尾
  - `insertBefore()`:把节点插入到父节点的某个兄弟节点的前面。所以他有两个参数，insertBefore(newItem,existingItem)

- 删除节点

  - `removeChild()`:删除节点

- 替换DOM节点

  - `replaceChild(newnode,oldnode)`：替换节点

- 查看节点

  > firstChild, lastChild, nextSibling, previousSibling, childNodes这5个dom属性，会把空格、回车(换行符)当做文本节点来处理
  >
  > 解决办法：都有对应的方法代替

  - `parentNode`:父节点属性，得到最近的父节点，如果找不到就返回null
  - 子节点
    - `childNodes`:子节点集合，包含元素节点和文本节点和注释节点**（不常用）**
    - `children`：子节点集合，只包括元素节点（常用）ie9以上，ie6-ie8包括注释节点
    - `firstChild`:返回第一个子节点（包括文本节点）**（不常用）**
    - `lastChild`:返回最后一个子节点（包括文本节点）**（不常用）**
    - `firstElementChild`:返回第一个子元素节点 ie9以上
    - `lastElementChild`:返回最后一个子元素节点 ie9以上
  - 兄弟节点
    - `nextSibling`:下一个兄弟节点（包括文本节点,换行） **（不常用）**
    - `previonsSibling`:上一个兄弟节点(包括文本节点)**（不常用）**
    - `nextElementSibling`:下一个元素兄弟节点，没有返回为null
    - `prevElementSibling`:上一个元素兄弟节点,没有返回为null



### [4、事件](事件.md)

- 注册事件
- 删除事件
- DOM事件流
- 事件对象
- 阻止冒泡
  - event.stopPropagation(); // 一般浏览器停止冒泡,w3c推荐
  - event.cancelBubble; // 属性，IE 6 7 8 的停止冒泡
- 阻止默认
  - e.preventDefaule(); 是一个方法，适合普通浏览器
  - e.returnValue;是一个属性，适用于 IE 6 7 8
  - return false; 没有兼容性问题，只适用于传统的onclick,不适用于addEventListener
- 事件委托
- 常用的事件
  - `contextmenu`:右键
  - `selectstart`：鼠标选中
  - `onkeyup`：某个键盘按键被松开时触发（松开的时候触发）
  - `onkeydown`：某个键盘按键被按下时触发（按下之后一直触发）
  - `onkeypress`：某个键盘按键被按下时触发（按下之后一直触发），但是它不能识别功能键，比如 ctrl、shift 等
- 鼠标事件对象
  - `clientX/clientY`: 鼠标相对于浏览器（这里说的是浏览器的有效区域）左上角x/y轴的坐标；  不随滚动条滚动而改变；
  - `pagex/pageY`:鼠标相对于浏览器（这里说的是浏览器的有效区域）左上角x/y轴的坐标；  随滚动条滚动而改变；
  - `screenX/screenY`:鼠标相对于显示器屏幕左上角x/y轴的坐标；  
  - `offsetX/offsetY`: 鼠标相对于事件源左上角X/y轴的坐标
- 盒子模型
  - `offsetParent`:定位父级。与当前元素最近的经过定位(position不等于static)的父级元素
    - fixed定位：为null，有兼容性，在火狐上为body节点
    - absolute/relative定位：为最近的祖先元素，如果最近的祖先元素没有，就为body
    - 自身无定位：
      - 祖先元素有定位，则offsetParent的结果为离自身元素最近的经过定位的父级元素
      - 祖先元素无定位，则offsetParent的结果为<body>
  - `offsetWidth`：本身宽度+边框线+左右内边距，不包括外边距，width+pading+border
  - `offsetHeight`：本身高度[104]+边框线[3+3]+上下内边距[4+4]=118；不包括外边距
  - `offsetTop`：相对有定位属性的父节点（距离offsetParent元素的位置）上偏移量,；
    - offsetTop和top的区别
      - offsetTop没有单位
      - style.top:如果没有在行内样式或者js中指定过top，为空字符串
  - `offsetLeft`：相对有定位属性的父节点（距离offsetParent元素的位置）左偏移量；
  - `clientWidth`：本身的宽度[103]+左右内边距[5+5]=96;width+pading
  - `clientHeight`：本身的高度[104]+上下内边距[4+4]=95；height+pading
  - `clientTop`：上边框线的宽度；----border-top
  - `clientLeft`：左边框线的宽度；----border-left
  - `scrollWidth`：盒子的实际宽度(包括不可见部分，不包括边线) 
    - 个人理解：当自身盒子比子元素大，盒子的宽度+padding
    - 当子元素大于了自身盒子，要加上子元素超出的部分
  - `scrollHeight`：盒子的实际高度(包括不可见部分，不包括边线)
  - `scrollTop`：滚动条向下滚动的距离；
  - `scrollLeft`：滚动条向右滚动的距离；
  - `window.innerHeight`：浏览器窗口可见区域高度，(不包含工具条与滚动条)
  - `window.innerWidth`：浏览器窗口可见区域宽度，(不包含工具条与滚动条)

### 5、其他api

- `getBoundingClientRect`:用于获取某个元素相对于视窗的位置集合,相当于屏幕左上角的距离，right,bottom也是相对于左上角的

