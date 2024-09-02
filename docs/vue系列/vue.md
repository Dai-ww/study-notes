### vue 组件通讯（即传值）有哪些

1. props/$emit
2. $emit/$on
3. vuex
4. $attrs/$listeners
5. provide/inject
6. $parent / $children 与 ref

**父子通信**：
父向子传递数据是通过 props，子向父是通过 events（$emit）；通过父链 / 子链也可以通信（$parent
/ $children）；
ref 也可以访问组件实例；provide / inject API；$attrs/$listeners

**兄弟通信**：
Vuex

**跨级通信**：
Vuex；provide / inject API、$attrs/$listeners

#### vue 父组件异步传递 props 值，子组件接收不到

> 原因：父组件传递的值为异步获取，子组件挂载完毕后父组件才会挂载

**解决方式**

1. 使用 v-if
2. 使用 wacth 监听
3. 使用 key

### Vue 子组件为什么不可以修改父组件传递的 Prop

> 为了保证数据的单向流动，便于对数据进行追踪，避免数据混乱

单向数据流，易于监测数据的流动，出现了错误可以更加迅速的定位到错误发生的位置。一个父组件可能存在许多个子组件如果每个子组件可以修改父级传递的数据，将会导致修改数据的源头不止一处。保证数据修改源唯一。

- 如果修改了，Vue 是如何监控到属性的修改并给出警告的？
  在组件执行 initProps 方法时，会执行 defineReactive 方法。传入的第四个参数是自定义的 set 函数，该函数会在触发 props 的 set 方法时执行，当 props 修改了，就会运行这里传入的第四个参数，然后进行判断，如果不是 root 根组件，并且不是更新子组件，那么说明更新的是 props，所以会警告。

但是，源码只是进行了浅度监听，即只能监听几种基础类型，如果 Props 是个对象，在子组件修改某个 key 的值的话就不会报错了

### 观察者模式 和 订阅发布模式 的区别

**区别**：最大的区别就是订阅发布模式有一个事件调度中心。观察者模式中主体和观察者是互相感知的，发布-订阅模式是借助第三方来实现调度的，发布者和订阅者之间互不感知。

### vue 在渲染大量数据时应该怎么优化

- 添加加载动画 loading 图等，优化用户体验
- 利用服务器渲染 SSR，在服务端渲染组件
- 避免浏览器处理大量的 dom，比如懒加载，异步渲染组件，使用分页
- 对于固定的非响应式的数据，使用 Object.freeze 冻结

### pinia 和 vuex 有什么区别，为什么要使用 pinia

- Vuex：State、Gettes、Mutations(同步)、Actions(异步)
- Pinia： State、Gettes、Actions(同步异步都支持)

**优点**：

1. Pinia 对 vue2 和 vue3 都支持,尤其是 TypeScript 的项目
2. 没有模块的嵌套结构
3. 不需要注入、导入函数、调用它们
4. 无需手动添加 store，它的模块默认情况下创建就自动注册的
5. 支持 Vue DevTools
6. 支持服务端渲染

**缺点**：
与 Vuex 相比，它没有庞大的社区支持和解决方案。
Pinia 不支持调试功能，如时间旅行和编辑。

### vue nextTick 怎么实现的

> 在修改数据之后立即使用这个方法，获取更新后的 DOM。nextTick 接受一个延迟到下一个 DOM 更新周期的回调函数

nextTick 存在的原因是 Vue 在更新 DOM 时是异步执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部对异步队列尝试使用原生的 Promise.then、MutationObserver 和 setImmediate，如果执行环境不支持，则会采用 setTimeout(fn, 0) 代替

场景：1、在 Vue 生命周期的 created()钩子函数进行的 DOM 操作一定要放在 Vue.nextTick()的回调函数中 2、更改数据后想立即使用 js 操作新的视图

**与 setTimemeout 的区别**：都是异步函数，但是 nextTick 比 setTimeout 先执行

**步骤**：

