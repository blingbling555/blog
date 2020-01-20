# 1、vue源码解析

### 1.1、数据驱动

##### (1)、替换前和替换后打印对比

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.11"></script>

</head>
<body>
     <div id="root">
         <p>{{name}}</p>
         <p>{{message}}</p>
     </div>

     <script>
        //  移到console.log,没有高亮
         console.log(root)
        new Vue({
            el:'#root',
            data:{
                name:"张三",
                message:"哈哈"
            }
        })
        // 有高亮，说明结合后是完全替换id为root的元素
        console.log(root)

     </script>
</body>
</html>
```



##### （2）、粗糙实现(简化到极点)

> - tmpNode:相当于虚拟Dom,需要一直放在内存中，如果data里面发生改变需要对应发生改变
>
> - generateNode：相当于Dom节点，需要替换虚拟Dom的元素
>   	 -  let generateNode = tmpNode.cloneNode(true);相当于还没替换虚拟Dom
> -  compiler(generateNode,data)，把虚拟dom和数据相整合，现在还没添加到页面中
> -  root.parentNode.replaceChild(generateNode,root);把有数据的虚拟dom加到页面中
>
> 

思路有很大问题：

1、vue使用的虚拟Dom,这里用的真实的Dom

2、只考虑到单属性{{name}},而vue当中大量使用层级{{child.name.firstName}}

3、代码没有整合

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <!-- 
        步骤拆解
        1. 拿到模板
        2. 拿到数据（目前是data替换，后面还有computed等）
        3. 将数据与模板结合，得到的是HTML元素（DOM元素）
        4. 放到页面中
     -->
     <div id="root">
         <p>{{name}}</p>
         <p>{{message}}</p>
     </div>
     <!-- <div id="test"> </div> -->

     <script>
        //  匹配正则{{}}
        let rkuohao = /\{\{(.+?)\}\}/g;
        //  1、拿到模板，相当于虚拟dom
        let tmpNode = document.querySelector('#root');
        // 2、数据，现在先随意写一个
        let data={
            name:"张三",
            message:"哈哈哈"
        }
        /**
         * 3、数据与模板先结合
         *    考虑正则
         *    template:
         *      现在是dom元素，(暂时是Dom元素)
         *      在vue源码里面是字符串（DOM->字符串模板->VNODE->真正的DOM）
         * */
        function compiler(template,data){
           let childNodes = template.childNodes; //取出子元素,如果是<div></div>，则为空数组
           for(let i=0;i<childNodes.length;i++){
               let type=childNodes[i].nodeType; //取到节点类型，1、元素 3、文本节点
               if(type == 3){
                // 文本节点，可以判断里面是否有{{}}插值
                let txt = childNodes[i].nodeValue; //获取文本节点
                /**
                 * v:{{msg}}   group:为分组msg,双括号里面的东西
                 * replace：
                 *      第一个参数是匹配到的内容{{msg}}
                 *      第n个参数是匹配到的第n组，也就是双括号里面的东西
                 * 
                 * */
                 txt = txt.replace(rkuohao,((v,group)=>{ 
                     let key = group.trim(); //双花括号里面的东西
                     let value = data[key];
                    //  将{{msg}}有值去替换
                    return value
                 }))
                //  替换{{}}为对应的数据
                 childNodes[i].nodeValue = txt;
               }else if(type == 1){
                   //元素节点
                   compiler(childNodes[i],data)
               }
           }
        }
        // 注意：这里不应该直接用tmpNode，下次更改vue里面的值时，原来的坑没有了
        let generateNode = tmpNode.cloneNode(true); //Dom元素可以这么用
        console.log(tmpNode)
        compiler(generateNode,data)
        console.log(generateNode)
      
        // 第4部，放到页面当中
        root.parentNode.replaceChild(generateNode,root);
     </script>
</body>
</html>
```



