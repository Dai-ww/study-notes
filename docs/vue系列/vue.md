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

nextTick 存在的原因是 Vue 在更新 DOM 时是异步执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部对异步队列尝试使用原生的 Promise.then、MutationObserver 和 setImmediate，如果执行环境不支持，则会采用 setTimeout(fn, 0) 代替

**步骤**：

1. 将回调函数添加到 callbacks 中等待执行
2. 将执行函数放入到宏任务(setImediate 或者 setTimeout)或者微任务(promise,mutationObserver)中
3. 事件循环到了微任务或者宏任务，执行函数依次执行 callbacks 中的回调

### vue3 相比 vue2 做了哪些改变

1. 组合式 API
2. 双向绑定的更新，vue2 的双向数据绑定是利⽤ ES5 的⼀个 API ，Object.definePropert()对数据进⾏劫持 结合 发布订阅模式的⽅式来实现的。vue3 中使⽤了 es6 的 ProxyAPI 对数据代理，通过 reactive() 函数给每⼀个对象都包⼀层 Proxy，通过 Proxy 监听属性的变化，从⽽实现对数据的监控。
3.

### 相⽐于 vue2 版本，使⽤ proxy 的优势如下

1. defineProperty 只能监听某个属性，proxy 是对整个对象进行劫持，defineProperty 监听对象时需要递归绑定监听，对新增删除属性都不能监听到（需要使用 api $set $delete 等）。
2. 可以省去 for in、闭包等内容来提升效率（直接绑定整个对象即可）
3. 可以监听数组，不⽤再去单独的对数组做特异性操作,通过 Proxy 可以直接拦截所有对象类型数据的操作，完美⽀持对数组的监听。

### keep-alive 实现原理

> keep-alive 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中；使用 keep-alive 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。

主要是根据 LRU 策略缓存组件 VNode，最后在 render 时返回子组件的 VNode。缓存渲染过程会更新 keep-alive 插槽，重新再 render 一次，从缓存中读取之前的组件 VNode 实现状态缓存。

LRU（Least recently used，最近最少使用）：策略根据数据的历史访问记录来进行淘汰数据。

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
