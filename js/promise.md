# 1、promise

### 1.1、为什么用promise

主要为了解决回调地狱的问题

异步结构不清晰，promise可以让异步操作结构变得很清晰

### 1.2、promise语法

 executor是带有 `resolve` 和 `reject` 两个参数的函数 。Promise构造函数执行时立即调用`executor` 函数， `resolve` 和 `reject` 两个函数作为参数传递给`executor`（executor 函数在Promise构造函数返回所建promise实例对象前被调用）。`resolve` 和 `reject` 函数被调用时，分别将promise的状态改为*fulfilled（*完成）或rejected（失败）。executor 内部通常会执行一些异步操作，一旦异步操作执行完毕(可能成功/失败)，要么调用resolve函数来将promise状态改成*fulfilled*，要么调用`reject` 函数将promise的状态改为rejected。如果在executor函数中抛出一个错误，那么该promise 状态为rejected。executor函数的返回值被忽略。 

简单理解上面一段话，new Promise()里面接收一个函数，这个函数会立即执行，函数里面有两个参数resolve和reject,函数的执行体里面是异步的操作，异步操作有成功有失败

- 成功：状态pending->fulfilled,并且用`resolve` 去接收成功的值
- 失败：状态pending->rejected,并且用`reject` 去接收失败的值
- 3种状态两种结果

```js
new Promise( function(resolve, reject) {...} /* executor */  );
```

简单例子

> then里面接收两个函数，成功则执行第一个里面的函数，失败执行第二个回调函数

```js
 let p1=new Promise(function(resolve,reject){
           setTimeout(function(){
               let num=new Date().getTime();
               num%2==0?resolve('成功'):resolve('失败')
           },0)
        })
        p1.then(function(value){
            console.log(value)
        },function(reason){
            console.log(reason)
        })
```

连写

```
 let p1=new Promise(function(resolve,reject){
           setTimeout(function(){
               let num=new Date().getTime();
               num%2==0?resolve('成功'):resolve('失败')
           },0)
        }).then(function(value){
            console.log(value)
        },function(reason){
            console.log(reason)
        })
```

如果一个promise即调用了resolve，又调用了reject，谁先调用，最后就走对应的方法，

```js
 new Promise(function (resolve, reject) {
            resolve("成功")
            reject("失败")
            console.log("执行了")
        }).then(value => {
            console.log(value)
        }, reason => {
            console.log(reason)
        })
        // 执行了
        // 成功
```

如果没有成功，刚在then的第二个参数写的失败的回调函数，其实也可以用catch

```
 new Promise(function (resolve, reject) {
           reject("失败")
            resolve("成功")
        })
        .then(value => {
            console.log(value)
        })
        .catch(reason => {
            console.log(reason)
        })
        // 失败
```



### 1.3、Promise.resolve()

成功的语法糖

```
let p1=new Promise(function(resolve,reject){
            resolve(11)
        })
        p1.then(function(value){
            console.log(value)
        })
```

Promise.resolve()  

```js
const p1=Promise.resolve(11); //跟上面是一样的
p1.then(value=>{console.log(value)})
```



### 1.4、Promise.reject()

失败的语法糖

```js
const p3=Promise.reject(33)
p3.then(null,reason=>{console.log(reason)})
```



### 1.5、Promise.All()

Promise.All():发送了多个请求，只有全部成功才走成功的处理，只要其中有一个失败就失败，这个返回的是p2的原因

- 失败原因，走第一个失败的原因,这里p2是第一个失败的，所以reason为22

```js
        const p1 = Promise.resolve(11); //跟上面是一样的
        const p2 = Promise.reject(22)
        const p3 = Promise.reject(33)
    
        const pAll = Promise.all([p1, p2, p3])
        pAll.then(
            value => {},
            reason => {
                console.log(reason) //22
            }
        )
```

- 成功是几个结果组成的数组,结果顺序跟[p1, p2, p3]一样

```js
 const p1 = Promise.resolve(11); //跟上面是一样的
        const p2 = Promise.resolve(22)
        const p3 = Promise.resolve(33)
    
        const pAll = Promise.all([p1, p2, p3])
        pAll.then(
            values => {
                console.log(values)
            }
        )
//[11,22,33]
```



### 1.6、Promise.race()

 **`Promise.race(iterable)`** 方法返回一个 promise，一旦迭代器中的某个promise解决或拒绝，返回的 promise就会解决或拒绝。 

多个异步任务，谁先执行完就用谁的,可以用setTimeout延迟去模拟，这里我就不试了

