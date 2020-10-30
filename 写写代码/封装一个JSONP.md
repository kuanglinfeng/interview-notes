## 服务端代码

```js
const Express = require('express')

const app = Express()

app.get('/api', (request, response) => {
  const callback = request.query.callbackName
 	// 将客户端的count进行加一 然后返回给客户端
  const count = parseInt(request.query.count) + 1
  const result = {count}
  response.send(`${callback}(${JSON.stringify(result)})`)
})

app.listen(8080, () => console.log('http://localhost:8080'))
```



## 浏览器代码

```html
<body>
<script>
  // 使用Promise封装
  function jsonp({url, params, callbackName}) {
    return new Promise((resolve, reject) => {

      let script = document.createElement('script')
      params = {...params, callbackName}
      const arr = []

      for (let prop in params) {
        arr.push(`${prop}=${params[prop]}`)
      }

      script.src = `${url}?${arr.join('&')}`
      document.body.appendChild(script)

      window[callbackName] = function (data) {
        resolve(data)
        document.body.removeChild(script)
      }
      script.onerror = function (error) {
        reject(error)
      }
    })
  }
  // 测试
  jsonp({
    url: 'http://localhost:8080/api',
    params: { count: 0 },
    callbackName: 'fn'
  }).then(
      data => { console.log(data)}, // 输出 1
      reason => { console.log('reason', reason)}
    )
</script>
</body>
```



