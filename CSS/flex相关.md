1. **flex-basis、flex-grow、flex-shrink**

```css
.container {
   width: 600px;
   display: flex;
}
.a {
  flex: 1 0 400px;
}
.b {
  flex: 2 0 100px;
}

<div class="container">
    <div class="a">a</div>
    <div class="b">b</div>
</div>
```

问以上代码a和b的宽度分别是多少？



公式：某一项的宽度 = flex-basis + (父元素宽度 - 子元素占用的总的flex-basis) * 某一项的flex-grow / 其它项flex-grow之和



a的宽度：400px + (600 - 400 - 100) * 1 / (1 + 2)

b的宽度：100px + (600 - 400 - 100) * 2 / (1 + 2)

