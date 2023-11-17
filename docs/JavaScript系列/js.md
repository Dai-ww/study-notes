### ES6 新特性

- let const 关键字
- 函数默认参数
- 箭头函数
- 摸板字符串
- 扩展运算符 解构赋值 对象字面量简化
- 类 模块化
- promise（all，race，any） async await
- map set
- 迭代器（iterator generator）
- proxy 代理 reflect 反射
- 数组（includes、flat、from、find、fill）、对象（assign、getPrototypeOf、**proto**方法）、字符串的扩展（includes、startsWith、repeat）

### AST 抽象语法树

> 它是源代码语法结构的一种抽象表示

- AST 生成：js 执行的第一步是读取 js 文件中的字符流，然后通过词法分析生成 token，之后再通过语法分析( Parser )生成 AST，最后生成机器码执行。
- 词法分析：每个关键字是一个 token，每个标识符，操作符也是一个 token，例如 var 这三个字符，语义上不能再被分解。注释和空白字符会被过滤掉。 整个代码最终会被分割进一个 tokens 列表。
- 语法分析：将词法分析出来的 tokens 转化为有语法含义的抽象树结构。

**AST 运用广泛**：

1. 编辑器的错误提示、代码格式化、代码高亮、代码自动补全；
2. elint、pretiier 对代码错误或风格的检查；
3. webpack 通过 babel 转译 javascript 语法；

### 闭包

> 在 JavaScript 中，函数即闭包，只有函数才会产生作用域。（函数被引用包围）

- 作用：使用闭包主要是为了设计私有的方法和变量。
- 优缺点：闭包的优点是可以避免全局变量的污染；缺点是闭包会常驻内存，增加内存的使用量，使用不当很容易
  造成内存泄漏。
- 闭包有 3 个特性：
  （1）函数嵌套函数
  （2）在函数内部可以引用外部的参数和变量
  （3）参数和变量不会被垃圾回收机制回收
- 使用场景：

```js
1、函数防抖
2、设置私有变量
3、拿到正确的值：
for(var i=0;i<10;i++){
    setTimeout(function(){
        console.log(i)//10个10
    },1000)
}

for(var i=0;i<10;i++){
((j)=>{
  setTimeout(function(){
        console.log(j)//1-10
    },1000)})(i)

}
```

### 说说 JavaScript 中内存泄漏的几种情况

1. 意外的全局变量 （可使用严格模式避免）
2. 定时器
3. 闭包
4. 事件监听，不使用时未取消监听

### 原型和原型链

##### 原型

- 在 JavaScript 中，每当定义一个函数数据类型(普通函数、类)时候，都会天生自带一个 prototype 属性，
  这个属性指向函数的原型对象，并且这个属性是一个对象数据类型的值。

- 每一个对象数据类型(普通的对象、实例、prototype......)也天生自带一个属性**proto**，属性值是当前
  实例所属类的原型(prototype)。原型对象中有一个属性 constructor, 它指向函数对象。

##### 原型链

当访问一个对象的某个属性时，会先在这个对象本身属性上查找，如果没有找到，则会去它的**proto**
隐式原型上查找，即它的构造函数的 prototype，如果还没有找到就会再在构造函数的 prototype 的**proto**
中查找，这样一层一层向上查找就会形成一个链式结构，我们称为原型链

**tips**: Object 是 JS 中所有对象数据类型的基类(最顶层的类)在 Object.prototype 上没有 proto 这个属性。

![Alt text](image.png)

### 函数防抖，函数节流

> 函数防抖：在事件触发 n 秒后执行回调，若在这 n 秒内，再次被触发，则重新开始计时。

```js
function debounce(fn, delay) {
  let timer = null;
  return function () {
    let context = this; // 保存this指向
    let args = arguments; // 拿到event对象
    if (timer) {
      clearTimeout(timer);
      Ï;
    }
    timer = setTimeout(() => {
      fn.apply(context, args);
    }, delay);
  };
}
```

**应用场景**:

1. 搜索框搜索输入。只需用户最后一次输入完，再发送请求
2. 手机号、邮箱验证输入检测
3. 窗口大小 resize。只需窗口调整完成后，计算窗口大小。防止重复渲染。

> 函数节流：在单位时间内，只有一次触发事件的回调函数执行。如果触发多次只有一次生效。

