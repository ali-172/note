# JS高级

## 垃圾回收机制

内存分配 

内存使用

内存回收



### 内存泄漏

 某种原因导致未释放，或者无法释放。

算法说明：

栈：操作系统自动分配释放函数的参数值，局部变量。基本数据类型放到栈中。

堆：由程序员释放，程序员不释放，由垃圾回收机制释放。复杂数据类型放到堆中。



引用计数法和标记清除法。

引用计数法  如果对象嵌套引用会导致无法回收。  对象=null 

标记清除法：

根对象出发，遍历所有可达的对象，标记为存活，这是标记

遍历整个堆内存，将未被标记为存活的对象判定为垃圾，回收其占用的内存空间。





## 闭包

内层函数 + 外层函数的变量。

作用：外部也能访问函数内部的变量。

应用实例  

```js
     function outer() {

            let num = 10;

            function inner() {
                num++;
                console.log(`第${num}次调用函数`);
            }

            return inner;
        }

        const fun = outer();
        fun();
```

num 作为局部变量  不会轻易被修改，保证安全性。

存在内存泄露的风险。



## 箭头函数

```js
//普通写法
const　fn= function(){

}

//箭头函数
const fn1=()=>{
    
}
//只有一个小括号可以省略小括号
const fn1 = x =>{
    
}
fn1(1);

//只有一行代码  省略大括号
const fn2 =(x,y) => console.log(x,y);
fn2(1,2);
```

没有this  去找上一层去找。  需要用到this 不适用箭头函数

```js
const a=["Hydrogen", "Helium", "Lithium", "Beryllium"];
        const a2 =a.map(function(s){
            return s.length;
        })

        const a3=a.map((s)=> s.length);  //语法更为简洁
```

### 日期

```
//  创建一个日期
const newDate= new Date(); 
```

## 正则表达式

匹配字符组合的一种模式。

```js
   const str = "你好我在学习Java和前端";
        const reg = /java/
        console.log(reg.test(str));
```

元字符 ：特殊的字符 

边界符： ^ 开始   $结束

量词 ：设置某个模式出现的次数 

```
*  0次或者多次
+   1次或者多次
？   0次或者1次

{n}  n次
{n, }  n次及以上
{n,m}  n-m次
```

字符类

```
[] 匹配字符 
```



```
   console.log(/^哈$/.test('哈哈'))  //fasle
```

## 对象

属性

行为，方法

```js
// 增删改查
let obj ={
name:"ali",
age:19,
gender:"男"
}
//查 1
obj.age
// 带有字符串 
obj["good-name"]  
// 改 增 
obj.age= 22
//删
delete obj.age

```

#### 遍历对象

```
for　(let k in obj){
console.log(obj[k])   //这么写是因为 k为字符串类型！
}
```

```js
for (let i=0;i<student.length;i++){
for (let key in student[i]){
console.log(student[i][key])
}
}
```

## Promise

### 回调地狱

丑 可读性差  

```js
doSomething(function (result) {
  doSomethingElse(result, function (newResult) {
    doThirdThing(newResult, function (finalResult) {
      console.log(`得到最终结果：${finalResult}`);
    }, failureCallback);
  }, failureCallback);
}, failureCallback);
```

异步调用新的解决方案 

```js
function doSomething() {
  return new Promise((resolve) => {
    setTimeout(() => {
      // 在完成 Promise 之前的其他操作
      console.log("完成了一些事情");
      // promise 的兑现值
      resolve("https://example.com/");
    }, 200);
  });
}
```

将回调的函数添加到返回的promise对象上，而不是传递参数。

优点：支持链式调用，不会产生回调地狱，回调的方式更加灵活。

#### 重点

```js
doSomething()  
  .then((result) => doSomethingElse(result))   //如果这里返回 100 普通值 不会断  下一个then收集到的就是100！
  .then((newResult) => doThirdThing(newResult))
   // 如果 返回的是promise  也不会断  下一个then收到的就是promise的兑现值！  懂了！
  .then((finalResult) => {
    console.log(`得到最终结果：${finalResult}`);
  })
  .catch(failureCallback);

//  必须return 否则就会漂浮   即使值为undefined 也必须返回！

```