##### （3）、对步骤2抽取到构造函数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
     <div id="root">
         <p>{{name}}</p>
         <p>{{message}}</p>
     </div>
     <!-- <div id="test"> </div> -->

     <script>
          let rkuohao = /\{\{(.+?)\}\}/g;

            function compiler(template,data){
           let childNodes = template.childNodes; //取出子元素,如果是<div></div>，则为空数组
           for(let i=0;i<childNodes.length;i++){
               let type=childNodes[i].nodeType; //取到节点类型，1、元素 3、文本节点
               if(type == 3){
                // 文本节点，可以判断里面是否有{{}}插值
                let txt = childNodes[i].nodeValue; //获取文本节点
                /**
                 * v:{{msg}}   group:为分组msg,双括号里面的东西
                 * replace：
                 *      第一个参数是匹配到的内容{{msg}}
                 *      第n个参数是匹配到的第n组，也就是双括号里面的东西
                 * 
                 * */
                 txt = txt.replace(rkuohao,((v,group)=>{ 
                     let key = group.trim(); //双花括号里面的东西
                     let value = data[key];
                    //  将{{msg}}有值去替换
                    return value
                 }))
                //  替换{{}}为对应的数据
                 childNodes[i].nodeValue = txt;
               }else if(type == 1){
                   //元素节点
                   compiler(childNodes[i],data)
               }
           }
        }

        function JGVue(options){
        //   习惯：内部的数据使用下划线开头，只读的以$开头，vue是这样的
        this._data=options.data;
        this._el = options.el;
        // 准备工作（准备模板）
        this.$el=this._templateDOM = document.querySelector(this._el);
        this._parent = this._templateDOM.parentNode;
        // 渲染工作
        this.render();
        }
        // 将模板  即可数据，得到html加到页面中
        JGVue.prototype.render = function() {
            this.compiler()
        }
        // 编译：将模板与数据结合，得到真正的DOM元素
        JGVue.prototype.compiler=function(){
           let realHTMLDOM = this._templateDOM.cloneNode(true); //用模板 拷贝 得到一个准DOM
           compiler(realHTMLDOM,this._data);//把{{}}替换成数据
           this.update(realHTMLDOM)
        }
        // 将dom元素 放到页面中
        JGVue.prototype.update=function(real){
            // 真正vue里面不是这样做的，不能直接替换，如果不存在就渲染，如果以前存在过，就替换更新的部分
            //document.querySelector(this._el)可以换为this.$el
            this._parent.replaceChild(real,document.querySelector(this._el))
        }
        let app = new JGVue({
            el:'#root',
            data: {
                name:'jim',
                message:"info"
            }
        })
     </script>
</body>
</html>
```

##### （4）、有点深度的处理，比如{{name.firstName}}

```
data:{
   name: {
   firstName:"张"，
   lastName:"三"
   }
}


