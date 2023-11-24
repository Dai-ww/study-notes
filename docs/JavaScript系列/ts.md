### 说说你对 TypeScript 的理解？与 JavaScript 的区别

> ts 是 js 的超集，它相对于 js 增加了静态类型，接口，类，模块，类型注解等，可用于开发大型应用。

**区别**：

- ts 遵循 ES6 规范
- ts 对 js 进行了扩展，引入类型的概念，添加许多新的特性
- ts 代码执行需要先编译成 js

### type 和 interface 的区别

**相同点：**

1. 都可以描述 '对象' 或者 '函数'
2. 都允许拓展(extends)

**不同点：**

1. type 可以声明任何类型，inter face 只能声明对象
2. type 不能继承，interface 能继承
3. 同名 type 只能有一个，同名 interface 可以多个
4. 使用 interface 描述‘数据结构’，使用 type 描述‘类型关系’
