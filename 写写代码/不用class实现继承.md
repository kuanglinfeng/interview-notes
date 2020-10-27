```js
function Father(lastName) {
  this.lastName = lastName
}

Father.prototype.walk = function () {
  console.log('walk in park')
}

function Son(firstName, lastName) {
  this.firstName = firstName
  Father.call(this, lastName)
}

const F = function () {}
F.prototype = Father.prototype
Son.prototype = new F()
Son.prototype.constructor = Son

Son.prototype.say = function () {
  console.log(`my name is ${this.firstName} ${this.lastName}`)
}
```