通过上面的实际上是data[name.firstName],这样是获取不到的，现在就要写一个函数，能获取到里面的值
getValueByPath
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
     <div id="root">
         <p>{{name}}</p>
         <p>{{a.message}}</p>
     </div>
     <!-- <div id="test"> </div> -->

     <script>
        let rkuohao = /\{\{(.+?)\}\}/g;
        //把{{}}变为数据
        function compiler(template,data){
           let childNodes = template.childNodes; //取出子元素,如果是<div></div>，则为空数组
           for(let i=0;i<childNodes.length;i++){
               let type=childNodes[i].nodeType; //取到节点类型，1、元素 3、文本节点
               if(type == 3){
                // 文本节点，可以判断里面是否有{{}}插值
                let txt = childNodes[i].nodeValue; //获取文本节点
                /**
                 * v:{{msg}}   group:为分组msg,双括号里面的东西
                 * replace：
                 *      第一个参数是匹配到的内容{{msg}}
                 *      第n个参数是匹配到的第n组，也就是双括号里面的东西
                 * 
                 * */
                 txt = txt.replace(rkuohao,((v,group)=>{ 
                     let key = group.trim(); //双花括号里面的东西
                    //  let value = data[key];
                    let value = getValueByPath(data,key)
                    //  将{{msg}}有值去替换
                    return value
                 }))
                //  替换{{}}为对应的数据
                 childNodes[i].nodeValue = txt;
               }else if(type == 1){
                   //元素节点
                   compiler(childNodes[i],data)
               }
           }
        }
        //    
        function getValueByPath(obj,path) {
           let paths = path.split('.'); //得到一个数组[xxx,yyy,zzz]
           let res = obj;
           let prop;
           while(prop = paths.shift()){
               res = res[prop];
           }
           return res
        }
        function JGVue(options){
        //   习惯：内部的数据使用下划线开头，只读的以$开头，vue是这样的
        this._data=options.data;
        this._el = options.el;
        // 准备工作（准备模板）
        this.$el=this._templateDOM = document.querySelector(this._el);
        this._parent = this._templateDOM.parentNode;
        // 渲染工作
        this.render();
        }
        // 将模板  即可数据，得到html加到页面中
        JGVue.prototype.render = function() {
            this.compiler()
        }
        // 编译：将模板与数据结合，得到真正的DOM元素
        JGVue.prototype.compiler=function(){
           let realHTMLDOM = this._templateDOM.cloneNode(true); //用模板 拷贝 得到一个准DOM
           compiler(realHTMLDOM,this._data);//把{{}}替换成数据
           this.update(realHTMLDOM)
        }
        // 将dom元素 放到页面中
        JGVue.prototype.update=function(real){
            // 真正vue里面不是这样做的，不能直接替换，如果不存在就渲染，如果以前存在过，就替换更新的部分
            this._parent.replaceChild(real,document.querySelector(this._el))
        }

        let app = new JGVue({
            el:'#root',
            data: {
                name:'jim',
                a: {
                    message:"info"
                 }
            }
        })
     </script>
</body>
</html>
```

函数柯里化，现在由于代码的设计模式问题，暂时柯里化放不进去

```js
 //  改良，用了函数柯里化，主要是优化吧，这块东西我也不是很懂，这个函数是在vue编译模板时就生成了
        function createGetValueByPath(path){
            let paths = path.split('.'); //得到一个数组[xxx,yyy,zzz]
           return function getValueByPath(obj) {
           let res = obj;
           let prop;
           while(prop = paths.shift()){
               res = res[prop];
           }
           return res
        }
        }
```



### 1.2、虚拟DOM

##### 1、为什么要使用虚拟DOM

性能

##### 2、怎么将真正的DOM转换为虚拟DOM

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="root" title="哈哈哈" data-id="你说">
        <div class="div1">div1</div>
        <div>div2</div>
        <div>div3</div>
        <ul>
            <li>1</li>
            <li class="li1">2</li>
            <li>3</li>
        </ul>       
    </div>
    <script>
     /**
      * 属性：
      *     tag:标签类型
      *     data:标签属性
      *     value:文本值
      *     type:节点类型
      *     
     */
    class VNode{
      constructor(tag,data,value,type) {
         this.tag = tag&&tag.toLowerCase();
         this.data = data;
         this.value = value;
         this.type = type;
         this.children = [];

      }
      appendChild(VNode){
        this.children.push(VNode);
      }
    }
    // 使用递归来遍历DOM元素来生成虚拟DOM.VUE源码使用栈存储父元素来实现递归生成。
    function getVNode(node){
      let nodeType = node.nodeType;
      let _VNode = null;
      if(nodeType == 1){ //元素节点
       let nodeName = node.nodeName;
       let attrs = node.attributes; //返回所有属性返回的伪数组
       let _attrObj ={};
       for(let i=0;i<attrs.length;i++){
        _attrObj[attrs[i].nodeName] = attrs[i].nodeValue; //attrs[i]是一个属性节点
       }
       _VNode = new VNode(nodeName,_attrObj,undefined,nodeType);
    //    考虑传来来的DOM元素的子元素
        let childNodes = node.childNodes;
       for(let i=0;i<childNodes.length;i++){
        _VNode.appendChild(getVNode(childNodes[i])); //递归
       }
      }else if(nodeType == 3){ //
        _VNode = new VNode(undefined,undefined,node.nodeValue,nodeType)
      }
      return _VNode;
    }

    let root = document.querySelector('#root');
    console.log(getVNode(root))
    
    </script>
</body>
</html>
```