```js
        const p1 = Promise.resolve(11); //跟上面是一样的
        const p2 = Promise.resolve(22)
        const p3 = Promise.resolve(33)
    
        const pRace = Promise.race([p1, p2, p3])
        pRace.then(
            value => {
                console.log(value) //11 走的成功
            }
        )
```

如果第一个执行完是一个失败的，那就走失败

```js
        const p1 = Promise.reject(11); //跟上面是一样的
        const p2 = Promise.resolve(22)
        const p3 = Promise.resolve(33)
    
        const pRace = Promise.race([ p1,p2, p3])
        pRace.then(
            value => {
                console.log(value) 
            },
            reason=>{
                console.log(reason) //11 走的 失败
            }
        )
```



### 1.7、promise理解

##### 1.7.1、如何改变promise的状态?

```
    resolve(value): 如果当前是pendding就会变为resolved
    reject(reason): 如果当前是pendding就会变为rejected
    抛出异常: 如果当前是pendding就会变为rejected
```

```js
//如果当前是pendding就会变为rejected，内部抛出也是这样
const p=new Promise((resolve,reject)=>{
  throw new Error('出错了')
})

const p = new Promise((resolve, reject) =>{
        //resolve(1)//promies变为resolved成功状态
        //reject(2)//promise变为rejected失败状态
        //throw new Error("出错了")//抛出异常promise变为rejected失败状态，reason 为抛出的error
        throw 3
    });
    p.then(
        reason => {console.log("reason:", reason)}//3
    )
```

##### 1.7.2、一个promise指定多个成功/失败回调函数，都会调用吗？

```
	都会调用
```

```js
 const p1=Promise.resolve('11')
        p1.then(value=>{
            console.log("第一次"+value)
        })
        p1.then(value=>{
            console.log("第二次"+value)
        })
        //第一次11
        //第二次11
```

##### 1.7.3、改变promise状态和指定回调函数谁先谁后

```js
   3.1、都有可能，正常情况下时先指定回调函数再改变状态，但也可以先改变状态再指定回调函数
   3.2、如何先改变状态再指定回调？
 	 3.2.1、在执行器中直接调用resolve()/reject()
     3.2.2、延迟更长时间才调用then()
```

```
new Promise((resolve, reject) =>{
        setTimeout(()=>{
            resolve(1)//后改变的状态（同时指定数据），异步执行回调函数
        }, 1000)    
    }).then(//先指定回调函数，保存当前指定的回调函数
        value => {console.log("value1:", value)}//value1: 1
    )
```

##### 1.7.4、promise.then（）返回的新的promise的结果状态由什么决定？

> 如果没有返回值也没有抛出错误，就走成功undefined

```
　　4.1、简单表达：由then()指定的回调函数执行的结果决定
　　4.2、详细表达：
　　　　4.2.1、如果抛出异常，新promise变为rejected，reason为抛出的异常
　　　　4.2.2、如果返回的是非promise的任意值，新的promise变为resolved，value为返回的值
　　　　4.2.3、如果返回的是另一个新promise，此promise的结果就会成为新promise的结果 
```



```js
new Promise((resolve,reject)=>{
            setTimeout(function(){
                resolve(11)
            },1000)
        })
        .then(value=>{
            console.log("第一次"+value)
        })
        .then(value=>{
            console.log("第二次"+value)
        })
        //第一次11
        //第二次undefined
```



```js
 new Promise((resolve,reject)=>{
            setTimeout(function(){
                reject(11)
            },1000)
        })
        .then(value=>{
            console.log("成功第一次"+value)
        },reason=>{
         console.log("失败第一次"+reason)
         //throw new Error('出错了')
        })
        .then(value=>{
            console.log("成功第二次"+value)
        },reason=>{
         console.log("失败第二次"+reason)
        })
        //失败第一次11
        // 成功第二次undefined
```

以下都是针对第二次then的结果

```js
new Promise((resolve,reject)=>{
            setTimeout(function(){
                resolve(11)
            },1000)
        })
        .then(value=>{
            console.log("成功第一次"+value)
            // return 2  //成功第二次2
            // return Promise.resolve(2)  //成功第二次2
            // return Promise.reject(2)  //失败第二次2
            throw 3;  //失败第二次3



        },reason=>{
         console.log("失败第一次"+reason)
        })
        .then(value=>{
            console.log("成功第二次"+value)
        },reason=>{
         console.log("失败第二次"+reason)
        })
```

##### 1.7.5、 promise如何串连多个操作任务 