1. 将回调函数添加到 callbacks 中等待执行
2. 将执行函数放入到宏任务(setImediate 或者 setTimeout)或者微任务(promise,mutationObserver)中
3. 事件循环到了微任务或者宏任务，执行函数依次执行 callbacks 中的回调

### vue3 相比 vue2 做了哪些改变

1. 组合式 API
2. 双向绑定的更新，vue2 的双向数据绑定是利⽤ ES5 的⼀个 API ，Object.definePropert()对数据进⾏劫持 结合 发布订阅模式的⽅式来实现的。vue3 中使⽤了 es6 的 ProxyAPI 对数据代理，通过 reactive() 函数给每⼀个对象都包⼀层 Proxy，通过 Proxy 监听属性的变化，从⽽实现对数据的监控。
3. Vue3 支持了多根节点组件，也就是 fragment，异步组件 Suspense

**vue3 对比 vue2 的优点**：更小（引入 tree-shaking），更友好（component API），proxy 替换 Object.definePropert()（可以劫持整个对象）

### 相⽐于 vue2 版本，使⽤ proxy 的优势如下

1. defineProperty 只能监听某个属性，proxy 是对整个对象进行劫持，defineProperty 监听对象时需要递归绑定监听，对新增删除属性都不能监听到（需要使用 api $set $delete 等）。
2. 可以省去 for in、闭包等内容来提升效率（直接绑定整个对象即可）
3. 可以监听数组，不⽤再去单独的对数组做特异性操作,通过 Proxy 可以直接拦截所有对象类型数据的操作，完美⽀持对数组的监听。

### keep-alive 实现原理

> keep-alive 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中；使用 keep-alive 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。

主要是根据 LRU 策略缓存组件 VNode，最后在 render 时返回子组件的 VNode。缓存渲染过程会更新 keep-alive 插槽，重新再 render 一次，从缓存中读取之前的组件 VNode 实现状态缓存。

LRU（Least recently used，最近最少使用）：策略根据数据的历史访问记录来进行淘汰数据。

**属性**
include 字符串或正则表达式，只有名称匹配的组件会被缓存
exclude 字符串或正则表达式，任何名称匹配的组件都不会被缓存
max 数字，最多可以缓存多少组件实例

**作用**

- 减少数据更新
- 优化性能
- 解决数据变化后视图不更新的问题

**关联的生命周期**
created > mounted > activated > deactivated

1. 当组件被从内存中恢复时调用。如果之前被缓存的组件再次被切回，将触发 activated 生命周期钩子。在这个阶段，我们可以执行一些需要在组件激活时进行的操作。
2. 当组件被缓存到内存中时调用。在切换到其他组件时，原组件不会被销毁，而是被缓存，此时触发 deactivated 生命周期钩子

### vue 的双向绑定原理

> Vue 数据双向绑定原理是通过 数据劫持 + 发布者-订阅者模式 的方式来实现的，首先是通过 ES5 提供的 Object.defineProperty() 方法来劫持（监听）各属性的 getter、setter，并在当监听的属性发生变动时通知订阅者，是否需要更新，若更新就会执行对应的更新函数

- 常见的基于数据劫持的双向绑定有两种实现

  - 一个是目前 Vue 在用的 Object.defineProperty
  - 一个是 ES2015 中新增的 Proxy，而在 Vue3.0 版本后加入 Proxy 从而代替 Object.defineProperty

- 实现：
  1. 利用 Proxy 或 Object.defineProperty 生成的 Observer 针对对象/对象的属性进行"劫持",在属性发生变化后通知订阅者
  2. 解析器 Compile 解析模板中的 Directive(指令)，收集指令所依赖的方法和数据,等待数据变化然后进行渲染
  3. Watcher 属于 Observer 和 Compile 桥梁,它将接收到的 Observer 产生的数据变化,并根据 Compile 提供的指令进行视图渲染,使得数据变化促使视图变化

### vuex 工作流

