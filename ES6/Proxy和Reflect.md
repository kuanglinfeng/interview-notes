## Reflect （反射）

Reflect是一个内置的JS对象，它提供了一系列方法，可以让开发者通过调用这些方法，访问一些JS底层功能

### 作用

使用Reflect可以实现诸如 属性的赋值与取值、调用普通函数、调用构造函数、判断属性是否存在于对象中等功能

### 为什么需要Reflect

ES5提出过一个重要的理念：减少魔法，让代码更纯粹

ES6进一步贯彻了这种理念，它认为，对象属性内存的控制，原型链的修改，函数的调用等等，这些都属于底层控制，属于一种魔法，因此需要把这些功能提取出来，形成一个正常的API，并高度聚合到某个对象中，于是就造就了Reflect对象

### 提供的API

- Reflect.set(target, propertyKey, value)：设置对象target的属性propertyKey的值为value
- Reflect.get(target, propertyKey)：读取对象target的属性propertyKey
- Reflect.apply(target, thisArgument, argumentsList)：调用一个指定的函数，并绑定this和参数列表
- Reflect.deleteProperty(target, propertyKey)：删除一个对象的某个属性
- Reflect.defineProperty(target, propertyKey, attributes)：类似于Object.defineProperty，不同的是如果配置出现问题，返回false，而不是报错
- Reflect.construct(target, argumentsList)：用构造函数的方式创建一个对象
- Reflect.has(target, propertyKey)：判断一个对象是否拥有某个属性

## Proxy （代理）

代理：提供了修改底层实现的方式

```js
// 代理一个目标对象
// target：目标对象
// handler：一个普通对象，其中可以重写target的底层实现
// 返回值：一个代理对象
new Proxy(target, handler)
```

使用：

```html
<script>
  const o = {
    a: 1,
  }
  const proxy = new Proxy(o, {
    set(target, propertyKey, value) {
      Reflect.set(target, propertyKey, value)
    },
    get(target, propertyKey) {
      if (Reflect.has(target, propertyKey)) {
        return Reflect.get(target, propertyKey)
      } else {
        return -1
      }
    },
    has(target, propertyKey) {
      return false
    }
  })
  // 1 -1
  console.log(proxy.a, proxy.b)
  // false
  console.log("a" in proxy)
</script>
```

## 实现观察者模式

1. Object.defineProperty实现

```html
<div id="container"></div>
<script>
  
  function observe(target) {
    const div = document.getElementById('container')
    const observer = {}
    const props = Object.keys(target)
    for (const prop of props) {
      Object.defineProperty(observer, prop, {
        get() {
          return target[prop]
        },
        set(val) {
          target[prop] = val
          render()
        },
        enumerable: true
      })
    }
    render()
    function render() {
      let html = ''
      for (const prop of Object.keys(observer)) {
        html += `
          <p><span>${prop}</span><span>${observer[prop]}</span></p>
        `
      }
      div.innerHTML = html
    }
    return observer
  }

  const o = {a: 1, b: 2}

  const observer = observe(o)

  observer.a = 3 // 页面上的 a值变为3 了
  observer.c = 999 // 无法监听到新增的属性

  // Object.defineProperty的缺点：
  // 1. 创建了两个对象，造成空间浪费
  // 2. 无法监听到新增的属性
</script>
```


2. Proxy & Reflect 实现

```html
<div id="container"></div>
<script>

  function observe(target) {
    const div = document.getElementById('container')
    const proxy = new Proxy(target, {
      set(target, propertyKey, value) {
        Reflect.set(target, propertyKey, value)
        render()
      },
      get(target, propertyKey) {
        return Reflect.get(target, propertyKey)
      }
    })

    render()
    function render() {
      let html = ''
      for (const prop of Object.keys(target)) {
        html += `
          <p><span>${prop}</span><span>${target[prop]}</span></p>
        `
      }
      div.innerHTML = html
    }
    return proxy
  }

  const o = {a: 1, b: 2}
  const observer = observe(o)

  observer.a = 3 // 页面上的 a值变为3 了
  observer.c = 999 // 能监听到新增的属性了！！！
</script>
```