```
1、promise的then()返回一个新的promise，可以看成then()的链式调用
2、通过then的链式调用串连多个同步/异步任务
```

```js
new Promise((resolve, reject) =>{
        setTimeout(()=>{
            console.log("执行任务1(异步)")
            resolve(1)
        },1000);
    }).then(
        value => {
            console.log("任务1的结果:", value)
            console.log("执行任务2(同步):")
            return 2
        }
    ).then(
        value => {
            console.log("任务2的结果():", value)
            return new Promise((resolve, reject)=>{
                setTimeout(()=>{
                    console.log("执行任务3(异步)")
                    resolve(3)
                },1000)
            })
        }
    ).then(
        value => {
            console.log("任务3的结果", value)
        }
    )
/*
执行任务1(异步)
任务1的结果: 1
执行任务2(同步):
任务2的结果(): 2
执行任务3(异步)
任务3的结果 3
*/
```

##### 1.7.6、promise异常穿透

```
1、当使用promise的then链式调用时，可以在最后指定失败的回调
2、前面任何操作出了异常，都会传到最后失败的回调中处理
```

第一个走的失败的回调，但是失败的回调没有写，默认他会执行 reason=>{throw reason},到第三个时，由于第二个没有抛出异常，也没有返回值，所以走成功值为undefined

catch在后面也不会成功，因为第三个走的成功，所以不会执行catch

```js
new Promise((resolve, reject) => {
            reject(1)
        }).then(
            value => {
                console.log("onResolveed1():", value)
                return Promise.reject(2)
            }
        ).then(
            value => {
                console.log("onResolveed2():", value)
                return 3
            },
            reason => {
                console.log("第二个失败"+reason)
            }
        ).then(
            value => {
                console.log("onResolveed3()", value)
            }
        ).catch(err=>{
            console.log("catch"+err)
        })
/*
  第二个失败1
  onResolveed3() undefined
*/
```

默认在catch后面的then函数，执行成功和失败回调和上面的规则是一样的

```js
 new Promise((resolve, reject) => {
            reject(1)
        }).catch(err => {
            console.log(err)
        }).then(value => {
            console.log("成功"+value)
        }, reason => {
           console.log("失败"+reason)
        })
/*
1
成功undefined
*/
```

如果我不想执行后面then的函数呢？这就看下一个中断promise链

##### 1.7.7、中断promise链？

``` js
1、当时用promise的then的链式调用时，在中间中断，不再调用后面的回调函数
2、办法：在回调函数中返回一个pedding状态的promise对象
```

```js
 new Promise((resolve, reject) => {
            reject(1)
        }).catch(err => {
            console.log(err)
            return new Promise((resolve,rejuct)=>{}) //返回一个pending的promise
        }).then(value => {
            console.log("成功"+value)
        }, reason => {
           console.log("失败"+reason)
        })
```





### 1.8、Promise.prototype.then() 

接收两个参数：

```js
p.then(onFulfilled[, onRejected]);

p.then(value => {
  // fulfillment
}, reason => {
  // rejection
});
```

>  返回了一个值，那么 `then` 返回的 Promise 将会成为接受状态，并且将返回的值作为接受状态的回调函数的参数值。 

```js
var p1 = Promise.resolve(33); 
p1.then(value => {
    console.log(value, "成功") //33 "成功"
}, reason => {
    console.log(reason, "失败")
})

/*
@return 新的promise
Promise {<resolved>: undefined}
__proto__: Promise
[[PromiseStatus]]: "resolved"
[[PromiseValue]]: undefined
*/
```

>  抛出一个错误，那么 `then` 返回的 Promise 将会成为拒绝状态，并且将抛出的错误作为拒绝状态的回调函数的参数值。 

```js
var p1 = Promise.reject(33); 
p1.then(value => {
    console.log(value, "成功") 
}, reason => {
    console.log(reason, "失败") 
})

//33 "失败"
/*
Promise {<resolved>: undefined}
__proto__: Promise
[[PromiseStatus]]: "resolved"
[[PromiseValue]]: undefined
*/

```

>  返回一个已经是拒绝状态的 Promise，那么 `then` 返回的 Promise 也会成为拒绝状态，并且将那个 Promise 的拒绝状态的回调函数的参数值作为该被返回的Promise的拒绝状态回调函数的参数值。 

```js
const p1 = new Promise(function (resolve, reject) {
  reject(new Error('fail'))
})

const p2 = new Promise(function (resolve, reject) {
  resolve(p1)
})

p2.then(result => console.log(result, "成功"))
  .catch(error => console.log(error, "失敗")) //fail 失败
```

