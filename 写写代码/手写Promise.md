```js
const MyPromise = (() => {

  const pending = 'pending'
  const resolved = 'resolved'
  const rejected = 'rejected'
  const promiseStatus = Symbol('promiseStatus')
  const promiseValue = Symbol('promiseValue')
  const thenables = Symbol('thenables')
  const catchables = Symbol('catchables')

  return class MyPromise {

    constructor(executor) {
      this[promiseStatus] = pending
      this[promiseValue] = undefined
      this[thenables] = []
      this[catchables] = []
      const resolve = data => {
        if (this[promiseStatus] === pending) {
          this[promiseStatus] = resolved
          this[promiseValue] = data
          this[thenables].forEach(thenable => thenable())
        }
      }
      const reject = reason => {
        if (this[promiseStatus] === pending) {
          this[promiseStatus] = rejected
          this[promiseValue] = reason
          this[catchables].forEach(catchable => catchable())
        }
      }
      try {
        executor(resolve, reject)
      } catch (error) {
        reject(error)
      }
    }

    then(thenable, catchable) {
      if (typeof thenable !== 'function') return
      if (this[promiseStatus] === resolved) {
        setTimeout(() => {
          thenable(this[promiseValue])
        }, 0)
      } else {
        this[thenables].push(() => {
          setTimeout(() => {
            thenable(this[promiseValue])
          }, 0)
        })
      }
      this.catch(catchable)
    }

    catch(catchable) {
      if (typeof catchable !== 'function') return
      if (this[promiseStatus] === rejected) {
        setTimeout(() => {
          catchable(this[promiseValue])
        }, 0)
      } else {
        this[catchables].push(() => {
          setTimeout(() => {
            catchable(this[promiseValue])
          }, 0)
        })
      }
    }

    static resolve(data) {
      if (data instanceof  MyPromise) return data
      return new Promise((resolve, reject) => {
        resolve(data)
      })
    }

    static reject(reason) {
      if (reason instanceof  MyPromise) return reason
      return new Promise((resolve, reject) => {
        reject(reason)
      })
    }

    static all(promiseList) {
      return new Promise((resolve, reject) => {
        const states = promiseList.map(promise => {
          const state = {data: undefined, isResolved: false}

          promise.then(data => {
            state.data = data
            state.isResolved = true
            if (states.filter(state => !state.isResolved)) {
              resolve(states.map(state => state.data))
            }
          }, reason => {reject(reason)})

          return state
        })
      })
    }

    static race(promiseList) {
      return new Promise((resolve, reject) => {
        promiseList.forEach(promise => {
          promise.then(data => {
            resolve(data)
          }, reason => {
            reject(reason)
          })
        })
      })
    }

    // 由于我们手写的Promise没有链式调用功能，所以这里用原生的Promise来实现
    finally(fn) {
      return this.then(data => {
        return Promise.resolve(fn()).then(() => data)
      }, reason => {
        return Promise.resolve(fn()).then(() => {
          throw reason
        })
      })
    }

  }

})()
```

