## Map



1. **Map**对象是一个简单的键/值映射。任何值（包括对象和原始值）都可以用作一个键或一个值。



```js
var m = new Map();
var o = {p: "Hello World"};
m.set(o, "content")
m.get(o) // "content"
```

2. Map也可接收一个数组作为参数

```js
var map = new Map([["name", "张三"], ["title", "Author"]]);
map.size // 2
map.get("name") // "张三"
map.get("title") // "Author"
```



## WeakMap



1. **WeakMap**结构与**Map**结构基本类似，唯一的区别是**它只接受对象作为键名（null外），不接受其他类型的值作为键名**，而且键名所指向的对象，不计入垃圾回收机制。

```js
var map = new WeakMap()

// TypeError: 1 is not an object!
map.set(1, 2)
// TypeError: Invalid value used as weak map key
map.set(Symbol(), 2)
```

2. *WeakMap*的设计目的在于，**键名是对象的弱引用**（垃圾回收机制不将该引用考虑在内），所以其所对应的对象可能会被自动回收。**当对象被回收后，WeakMap自动移除对应的键值对**。

3. **典型应用是，一个对应DOM元素的WeakMap结构，当某个DOM元素被清除，其所对应的WeakMap记录就会自动被移除。**基本上，WeakMap的专用场合就是，它的键所对应的对象，可能会在将来消失。WeakMap结构有助于防止内存泄漏。
4. **WeakMap**只有四个方法可用：get()、set()、has()、delete()