>  返回一个已经是接受状态的 Promise，那么 `then` 返回的 Promise 也会成为接受状态，并且将那个 Promise 的接受状态的回调函数的参数值作为该被返回的Promise的接受状态回调函数的参数值。 

```js
const p1 = new Promise(function (resolve, reject) {
  resolve(33)
})

const p2 = new Promise(function (resolve, reject) {
  resolve(p1)
})

p2.then(result => console.log(result, "成功")) //33,成功
  .catch(error => console.log(error, "失敗")) 
```

>  返回一个未定状态（`pending`）的 Promise，那么 `then` 返回 Promise 的状态也是未定的，并且它的终态与那个 Promise 的终态相同；同时，它变为终态时调用的回调函数参数与那个 Promise 变为终态时的回调函数的参数是相同的。 

```js
const p1 = new Promise(function (resolve, reject) {
})

const p2 = new Promise(function (resolve, reject) {
  resolve(p1)
})

p2.then(result => console.log(result, "成功")) //33,成功
  .catch(error => console.log(error, "失敗")) 
```

> 一般来说，不要在`then()`方法里面定义 Reject 状态的回调函数（即`then`的第二个参数），总是使用`catch`方法。 

```javascript
// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```

### 1.9、Promise.prototype.catch()

 `Promise.prototype.catch()`方法是`.then(null, rejection)`或`.then(undefined, rejection)`的别名，用于指定发生错误时的回调函数。 



### 2.0、Promise.prototype.finally()

 `finally()`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。

比如在项目vuex发送请求，在请求前，加loading效果，在请求完不管成功或者都需要关闭，这时候就可以用finally()

# 2、async和await

### 简单解释下async和await

加了async属性返回一个promise

```js
async function fun1() {return 7}
console.log(fun1()) //Promise {<resolved>: 7}
```

怎么得到返回值呢？

```js
async function fun1() {return 7}
fun1().then((res) => {
  console.log(res) //7
})
```

await一个promise可以直接得到值，不用.then

```js
async function fun1() {return 7}

async function fun2() {
  let res = await fun1();
  console.log(res) //7
}
```

### 2.1、async

```
1、函数的返回值为promise对象
2、promise对象的结果由async函数执行的返回值决定
```

只要加了async，返回一个promise，里面保存了状态，如果async函数成功，下面就走成功回调，如果是失败，就失败的回调函数函数

```js
async function fn1() {
       return 1
}
let result=fn1();
console.log(result)
//Promise


```

```js
async  function fn1() {
      return 1
}
fn1().then(value=>{
  console.log(value)
})

//1
```



### 2.2、await

 该指令会暂停异步函数的执行，并等待Promise执行，然后继续执行异步函数，并返回结果。 

```
1、await右侧的表达式一般为promise对象，但也可以是其他的值 
2、如果表达式是promise对象，await返回的是promise成功的值
3、如果表达式是其他值，直接将此值作为await的返回值
```

如果value的右边是promise，返回的是promise成功时候的值

如果value的右边是promise，返回的是promise失败时候的值，就用try catch来获取

如果value右边的不是promise，返回的是值本身

```js
 function fn2() {
        return  new Promise((resolve,reject)=>{
           setTimeout(()=>{
               resolve(5)
           },10)
         })
    }
     async function fn1() {
          const value=await fn2();
          console.log(value)
    }
    fn1() //5
```

>  注意：
>
> 　await必须写在async函数中，但是async函数中可以没有await
>
> 　如果await的promise失败了，就会抛出异常，需要通过try...catch来捕获处理



### 2.3、常用请求用法

### 　　

# 3、  JS异步之宏队列与微队列 

```
1、JS中用来存储执行回调函数的队列包含2个不同特定的队列
2、宏队列：用来保存带执行的宏任务，比如：定时器回调，DOM事件回调，ajax回调
3、微队列：用来保存待执行的微任务，比如：promise的回调，MutationObserver的回调
4、JS执行时会区别这2两个队列
    1、JS引擎首先必须先执行所有的初始化同步任务代码
    2、每次准备取出第一个宏任务前，都要将所有的微任务一个一个取出来执行
```

> 注意promise放在微任务里面，需要更改状态才会放到微任务里面，比如是从pending =》resolved 
>
> 或者 pedding变为rejected 才会放到微队列

