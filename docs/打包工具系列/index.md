### 说说对 webpack 的理解

> webpack 是一个用于 javascript 应该程序的静态模块打包工具

- 在编码上：对资源进行处理，比如压缩图片，将 scss 转成 css，将 ES6 语法转成 ES5 语法，将 TypeScript 转成 JavaScript 等等操作，提高效率，解决浏览器兼容问题。
- 模块整合能力，将 js，css，图片等打包成 bundle.js 文件，提高性能和可维护性，解决浏览器频繁请求文件的问题。
- 支持不同种类的前端模块类型，统一的模块化方案，所有资源文件的加载都可以通过代码控制

![Alt text](image.png)

### webpack vite rollup

#### webpack vite rollup 适用场景

rollup 更适合打包库 （不支持热更新）
webpack 更适合打包项目（打包速度慢）
vite 基于 rollup 实现了热更新也适合打包项目（生态不及 webpack，加载器、插件不够丰富）

#### webpack code spliting 目的是什么？怎么实现的？

- Code Splitting 是 webpack 的一个重要特性，他允许你将代码打包生成多个 bundle。
- 对多页应用来说，它是必须的，因为必须要配置多个入口生成多个 bundle；
- 对于单页应用来说，如果只打包成一个 bundle 可能体积很大，导致无法利用浏览器并行下载的能力，且白屏时间长，Code Splitting 能够将代码分割，实现按需加载或并行加载多个 bundle，可利用并发下载能力，减少首次访问白屏时间，可以只上线必要的文件。

**实现**：

1. 多 entry 方式 指定多个打包入口
2. 公共提取 将公共模块提取出来生成一个 bundle
3. 动态加载

#### vite 为什么比 webpack 更快

**Vite 有如下特点**：

1. 快速的冷启动: No Bundle + esbuild 预构建
2. 即时的模块热更新: 基于 ESM 的 HMR，同时利用浏览器缓存策略提升速度
3. 真正的按需加载: 利用浏览器 ESM 支持，实现真正的按需加载

Vite 其核心原理是利用浏览器现在已经支持 ES6 的 import,碰见 import 就会发送一个 HTTP 请求去加载文件。Vite 启动一个 connect 服务器拦截这些请求，并在后端进行相应的处理将项目中使用的文件通过简单的分解与整合，然后再以 ESM 格式返回返回给浏览器。整个过程中没有对文件进行打包编译。

### 如何选择合适的打包工具，Webpack 和 Vite 的特点和使用场景。

### 打包工具代码优化技巧：如代码分割、懒加载、Tree Shaking 等。
