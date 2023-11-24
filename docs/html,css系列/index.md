### 浏览器渲染原理

当浏览器的网络线程收到 html 后，会产生一个渲染任务，并将其传递给渲染线程的消息队列，在事件循环的作用下，渲染主线程取出任务，开启渲染流程。
整个流程分为多个阶段

html 解析 -> 样式计算 -> 布局 -> 分层 -> 绘制 -> 分块 -> 光栅化 -> 画

- html 解析：
  生成 Dom 树，cssom 树。
- 样式计算：
  遍历节点，得到一颗带有样式的 Dom 树，在这一个过程中，很多预设值会变成绝对值，相对单位变成绝对单位。
- 布局：
  依次遍历 Dom 树的每一个节点，计算每个节点的几何信息，例如：宽高、相对包含块的位置
- 分层：
  主线程会使用一套复杂的策略对整个布局树中进行分层（使用 will-change 属性可将元素设置为单独的一层）
- 绘制：
  主线程会为每个层单独产生指令集，用于描述这一层的内容该如何画出来，完成后将每个图层的信息提交给合成线程。
- 光栅化：
  合成线程会将块信息交给 GPU 进程，快速完成光栅化，结果就是一块一块的位图
- 画：
  合成线程拿到每个层，每个块的位图后，生成一个个指引信息，然后交给 GPU 线程，由 GPU 线程产生系统调用，完成最终的屏幕成像。

**总结**：开启渲染线程后，开始解析 html 生成 dom 树，cssom 树，然后进行样式计算，将生成的 cssom 树和 dom 树计算得到最终的一个样式，再依次遍历每个节点，计算出每个节点的几何信息，然后将页面分成几个图层，每个层面单独进行绘制（提高渲染效率）,然后产生绘制指令，讲指令交给合成线程进行分块后对每一个小块进行光栅化（像素点），最后交给 GPU 线程画出来。

**扩展**：我们通过了解上面的流程可以引伸出下列问题

- 什么是 reflow

  - reflow 的本质是重新计算 layout （一般是 width height padding margin 等改动造成）
  - 为了避免连续多次的操作导致布局树反复计算，浏览器会合并这些操作，当 js 执行完成后才统一计算，改动属性造成的回流是异步完成的。由于这个原因，当 js 获取获取布局属性时，了能会导致获取到的不是最新布局信息，浏览器在权衡利弊下，座钟决定获取属性会立即 reflow。（避免反复多次获取布局属性）

- 什么是 refaint
  - refaint 的本质是重新根据分层信息绘制指令（一般 可见样式变动会导致）
  - reflow 一定会导致 refaint

### 导致页面白屏的原因及解决

**原因**

- 在渲染页面的时候需要加载很大的 js 文件，在 js 未解析完成之前无法展示页面，或网络延迟，js 加载延迟。
- url 无效或有中文字符
- 缓存原因：在客户端会默认缓存 index.html 文件，但是由于 vue 打包生成的 css/js 都是哈希值，跟上次文件名不一样，因此会出现找不到文件的情况从而导致白屏

**解决**

- 路由懒加载
- cdn 资源优化
- 缓存（静态资源缓存）
- 使用骨架屏 loading 状态

### SPA 首屏加载慢如何解决

- 动态懒加载所需插件
- 使用 CDN 资源；vue-ssr
- 减小入口文件积
- 静态资源本地缓存
- UI 框架按需加载
- 图片资源的压缩
- 组件重复打包
- 开启 GZip 压缩

### 实现响应式布局的几种方法

- 媒体查询
- flex 布局
- grid 布局
- 相对单位和百分比
- 一些 UI 组件自带的布局

### 垂直水平居中的几种方式

```
  <style>
      .parent {
        margin: auto;
        width: 500px;
        height: 500px;
        background: pink;
        /* 1、flex 布局 */
        /* display: flex;
        align-items: center;
        justify-content: center;
        或者父元素设置为flex 子元素设置 margin:auto
        */
        position: relative;
      }
      .son {
        width: 100px;
        height: 100px;
        background: rgb(24, 136, 44);
        position: absolute;
        /* 2、定位+ transform */
        /* top: 50%;
        left: 50%;
        transform: translate(-50%, -50%); */
        /* 3、定位+ margin auto */
        /* left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        margin: auto; */
        /* 4、定位+ margin负边距 */
        /* top: 50%;
        left: 50%;
        margin-left: -50px;
        margin-top: -50px; */
      }
    </style>
```

### width:100%与 width:auto 区

- 都是由最近父级决定，不过 auto 不会超出父级，100%的 padding，border 写多了容易超出父级，一般都是用 auto，不写默认就是 auto
  width:auto;会包含 padding,border,margin 的宽度，width:100%;在 content-box 下是不会包含这三个的宽度的

### BFC 及其应用

- BFC 就是块级格式上下文，是页面盒模型布局中的一种 CSS 渲染模式，相当于一个独立的容器，里面的元素和外部的元素相互不影响。创建 BFC 的方式有：

1. html 根元素
2. float 浮动
3. 绝对定位
4. overflow 不为 visiable
5. display 为表格布局或者弹性布局

- BFC 原理

1. 就是在 BFC 这个元素的垂直方面边距(margin)会发生重叠
2. BFC 的区域不会与浮动元素的 box 重叠
3. BFC 在页面中是一个独立的容器，外面的元素不会影响它里面的元素
4. 计算 BFC 高度的时候，浮动元素也会参与计算

- BFC 主要的作用是：

1. 清除浮动
2. 防止同一 BFC 容器中的相邻元素间的外边距重叠问题

### 盒模型

**区别**：这两种盒子模型最主要的区别就是 width 的包含范围

- 标准盒模型 : width = content
- IE 盒模型 : width = content + padding + border

**如何设置这两种模型**

- 标准模型: box-sizing:content-box;
- IE 模型: box-sizing:border-box;

### cookie、localStorage、sessionStorage

1. 数据存储方式：

- cookie 是网站为了标示用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）
- cookie 数据始终在同源的 http 请求中携带（即使不需要），记会在浏览器和服务器间来回传递
- sessionStorage 和 localStorage 不会自动把数据发给服务器，仅在本地保存

2. 存储大小：

- cookie 数据大小不能超过 4k
- sessionStorage 和 localStorage 虽然也有存储大小的限制，但比 cookie 大得多，可以达到 5M 或更大

3. 有期时间：

- localStorage 存储持久数据，浏览器关闭后数据不丢失除非主动删除数据
- sessionStorage 数据在当前浏览器窗口关闭后自动删除
- cookie 设置的 cookie 过期时间之前一直有效，即使窗口或浏览器关闭

**cookie 的安全性问题**

1. http-only , 作用是: 不能通过 JS 访问 Cookie，减少 XSS 攻击
2. same-site , 作用是: 规定浏览器不能在跨域请求中携带 Cookie，减少 CSRF 攻击
