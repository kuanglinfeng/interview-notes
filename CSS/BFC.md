## 什么是BFC



先思考一个问题：



请问：什么是色情？



联邦最高法院大法官斯图尔特更有一句名言



我不知道什么是色情，不过，我看了之后，就能知道



类似地：



我不知道什么是 BFC 但是你写出样式，我就知道这是不是 BFC

BFC 就是这样的东西（堆叠上下文也是）



它没有定义

它只有特性/功能：BFC全称块级格式上下文，BFC元素特性表现原则就是，内部子元素再怎么翻江倒海都不会影响外部的元素。



## 如何创建BFC



\- 根元素或其它包含它的元素

\- 浮动元素 (元素的 float 不是 none)

\- 绝对定位元素 (元素具有 position 为 absolute 或 fixed)

\- 内联块 (元素具有 display: inline-block)

\- 表格单元格 (元素具有 display: table-cell，HTML表格单元格默认属性)

\- 表格标题 (元素具有 display: table-caption, HTML表格标题默认属性)

\- 具有overflow 且值不是 visible 的块元素，

\- display: flow-root

\- column-span: all 应当总是会创建一个新的格式化上下文，即便具有column-span: all 的元素并不被包裹在一个多列容器中。



## BFC的功能



1. 爸爸管住儿子（用BFC包住浮动元素）



链接：https://codepen.io/kuanglinfeng/pen/BayJwZb



2. 兄弟之间划清界限（用float+div做左右自适应布局）



链接：https://codepen.io/kuanglinfeng/pen/VwYyMVG