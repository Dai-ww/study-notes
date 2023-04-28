### 1、什么是跨域，如何解决跨域？

```
同源策略：js不允许从一个域访问另一个域的对象。同源是指协议，主域名，子域名，端口相同，同源也就是同一个域
跨域：协议(http/https)、域名、端口号只要有一个不一样就是跨域；
目的 主要是防止 csrf 攻击
```

```
解决：JSONP 方式解决跨域（script标签不受同源策略的限制）
     CORS 设置跨域资源共享
     Nginx 反向代理解决跨域（推荐使用，配置简单）
```

### 2、识别数组的方法

```javascript
1、Array.isArray()
2、instanceof()
  判断被检测对象是否为构造函数的实例；
  原理：左侧被检测对象的原型链上是否包含右侧构造函数的prototype属性
  let arr = [1, 2, 3]
  arr instanceof Array // true
3、 constructor
  返回对象的构造函数，数组对象的构造函数为Array
  let arr = [1, 2, 3]
  arr.constructor === Array // true
  arr.constructor // ƒ Array() { [native code] }
4、Object.prototype.toString.call()

虽然Array也继承自Object，但js在Array.prototype上重写了toString，而我们通过toString.call(arr)实际上是通过原型链调用了。
  let arr = [1,2,3]
  Object.prototype.toString.call(arr) //[object Array]
5、isPrototypeOf()用于测试一个对象是否存在于另一个对象的原型链上
Array.prototype.isPrototypeOf(arr)
6、Object.getPrototypeOf()方法返回指定对象(arr)的原型，然后和Array的原型对比
let arr = [1,2,3];
console.log(Object.getPrototypeOf(arr) == Array.prototype); //true
```

### 3、闭包

```
作用：使用闭包主要是为了设计私有的方法和变量。
优缺点：闭包的优点是可以避免全局变量的污染；缺点是闭包会常驻内存，增加内存的使用量，使用不当很容易
造成内存泄漏。
概念：在JavaScript中，函数即闭包，只有函数才会产生作用域。（函数被引用包围）
闭包有3个特性：
（1）函数嵌套函数
（2）在函数内部可以引用外部的参数和变量
（3）参数和变量不会被垃圾回收机制回收
```

### 4、原型和原型链

```
原型
• 在JavaScript中，每当定义一个函数数据类型(普通函数、类)时候，都会天生自带一个prototype属性，
这个属性指向函数的原型对象，并且这个属性是一个对象数据类型的值。

• 每一个对象数据类型(普通的对象、实例、prototype......)也天生自带一个属性__proto__，属性值是当前
实例所属类的原型(prototype)。原型对象中有一个属性constructor, 它指向函数对象。

原型链
当访问一个对象的某个属性时，会先在这个对象本身属性上查找，如果没有找到，则会去它的__proto__
隐式原型上查找，即它的构造函数的prototype，如果还没有找到就会再在构造函数的prototype的__proto__
中查找，这样一层一层向上查找就会形成一个链式结构，我们称为原型链
• Object是JS中所有对象数据类型的基类(最顶层的类)在Object.prototype上没有proto这个属性。
```

![img](https://cdn.nlark.com/yuque/0/2021/png/22615045/1640048635224-f8eebf3b-5b6b-4ee0-a445-2dcc09152132.png?x-oss-process=image%2Fresize%2Cw_590%2Climit_0)



### 5、函数防抖，函数节流

> 函数防抖：在事件触发n秒后执行回调，若在这n秒内，再次被触发，则重新开始计时。

```js
function debounce(fn,delay){
  let timer = null;
  return function(){
    let context = this; // 保存this指向
    let args = arguments; // 拿到event对象
    if(timer){
    	clearTimeout(timer);Ï
    }
    timer = setTimeout(()=>{
        fn.apply(context,args)
      },delay)
  }
}
```

```
应用场景
搜索框搜索输入。只需用户最后一次输入完，再发送请求
手机号、邮箱验证输入检测
窗口大小resize。只需窗口调整完成后，计算窗口大小。防止重复渲染。
```

> 函数节流：在单位时间内，只有一次触发事件的回调函数执行。如果触发多次只有一次生效。

```js
function throttled(fn,delay){
  let timer = null;
  return function(...args){
    if(!timer){
      timer = setTimeout(()=>{
        fn.apply(this,args)
        timer = null;
      },delay)
    }
  }
}
```

```
应用场景
滚动加载，加载更多或滚到底部监听
搜索框，搜索联想功能
```

### 6、

