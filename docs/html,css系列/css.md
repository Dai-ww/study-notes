### 1.垂直水平居中的几种方式

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

### 2、width:100%与width:auto 区别

```
都是由最近父级决定，不过auto不会超出父级，100%的padding，border写多了容易超出父级，一般都是用auto，不写默认就是auto
width:auto;会包含padding,border,margin的宽度，width:100%;在content-box下是不会包含这三个的宽度的
```

### 3、介绍下 BFC 及其应用

```
BFC 就是块级格式上下文，是页面盒模型布局中的一种 CSS 渲染模式，相当于一个独立的容器，里面的元素和外部的元素相互不影响。创建 BFC 的方式有：
html 根元素
float 浮动
绝对定位
overflow 不为 visiable
display 为表格布局或者弹性布局

BFC原理
1.就是在BFC这个元素的垂直方面边距(margin)会发生重叠
2.BFC的区域不会与浮动元素的box重叠
3.BFC在页面中是一个独立的容器，外面的元素不会影响它里面的元素
4.计算BFC高度的时候，浮动元素也会参与计算

BFC 主要的作用是：
清除浮动
防止同一 BFC 容器中的相邻元素间的外边距重叠问题
```