```js
function throttled(fn, delay) {
  let timer = null;
  return function (...args) {
    if (!timer) {
      timer = setTimeout(() => {
        fn.apply(this, args);
        timer = null;
      }, delay);
    }
  };
}
```

**应用场景**:

1. 滚动加载，加载更多或滚到底部监听
2. 搜索框，搜索联想功能

### 什么是跨域，如何解决跨域？

**同源策略**：js 不允许从一个域访问另一个域的对象。同源是指协议，主域名，子域名，端口相同，同源也就是同一个域

**跨域**：协议(http/https)、域名、端口号只要有一个不一样就是跨域；

**目的**： 主要是防止 csrf 攻击

**解决**：

- JSONP 方式解决跨域（script 标签不受同源策略的限制）
- CORS 设置跨域资源共享
- Nginx 反向代理解决跨域（推荐使用，配置简单）

### 识别数组的方法

```js
1. Array.isArray()
2. instanceof()
   判断被检测对象是否为构造函数的实例；
   原理：左侧被检测对象的原型链上是否包含右侧构造函数的 prototype 属性
   let arr = [1, 2, 3]
   arr instanceof Array // true
3. constructor
   返回对象的构造函数，数组对象的构造函数为 Array
   let arr = [1, 2, 3]
   arr.constructor === Array // true
   arr.constructor // ƒ Array() { [native code] }
4. Object.prototype.toString.call()
   虽然 Array 也继承自 Object，但 js 在 Array.prototype 上重写了 toString，而我们通过 toString.call(arr)实际上是通过原型链调用了。
   let arr = [1,2,3]
   Object.prototype.toString.call(arr) //[object Array]
5. isPrototypeOf()用于测试一个对象是否存在于另一个对象的原型链上 Array.prototype.isPrototypeOf(arr)
6. Object.getPrototypeOf()方法返回指定对象(arr)的原型，然后和 Array 的原型对比
   let arr = [1,2,3];
   console.log(Object.getPrototypeOf(arr) == Array.prototype); //true
```

### 如何实现一个 new

1. 首先创建一个空对象。
2. 将构造函数内部的 this 赋值创建的空对象，用构造函数的内部方法修改空对象。
3. 将创建的对象的原型指向构造函数的原型。
4. 返回一个对象（如果构造函数本身有返回值且是对象类型，就返回本身的返回值，如果没有才返回新对象）

### 如何实现请求的数量控制

- promise 解决并发请求

```js
设置一个pool池放置这些请求返回的promise，然后发max个（构造函数发送），函数中每次都把promise# push到pool中，然后进行then回调的注册，里边首先把promise拿出来，然后拿数组id，判断进行递归。

let request = function (id) {
        return new Promise((resolve, reject) => {
          //随机一个执行时间
          let time = Math.floor(10000 * Math.random());
          console.log(`id为${id}开始请求,预计执行时间${time / 1000}`);
          setTimeout(() => {
            resolve(id);
          }, time);
        }).then((id) => {
          console.log(`id为${id}的请求进行逻辑处理`);
          return id;
        });
      };
      let idArray = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
      let pool = []; //并发池
      let max = 3; //最大并发量
      function run() {
        //启动函数，直接将max个请求进行执行
        for (let i = 0; i < max; i++) {
            // debugger;
          console.log(i)
          send(request(idArray.shift()));
        }
      }
      function send(promise) {
        pool.push(promise);
        console.log(pool,'pool');
        promise.then((res) => {
          console.log(`id${res}的请求已经处理完毕,当前并发${pool.length}`);
          //移除已处理的请求
          pool.splice(pool.indexOf(promise), 1);
          let id = idArray.shift();
          if (id !== undefined) {
            send(request(id));
          }
        });
      }
      run()
```

```js
利用async写成同步代码，用Promise.race(pool) 来控制，每次pool中有resolve的promise（处理# 完的请求）,就可以继续发送新请求了。
async function run(){
    for (let i=0;i<idArray.length;i++){
        let promise = request(idArray[i]);
        promise.then((res)=>{
            console.log(`id${res}的请求已经处理完毕,当前并发为${pool.length}`);
            pool.splice(pool.indexOf(promise),1);
        })
        pool.push(promise);
        //这里是重点，当满了就阻塞
        if (pool.length==max){
            await Promise.race(pool);
        }
    }
}
run();
```