##### 3、怎么将虚拟DOM转换为真正的DOM

在真正的vue里面也是用递归加栈数据类型

```html
   let root = document.querySelector('#root');
    let vnode = getVNode(root);
    function parseVNode(vnode) {
        let node =null
       if(vnode.type == 1){ //为元素节点时
        node = document.createElement(vnode.tag); 
        let data=vnode.data;
        Object.keys(data).forEach(key=>{
            let attrName = key;
            let attrValue = data[key];
            node.setAttribute(attrName,attrValue);
        })
        for(let i=0;i<vnode.children.length;i++){
            node.appendChild(parseVNode(vnode.children[i]))
        }
        console.log(node)
        return node
       }else if(vnode.type == 3){//为文本节点时
        return document.createTextNode(vnode.value);
       }
    }
    console.log(parseVNode(vnode))
```



### 1.3、vue源码思想

##### 1、vue里面怎么区分是html还是组件

通过枚举，把常用的标签存放在一个数组里面。

vue的本质上是使用HTML的字符串作为模板的，将字符串的模板转换为AST，再转换为VNode,在通过VNode转换为DOM

- 模板->AST([AST抽象语法树](https://segmentfault.com/a/1190000016231512))
- AST->VNode
- VNode->DOM

##### 2、为什么要使用柯里化函数

就是为了解决上面一个问题，用常规方法来区分

```js
现在一个函数都要执行很多次这个，更何况有很多元素，这样对性能不是很好
function isHtml(element){
  const HTMLElement = "div,p,a,ul,li".split(',');
    HTMLElement.indexOf(element)!==-1 
}
```

这个就是柯里化，按我的理解，就是让isHTMLTag，返回的函数，所以这个内存一直不会释放掉，所以性能就好

```
内存中一直有，每次进来查询就可以了，时间复杂度大大减少了
let set = {
  div:true,
  html:true
}
```



```js
function makeMap(keys){
  let tags = 'div,p,a,img,ul,li'.split(',');
  let set = {};
  tages.forEach(key=>set[key]==true);
  return function(tagName){
      return !!set[tagName.toLowerCase()]
  }
}

let isHTMLTag = makeMap(tages) //返回函数
```

##### 3、虚拟DOM的render方法。

> 思考：vue项目模板转换为抽象语法树（AST）需要几次？
>
> 

- 页面一开始加载需要渲染
- 每一个属性（响应式）数据在发生变化的时候要渲染
- watch,computed等等需要渲染

前面写的代码每次需要渲染的时候，模板就被解析一次（这里，我简化了解析方法，上面没有经过抽象语法树去解析，vue里面是用抽象语法树去解析）

render作用：是将虚拟DOM转换为真正的DOM,加载页面当中

	- 虚拟DOM可以降级理解为AST
	- 一个项目运行的时候，模板不会变=》AST不会变

**处理：可以将 虚拟DOM缓存起来，生成一个函数，函数只需要传入数据，就可以得到真正地 DOM**



### 1.4、响应式原理

##### 1.4.1、解决问题

- 在使用vue时。赋值属性获得属性都是直接使用vue的实例，比如现在使用需要app._data.msg,我们希望是app.msg
- 在设计属性值时，页面的数据要更新

```js
Object.defineProperty('对象'，‘属性名’，{
                      writable 
                      configable
                      enumerable //作用：用来控制柯枚举，是不是能够被for in循环取出来的
                      set(){} //赋值触发
                      get(){}//取值触发
                      
 })
```





# 参考链接：

视频链接： https://www.bilibili.com/video/av83252401?from=search&seid=617521448022371652 

函数式编程（内功修炼）： https://llh911001.gitbooks.io/mostly-adequate-guide-chinese/content/ 

 虚拟DOM:https://www.cnblogs.com/fundebug/p/vue-virtual-dom.html 

函数柯里化 （数学演变来的-》维基百科）  柯里化->偏函数->高阶函数

柯里化->偏函数->高阶函数



requirementView(1324,1328)



requirementListInit //1179行