```js
 setTimeout(()=>{
       console.log('settimeout')
   },0)
   Promise.resolve(1).then(value=>{
       console.log("promise"+value)
   })
   
   /*
      promise1
      settimeout
   */
```

js会把程序走一遍，定时器加到宏队列，promise加到微队列，

每次会先把微队列执行完在执行宏队列

```js
 setTimeout(()=>{
       console.log('定时器1')
   },0)

   setTimeout(()=>{
       console.log('定时器2')
   },0)
   Promise.resolve(1).then(value=>{
       console.log("第一个promise"+value)
   })
   Promise.resolve(2).then(value=>{
       console.log("第二个promise"+value)
   })

/*
第一个promise1
第二个promise2
定时器1
定时器2
*/
```

这里看下是先执行 **第三个promise** 还是先执行 **定时器2**

```js
 setTimeout(()=>{
       console.log('定时器1')
       Promise.resolve(3).then(value=>{
       console.log("第三个promise"+value)
   })
   },0)

   setTimeout(()=>{
       console.log('定时器2')

   },0)
   Promise.resolve(1).then(value=>{
       console.log("第一个promise"+value)
   })
   Promise.resolve(2).then(value=>{
       console.log("第二个promise"+value)
   })
  /*
    第一个promise1
    第二个promise2
    定时器1
    第三个promise3
    定时器2
  */
```

这个也很好理解，当执行第一个定时器时，就把promise添加到了微队列

执行定时器2的时候，这个时候把微队列的取出来执行，所以**第三个promise** 先执行 **定时器2**后执行

# 4、面试题

### 第一题

```js
 setTimeout(()=>{
       console.log(1)
   },0)
   new Promise((resolve)=>{
       console.log(2)
       resolve()
   }).then(()=>{
       console.log(3)
   }).then(()=>{
       console.log(4)
   })

   console.log(5)
//25341
```

第一个看同步执行 25

当执行到第一个then时，上面已经有结果了 pedding变为resolved ，所以放到了微队列了

这时候执行第一个then，执行完了第二个then就有状态变化了 pedding变为resolved，这里也放到了微队列

最后执行定时器



### 第二题

```js
  const first = () => (new Promise((resolve, reject) => {
            console.log(3);
            let p = new Promise((resolve, reject) => {
                console.log(7);
                setTimeout(() => {
                    console.log(5);
                    resolve(6)
                }, 0)
                resolve(1)
            })
            resolve(2)
            p.then((arg) => {
                console.log(arg)
            })
        }))

        first().then((arg) => {
            console.log(arg);
        });
        console.log(4);
//374125
```

第一步同步执行：执行第一个promise输出3，接着执行第二个promise里面接着输出7，

```js
setTimeout(() => {
                    console.log(5);
                    resolve(6)
                }, 0)
```

定时器放在宏队列

往下执行 

```js
p.then((arg) => {
         console.log(arg)
 })
 //这个上面执行 resolve(1)的时候就有结果了，arg的值为1，有未成功-》成功的状态变化，所以这个加入到微队列里面[then2]
```

```js
  resolve(2) //这句话代表了第一个promise的状态右不成功到成功
   first().then((arg) => {
            console.log(arg);
    });
//这个第一个promise的then方法的回调函数也放在队列里，现在队里 [then2(1),then2(2)]
```

现在执行console.log(4) ,目前执行第一遍输出的是 3,7,4

现在取出微队列 输出 1,2

最后执行定时器 输出5，那个定时器的resolve(6)没有任何意义，因为改变了一次就不能在改变状态了

所以随后输出的是3,7,4,1,2,5

### 第三题

记住一句话：状态只能改变一次，所以就resolve('success1');是有效的

```js
const promise = new Promise((resolve, reject) => {
    resolve('success1');
    reject('error');
    resolve('success2');
});

promise.then((res) => {
    console.log('then:', res);
}).catch((err) => {
    console.log('catch:', err);
})
//then: success1

```

```
resolve 函数将 Promise 对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；

reject 函数将 Promise 对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

而一旦状态改变，就不会再变。
所以 代码中的reject('error'); 不会有作用。

Promise 只能 resolve 一次，剩下的调用都会被忽略。
所以 第二次的 resolve('success2'); 也不会有作用。

```



# 5、参考链接

[MDN promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

[MDN promise使用](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)

[promise详解]( https://www.cnblogs.com/xdl-smile/p/11807048.html )

[promise面试题](https://blog.csdn.net/FE_dev/article/details/83278508)

[promise参考视频](https://www.bilibili.com/video/av74611107?p=39)