#### 竞态条件

```js
const listOfIngredients = [];

doSomething()
  .then((url) => {
    // fetch(url) 前缺少 `return` 关键字。 直接返回undefined  导致退出执行下面的then
    fetch(url)
      .then((res) => res.json())
      .then((data) => {
        listOfIngredients.push(data);
      });
  })
  .then(() => {
    console.log(listOfIngredients);
    // listOfIngredients 永远为 []，因为 fetch 请求还没有完成。
  });
```

```js
const listOfIngredients = [];

doSomething()
  .then((url) => {
    // fetch 调用前面现在包含了 `return` 关键字。
    return fetch(url)
      .then((res) => res.json())
      .then((data) => {
        listOfIngredients.push(data);
      });
  })
  .then(() => {
    console.log(listOfIngredients);
    // listOfIngredients 现在将包含来自 fetch 调用的数据。
  });
```

#### 扁平化处理

```js
      doSomething()
        .then((url)=> fetch(url))
        .then((res)=>res.json())
        .then((data)=>{
            listOfIngredients.push(data);
        })
        .then(()=>{
            console.log(listOfIngredients);
        })
```

```js
// 异步标准写法     
async function logIngredients() {

            const url =await doSomething();
            const res =await fetch(url);
            const data =await res.json();
            listOfIngredients.push(data);
            console.log(listOfIngredients)
            
        }
```

#### 错误处理

.catch()  形成异常透传  最后再处理！

#### 初体验

```js
<button class="chou">choujang</button>

    <script>

        const btn = document.querySelector(".chou");

        function getRandomNumber() {
            return Math.floor(Math.random() * 100) + 1;
        }

        btn.addEventListener("click", () => {
            console.log("点击")
            const p = new Promise((resolve, reject) => {

                const n = getRandomNumber()

                // 接受n
                setTimeout(() => {
                    if (n < 30) {
                        resolve(n);
                    } else {
                        reject(n);
                    }

                }, 1000)


            })

            p.then(
                (value) => {
                    alert("yes" + value)

                }, (reason) => {
                    alert("no" + reason)
                })

        })




    </script>
```

#### 读取文件

```js


const fs = require('fs');

const p = new Promise((resolve, reject) => {

    fs.readFile('./contnt.txt', (err, data) => {
        if (err) reject(err);
        else resolve(data)
    });


})

p.then(value => {
    console.log(value.toString()
    )

}, reason => {
    console.log(reason)

})



```

#### 获取网络请求

别打错，别犯低级错误！ 打字的时候看着！



```js
 const btn = document.querySelector("#btn");
        btn.addEventListener("click", () => {
            console.log("进入");

            // 封装为promise

            const p = new Promise((resolve, reject) => {

                const xhr = new XMLHttpRequest();

                xhr.open("GET", 'https://api.apiopen.top/api/articles');

                xhr.send();

                xhr.onreadystatechange = function () {
                    if (xhr.readyState === 4) {
                        if (xhr.status >= 200 && xhr.status < 300) {

                            resolve(xhr.response)
                        } else {

                            reject(xhr.status)
                        }
                    }
                }



            })

            p.then((value) => {
                console.log(value);
            }, (response) => {
                console.log(response);
            })




        })
```



封装为函数读取文件

```js

function mineReadFile(path) {

    return new Promise((resolve, reject) => {
        //读取文件
        require('fs').readFile(path, (err, data) => {
            if (err) reject(err);
            else resolve(data);
        })
    })
}

//调用函数
mineReadFile('./content.txt').then((value) => {
    console.log(value.toString());
}, (reason) => {
    console.log(reason)
})
```

#### promisify

```js
const util = require("util")

const fs = require("fs")

let mineReadFile = util.promisify(fs.readFile);

mineReadFile("./content.txt").then(value => {
    console.log(value.toString())
})
```

