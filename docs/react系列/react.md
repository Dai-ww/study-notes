### 1、react 中使用的 key 有什么作用，key 的内部原理是什么

```
key是虚拟dom的标识

当状态中的数据发生变化时，reaxt会根据 新的数据生成薪的虚拟dom，然后比较进行新旧虚拟dom的diff比较
比较规则：
旧虚拟都没中没有找到与新虚拟dom相同的key 根据数据创建新的真实dom渲染到页面上。
找到相同的key 若虚拟dom中内容没变，直接使用之前的真实dom，变了，则生成新的真实dom，替换掉页面中之前的真实dom。

用index作为key可能引发的问题：
a、若对数据进行：逆序添加，删除等破坏顺序的操作，会产生没必要的dom更新。
b、如果包含输入类型的dom 会产生错误dom更新。
```

### 2、生命周期

```
1、componentWillMount()
2、componentDidMount()
3、componentWillReceiveProps()
4、shouldComponentUpdate()
5、componentWillUpdate()
6、componentDidUpdate()
7、componentWillUnmount()
```

![Alt text](image.png)

### 和 vue 的区别

- 相同点：

1. 数据驱动视图
2. 组件化
3. 都使用了虚拟 DOM

- 不同点

1. 写法不同：react 是 jsx+inline style，也就是把 html 和 css 写在 javaScript 中。vue 是 html+css，写在 html 中。
2. 响应式原理不同：vue 依赖收集，自动优化，数据可变，react 手动优化 数据不可变，需要 setState 驱动新的 state
3. diff 算法：vue 对比节点，当节点元素相同时，但是 class 不同，认为是不同类型的元素，但是 react 认为是同类型的元素，只是修改节点属性。
4. 框架：Vue 本质是 MVVM 框架，由 MVC 发展而来，React 是前端组件化框架，由后端组件化发展而来。