- 五大核心属性

1. state,用来存放数据相当于 vue 里的 data
2. mutations,同步方法，可以修改 state 中的数据
3. actions，异步方法，可以发送请求，不能直接修改 state 中的数据，要通过调用 mutations 里的方法修改数据
4. getters，相当于 vue 里的 computed
5. modules，模块化

- 工作流

1. 通过 dispatch 去提交一个 actions
2. 在 actions 接收到事件后，在 actions 中执行一些同步或异步操作
3. 根据不同的情况分发给不同的 mutations，actions 通过 commit 触发 mutations
4. mustations 在触发后就会去更新 state
5. 在 state 更新完毕后，就会通知 vue 进行渲染

**为什么不能在 mutations 执行异步操作**
Vuex 中所有的状态更新的唯一途径都是 mutation，异步操作通过 Action 来提交 mutation 实现，这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。
每个 mutation 执行完成后都会对应到一个新的状态变更，这样 devtools 就可以打个快照存下来，然后就可以实现 time-travel 了。如果 mutation 支持异步操作，就没有办法知道状态是何时更新的，无法很好的进行状态的追踪，给调试带来困难。

### vue 导航守卫

> 导航守卫就是路由跳转过程中的一些钩子函数

- 全局守卫：指路由实例上直接操作的钩子函数
  - beforeEach：在路由跳转前触发
  - beforeResolve：在所有组件内守卫和异步路由组件被解析后触发
  - afterEach：路由跳转完成后触发
- 单个路由守卫：指在单个路由配置的时候也可以设置的钩子函数
  - beforeEnter：在单个路由跳转前触发，在 beforeEach 之后紧随执行
- 组件内守卫：是指在组件内执行的钩子函数，类似于组件内的生命周期
  - beforeRouteEnter：路由进入之前调用，全局守卫 beforeEach 和独享守卫 beforeEnter 之后
  - beforeRouteUpdate：在当前路由改变时，并且该组件被复用时调用
  - beforeRouteLeave：导航离开该组件的对应路由时调用

### vue 路由两种模式的区别

> hash 模式，history 模式

- hash：即地址栏 URL 中的 # 符号。hash 虽然出现在 URL 中，但不会被包括在 HTTP 请求中，对后端完全没有影响，其原理是 onhashchange 事件
- history：pushState() 和 replaceState()。通过这两个 API 可以改变 url 地址且不会发送请求，不仅可以读取历史记录栈，还可以对浏览器历史记录栈进行修改
  **区别**：前面的 hashchange，你只能改变#后面的 url 片段。而 pushState 设置的新 URL 可以是与当前 URL 同源的任意 URL。
  history 模式则会将 URL 修改得就和正常请求后端的 URL 一样,如后端没有配置对应/user/id 的路由处理，则会返回 404 错误

  **如何进行路由配置**

  1. 引入 vue-router 注册 Vue.use(VueRouter)
  2. 创建一个路由对象，里面包含了路由的路径和对应的组件
  3. 创建一个 router 实例，并传入 `routes` 配置
  4. 将 router 实例挂载到根实例上，这样所有的子组件都可以访问到 router 实例
  5. 最后，通过 router-view 组件渲染匹配到的组件

## vue 动态权限绑定渲染列表

在 vue 中，我们可以通过 v-if 和 v-show 来实现动态权限的渲染列表。

```html
<div v-if="hasPermission">
  <div>
    <h1>Hello</h1>
  </div>
</div>
```

### vue 用的哪种设计模式

1. 工厂模式 - 传入参数即可创建实例
   虚拟 DOM 根据参数的不同返回基础标签的 Vnode 和组件 Vnode

2. 单例模式 - 整个程序有且仅有一个实例
   vuex 和 vue-router 的插件注册方法 install 判断如果系统存在实例就直接返回掉

3. 发布-订阅模式 (vue 事件机制) 发布订阅模式的核心就是一对多的关系，一个发布者发起事件，所有的订阅者都会执行