#### 封装ajax

```js
// 封装ajax

        function sendAjax(url) {

            return new Promise((resolve, reject) => {

                //创建对象
                const xhr = new XMLHttpRequest();
                //返回的类型设置为json
                xhr.responseType = "json"

                // 
                xhr.open("GET", url);
                xhr.send();
                //处理结果
                xhr.onreadystatechange = function () {
                    //===4
                    if (xhr.readyState === 4) {
                        if (xhr.status >= 200 && xhr.status < 300) {
                            resolve(xhr.response);
                        } else {
                            resolve(xhr.status);
                        }
                    }

                }

            })

        }

        //调用函数
        sendAjax("https://api.apiopen.top/api/articles").then((value) => {
            console.log(value);
        })
```

#### 状态

+ pending  未决定的
+ resolved   成功的
+ rejected  失败的

值

PromiseResult   只能由resolve 和reject 决定



#### Promise

Promise.resolve

```js
   // 创建一个非Promise类型的数据  返回的是成功的Promise对象 
        const s1 = Promise.resolve(521);
        console.log(s1);

        //创建一个Promise类型的对象 返回的结果由创建的决定
        const s2 = Promise.resolve(new Promise((resolve, reject) => {
            reject('yes');
        }))

        s2.catch((error) => {
            console.log(error)
        })
```

Promise.reject  只能返回失败的Promise对象

Promise.all  所有的为成功 才返回成功的对象 有一个失败 只返回那个失败的对象

Promsie.race  返回第一个改变的状态的对象



#### 常见的问题

1. 改变状态的三种  resolve   reject  throw  
2. 异常穿透   最后指定一个失败回调即可
3. 终止 promise链  返回一个pending状态的promise对象



### 自定义封装Promise

```js


function Promise(executor) {
    this.PromiseState = "pending";
    this.PromiseResult = null;
    //回调函数
    this.callbacks = [];

    const self = this;

    function resolve(data) {
        //确保状态只能修改一次
        if (self.PromiseState === "pending") {
            //修改状态
            self.PromiseState = "fullfilled";
            //修改结果
            self.PromiseResult = data;

            // 遍历数组
            self.callbacks.forEach((item) => {
                item.onResolved(data);
            })
        }




    }

    function reject(data) {
        if (self.PromiseState === "pending") {
            self.PromiseState = "rejected";
            self.PromiseResult = data;

            self.callbacks.forEach((item) => {
                item.onRejected(data);
            })
        }


    }

    //同步执行
    try {
        executor(resolve, reject);

    } catch (error) {
        reject(error);

    }


}

Promise.prototype.then = function (onResolved, onRejected) {

    const self = this;
    return new Promise((resolve, reject) => {

        //封装函数
        function callback(type) {
            try {
                const result = type(self.PromiseResult);

                if (result instanceof Promise) {
                    //类型为Promise 
                    result.then((r) => {
                        resolve(r);

                    }, (v) => {
                        reject(v);

                    })

                } else {
                    //不为Pormise  直接类型为fullfilled  值返回
                    resolve(result)

                }

            } catch (error) {
                reject(error);
            }
        }

        if (this.PromiseState === "fullfilled") {
            //进入成功

            callback(onResolved);

        }

        if (this.PromiseState === "rejected") {
            callback(onRejected);
        }

        //异步处理  
        if (this.PromiseState === "pending") {
            //保存回调函数  状态变化之后再执行
            this.callbacks.push({
                onResolved: function () {
                    callback(onResolved);




                },  //简写
                onRejected: function () {
                    callback(onRejected);

                }
            })

        }

    })


}


// 异常透传这里有点问题！

```

长点心吧！

