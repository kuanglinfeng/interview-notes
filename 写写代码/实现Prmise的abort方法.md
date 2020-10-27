因为**Promise 是没有中断方法的**，xhr.abort()、ajax 有自己的中断方法，axios 是基于 ajax 实现的；fetch 基于 promise，所以他的请求是无法中断的。

这也是 promise 存在的缺陷，我们可以使用 race 来自己封装中断方法



```js
function getAbortPromise(promise) {
  let abort = undefined
  const newPromise = new Promise((resolve, reject) => {
    abort = reject
  })
  const p = Promise.race([promise, newPromise])
  p.abort = abort
  return p
}

const p1 = new Promise((resolve, reject) => {
  setTimeout(() => { // 模拟一个请求，假设需要5s才能拿到数据
    resolve('成功')
  }, 5000)
})

let p2 = getAbortPromise(p1)

setTimeout(() => { // 设置阈值，如果超过3s就通知超时
  p2.abort('超时了')
}, 3000)

p2.then(data => {
  console.log('成功的结果', data)
}, reason => {
  console.log('失败的原因', reason) // 输出 ： 失败的原因 超时了
})

```