4. 观察者模式 (响应式数据原理)

5. 装饰模式: (@装饰器的用法)在不改变原对象的基础上，通过对其添加属性或方法来进行包装拓展，使得原有对象可以动态具有更多功能
   Actions 是一个装饰器，它包裹 Mutations 使之可以异步使用。对于 Store 对象，使用 Action 可以异步改变状态；不用 Actions 也能使用 Mutations 来同步改变状态；使用 Actions 也不会改变 State、Getters、Mutations 的用法、结构

6. 策略模式 策略模式指对象有某个行为,但是在不同的场景中,该行为有不同的实现方案-比如选项的合并策略

### v-model 原理

> v-model 即可以作用于表单元素，又可作用于自定义组件

- 用于表单元素
  1. v-bind 绑定一个 value 属性
  2. v-on 指令给当前元素绑定 input 事件
  3. 表单元素的 value 属性变化时，触发 input 事件，触发 vue 的响应式系统，更新 data 中的值
- 用于自定义组件
  1. 在父组件内给子组件标签添加 v-model ，其实就是给子组件绑定了 value 属性
  2. 子组件内使用 prop 创建 创建 value 属性可以拿到父组件传递下来的值，名字必须是 value。
  3. 子组件内部更改 value 的时候，必须通过 ¥emit 派发一个 input 事件，并携最新的值
  4. v-model 会自动监听 input 事件，把接收到的最新的值同步赋值到 v-model 绑定的变量上
- 与 v-bind 区别
  1. v-bind 绑定的是一个静态的属性，而 v-model 绑定的是一个动态的属性
  2. v-model 绑定的数据是双向绑定的，而 v-bind 绑定的属性是单向绑定的
  3. v-model 绑定的属性是响应式的，而 v-bind 绑定的属性不是响应式的

### vue 定义自定义指令的方法有哪些

- 注册一个自定义指令有全局注册与局部注册

1. 全局注册主要是通过 Vue.directive 方法进行注册 ,Vue.directive 第一个参数是指令的名字（不需要写上 v-前缀），第二个参数可以是对象数据，也可以是一个指令函数

```js
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus(); // 页面加载完成之后自动让输入框获取到焦点的小功能
  },
});
```

2. 局部注册通过在组件 options 选项中设置 directive 属性

```js
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus() // 页面加载完成之后自动让输入框获取到焦点的小功能
    }
  }
}
```

**使用场景**

```js
// 1.设置v-throttle自定义指令
Vue.directive('throttle', {
  bind: (el, binding) => {
    let throttleTime = binding.value; // 节流时间
    if (!throttleTime) { // 用户若不设置节流时间，则默认2s
      throttleTime = 2000;
    }
    let cbFun;
    el.addEventListener('click', event => {
      if (!cbFun) { // 第一次执行
        cbFun = setTimeout(() => {
          cbFun = null;
        }, throttleTime);
      } else {
        event && event.stopImmediatePropagation();
      }
    }, true);
  },
});
// 2.为button标签设置v-throttle自定义指令
<button @click="sayHello" v-throttle>提交</button>
```