```html
//调用
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>

    <script src="./promise.js"></script>
    <script>

        let p = new Promise((resolve, reject) => {
            //实现throw 改变状态

            // 异步状态改变

            setTimeout(() => {
                resolve("ok");
                // reject("ok");


            }, 100)

            // 同步
            // reject("Error");

        })



        // console.log(res);

        // console.log(p)

        // 同步状态获取then结果
        // 非promise直接返回fullfilled
        // 是promise由其类型决定

        //异步状态获取then结果

        p.then().then(value => {
            console.log("222")
        }).then(value => {
            console.log("333")
        }).catch(error => {
            console.warn(error)
        })






    </script>

</body>

</html>
```

完整版

```js


function Promise(executor) {
    this.PromiseState = "pending";
    this.PromiseResult = null;
    //回调函数
    this.callbacks = [];

    const self = this;

    function resolve(data) {
        //确保状态只能修改一次
        if (self.PromiseState === "pending") {
            //修改状态
            self.PromiseState = "fullfilled";
            //修改结果
            self.PromiseResult = data;

            // 遍历数组
            self.callbacks.forEach((item) => {
                item.onResolved(data);
            })
        }




    }

    function reject(data) {
        if (self.PromiseState === "pending") {
            self.PromiseState = "rejected";
            self.PromiseResult = data;

            self.callbacks.forEach((item) => {
                item.onRejected(data);
            })
        }


    }

    //同步执行
    try {
        executor(resolve, reject);

    } catch (error) {
        reject(error);

    }


}

Promise.prototype.then = function (onResolved, onRejected) {

    const self = this;

    if (typeof onRejected !== 'function') {
        onRejected = reason => {
            throw reason;
        }
    }

    if (typeof onResolved !== 'function') {
        onResolved = value => value;
    }
    return new Promise((resolve, reject) => {

        //封装函数
        function callback(type) {
            try {
                const result = type(self.PromiseResult);

                if (result instanceof Promise) {
                    //类型为Promise 
                    result.then((r) => {
                        resolve(r);

                    }, (v) => {
                        reject(v);

                    })

                } else {
                    //不为Pormise  直接类型为fullfilled  值返回
                    resolve(result)

                }

            } catch (error) {
                reject(error);
            }
        }

        if (this.PromiseState === "fullfilled") {
            //进入成功

            callback(onResolved);

        }

        if (this.PromiseState === "rejected") {
            callback(onRejected);
        }

        //异步处理  
        if (this.PromiseState === "pending") {
            //保存回调函数  状态变化之后再执行
            this.callbacks.push({
                onResolved: function () {
                    callback(onResolved);




                },  //简写
                onRejected: function () {
                    callback(onRejected);

                }
            })

        }

    })


}

//catch方法

Promise.prototype.catch = function (onRejected) {
    //这里巧妙地借用then方法
    return this.then(undefined, onRejected);
}

```

#### 封装api

```

```



### js模块

#### 导出模块

```js
// modules/square.js  

export const name ="square";  //导出变量

//导出函数
export function draw(){
 
}

// 汇总导出
export {name,draw}
```

#### 导入模块

```
import {name,draw} from './modules/square.js'
```

导入映射导入模块

```js
<script type="importmap">
    
    "imports":{
        //属性名为模块标识符
       "shapes":"./shapes/square.js",
       "shapes/square":"./modules/shapes/square.js",
       "https://example.com/shapes/square.js": "./shapes/square.js",
       "https://example.com/shapes/": "/shapes/square/",
       "../shapes/square": "./shapes/square.js" 
        "square":"./shapes/square.js"
    }

//裸模块名作为模块标识符
import {name as squareNameOne  } from "shapes";   //裸模块名
import { name as squareNameTwo  } from "shapes/square";  //带子路径的裸模块名

import { name as squareNameThree } from "https://example.com/shapes/square.js"; //重新映射一个url到另外一个url

// 裸名称导入模块 
import { name as squareName} from "square";





</script>
```

重映射模块路径

```
{
  "imports": {
    "lodash": "/node_modules/lodash-es/lodash.js",
    "lodash/": "/node_modules/lodash-es/"
  }
}

import fp from "lodash/fp.js"
```