```js
import { Message } from 'ant-design-vue';

const vCopy = {
  //
  /*
    bind 钩子函数，第一次绑定时调用，可以在这里做初始化设置
    el: 作用的 dom 对象
    value: 传给指令的值，也就是我们要 copy 的值
  */
  bind(el, { value }) {
    el.$value = value; // 用一个全局属性来存传进来的值，因为这个值在别的钩子函数里还会用到
    el.handler = () => {
      if (!el.$value) {
        // 值为空的时候，给出提示，我这里的提示是用的 ant-design-vue 的提示，你们随意
        Message.warning('无复制内容');
        return;
      }
      // 动态创建 textarea 标签
      const textarea = document.createElement('textarea');
      // 将该 textarea 设为 readonly 防止 iOS 下自动唤起键盘，同时将 textarea 移出可视区域
      textarea.readOnly = 'readonly';
      textarea.style.position = 'absolute';
      textarea.style.left = '-9999px';
      // 将要 copy 的值赋给 textarea 标签的 value 属性
      textarea.value = el.$value;
      // 将 textarea 插入到 body 中
      document.body.appendChild(textarea);
      // 选中值并复制
      textarea.select();
      // textarea.setSelectionRange(0, textarea.value.length);
      const result = document.execCommand('Copy');
      if (result) {
        Message.success('复制成功');
      }
      document.body.removeChild(textarea);
    };
    // 绑定点击事件，就是所谓的一键 copy 啦el.addEventListener('click', el.handler);
  },
  // 当传进来的值更新的时候触发
  componentUpdated(el, { value }) {
    el.$value = value;
  },
  // 指令与元素解绑的时候，移除事件绑定
  unbind(el) {
    el.removeEventListener('click', el.handler);
  },
};

export default vCopy;
```

### vue 中的 data 为什么是一个函数

- 因为 Vue 组件的 data 属性是一个可复用的选项，它需要在每个组件实例中都是独立的。如果将 data 属性定义为一个对象，那么所有的组件实例将共享同一个数据对象，这样会导致一个组件的数据变化会影响到其他组件的数据。

### vue 双向绑定原理

- vue 是基于 MVVM 模式实现的，所以数据的双向绑定就是通过数据绑定实现的。数据绑定分为双向绑定和单向绑定。单向绑定就是数据绑定的一种，双向绑定就是单向绑定的一种，即数据绑定的双向绑定，就是数据绑定的一种，数据绑定的单向绑定，就是数据绑定的一种。

- 过程
  new Vue()首先执行初始化，对 data 执行响应化处理，这个过程发生 Observe 中
  同时对模板执行编译，找到其中动态绑定的数据，从 data 中获取并初始化视图，这个过程发生在 Compile 中
  同时定义⼀个更新函数和 Watcher，将来对应数据变化时 Watcher 会调用更新函数
  由于 data 的某个 key 在⼀个视图中可能出现多次，所以每个 key 都需要⼀个管家 Dep 来管理多个 Watcher
  将来 data 中数据⼀旦发生变化，会首先找到对应的 Dep，通知所有 Watcher 执行更新函数

### vue 后台项目基于 RBAC 实现权限管理

> 基于角色的设计思想

1. 给员工配置角色 （一个员工可以拥有多个角色） 给角色配置权限点 （一个角色可以有多个权限点） 员工只要有了角色之后，就自动拥有了角色绑定的所有权限点
2. 菜单权限控制

- 登录 > 菜单权限数据 > 和本地的所有的动态路由数据做匹配出具 > 得到根据权限筛选之后的动态路由数据
- 添加到路由系统中 （可以根据路径标识渲染组件 **addRoutes**）
- 添加到左侧菜单渲染 （vuex 管理 + v-for 遍历）

3. 按钮权限控制

- 登录 > 按钮权限数据 > 使用按钮单独的权限标识 去权限数据里面查找 ————通过自定义指令

### 对 vue 的理解

1. vue 是一个渐进式单页面应用框架
2. 核心是 mvvm 数据驱动视图，将界面抽象成一个由数据驱动的组件树，每个组件都可以拥有自己的状态和行为
3. 特点是 响应式数据绑定 组件化开发 指令和插件系统

### mixin

它可以让多个组件共享一些代码，比如一些公共的方法、属性、生命周期钩子等。
使用：1. 在 main.js 中引入 mixin.js 文件，然后注册为全局混入。2. 在需要使用的组件中导入 mixin.js 文件，然后使用 mixin 选项来注册混入。

而组合式函数解决了 mixins 引起的所有问题(不清晰的数据来源，命名冲突等)，这也是 Vue3 中组合式 API 出现的原因之一。除此之外，组合式函数还有方便代码维护、易于逻辑复用 、改善代码结构等优点。