## Ajax

```js
//引入axios
src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"
//使用axios
axios({
            url: "http://hmajax.itheima.net/api/province"
        }).then(result => {
            console.log(result.data.list);  //获取到省份列表

            //数据展示到页面p标签
            document.querySelector(".my-p").innerHTML = result.data.list.join("<br>");
        })
```

### url 

统一资源定位符 网址  用于访问网上的资源。

组成  http: 协议 //hmajax.itheima.net  域名  /api/province 资源路径



### url查询参数

浏览器提供服务器额外的信息，让服务器返回浏览器想要的数据。

```js
   axios({
            url: "http://hmajax.itheima.net/api/area",
            params: {
                pname: '山东省',
                cname: '聊城市'
            }
        }).then((result => {
            console.log(result)
        }))
        
        //pname:pname  可以简写为pname
        
        
```

专心。一次就要做好！

#### 提交数据

```js
      document.querySelector("button").addEventListener("click", () => {

            console.log("点击事件")

            axios({
                url: "http://hmajax.itheima.net/api/register",
                method: "post",
                data: {
                    username: "iteheimawsos",
                    password: "123111"
                }
            }).then((res) => {
                console.log(res)
            })
```

### 常用的请求方法

GET  获取数据

POST  数据提交

PUT  修改数据

DELETE  删除数据



### HTTP协议

规定了浏览器发送和服务器返回内容的格式 

请求报文  ：**按照协议要求的格式 发送给服务器的内容**

组成：

- 请求行  请求方法 url  协议
- 请求头  附加信息
- 请求体  发送的资源

响应报文：服务器返回给浏览器的内容

组成：

- 响应行 协议，HTTP响应状态码 状态信息
- 响应头 
- 响应体



###  form-serialize 插件

```
//  获取表单元素值   hash 返回js对象   允许为空
const data =serialize(form,{hash:true,empty:true});

```



## 聊天的案例

```js
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
    <script>
        // 获取机器人回答消息 - 接口地址: http://hmajax.itheima.net/api/robot
        // 查询参数名: spoken
        // 查询参数值: 我说的消息

        // 1. 点击发送和敲击回车键，都能发送聊天消息
        // 2. 把自己和对方消息都展示到页面上
        // 3. 当聊天消息出现滚动条时，始终让最后一条消息出现在视口范围内

        // 获取元素
        const input = document.querySelector(".chat_input"); //输入框
        const sendImg = document.querySelector(".send_img"); //发送按钮
        const chatList = document.querySelector(".chat_list");  //聊天列表
        const chatBox = document.querySelector(".chat"); //滚动容器


        //封装发送消息的函数

        function sendMsg() {
            const msg = input.value;
            if (!msg.trim()) return;

            // 消息展示到右侧
            renderLi("right", msg);

            // 清空输入框
            input.value = "";

            //发送请求
            axios({
                url: "http://hmajax.itheima.net/api/robot",
                params: {
                    spoken: msg
                }
            }).then((res) => {
                console.log(res.data.data.info.text);
                const robotMsg = res.data.data.info.text;

                //消息展示
                renderLi("left", robotMsg);

            })



        }

        // 渲染函数
        function renderLi(type, msg) {
            const li = document.createElement("li");
            li.className = type;

            if (type === "right") {
                // 我的消息
                li.innerHTML = `
            <span>${msg}</span>
            <img src='./assets/send.png' alt=''>
            `;
            } else {
                // 机器人的消息
                li.innerHTML = `
            <img src='./assets/send.png' alt=''>
            <span>${msg}</span>
            `;
            }

            // 消息添加到列表
            chatList.appendChild(li);

            // 自动滚动到底部
            chatBox.scrollTop = chatBox.scrollHeight;

        }

        // 点击事件
        sendImg.addEventListener("click", () => {
            sendMsg();
        })

        //回车事件
        input.addEventListener("keydown", (e) => {
            if (e.key === "Enter") {
                sendMsg();
            }
        })






    </script>
```

