title: 'JAVASCRIPT 中的 call,apply,bind'
date: 2018-09-10 23:51:55
categories: 技术
tags:

- 技术
- 总结

---

Javascript 中有几个有趣的方法，它们位于 Function 对象原型上面，分别是：

- Function.prototype.call
- Function.prototype.apply
- Function.prototype.bind

今天我们就说说它们的用法和区别。

### Function.prototype.call

MDN 上面是这样定义它的

> fun.call(thisArg, arg1, arg2, ...)

第一个参数是传递给 _fun_ 的 _this_ 对象，后面的参数则是 _function_ 所接受的参数列表

```
Math.max.call(null, 5, 6, 7, 1) // 7
Math.max.call(undefined, 2, 3, 4, 1) // 4
```

因为 _max_ 函数不需要 _this_ 对象，所以一个参数可以传递 _null_ 或者 _undefined_

```
  function helloWorld(){
    console.info(`Hello, My name is: ${this.name}! arguments is: ${Array.prototype.slice.call(arguments)}`)
  }

  helloWorld.call({name: 'bin hong'}) // Hello, My name is: bin hong! arguments is:
  helloWorld.call({name: 'Tome'}, 1, 2, 3, 4) // Hello, My name is: Tome! arguments is: 1,2,3,4
```

上面例子可以看得出，我们能够通过使用 _call_ 来动态的指定 _function_ 中的 _this_ 的真正对象。

上面的例子还能够得知，_arguments_ 这个保留字就是在 _function_ 中拿到参数列表的属性，同时使用 _Array_ 对象原型上面的 _slice_ 方法能够将其转化为数组。

```
const args = [1, 2, 3, 4]
Array.prototype.slice.call(args)
// 等同于
args.slice()
Array.prototype.slice.call(args, -1)
// 等同于
args.slice(-1)
```

使用 _call_ 和 _apply_ 来调用 javascript 对象原型方法需要理解其第一个参数 _this_ 的含义，如同上面例子一样，我们一般使用 _slice_ 的方法就是在一个 _array_ 对象上面使用，就是这个 _array_ 就是调用 _slice_ 的 _this_ 对象，所以使用 _call_ 或者 _apply_ 的时候，第一个参数就是 _array_ 对象，就是需要使用 _slice_ 的对象。后续的参数，才是传递给 _slice_ 的参数。

### Function.prototype.apply

MDN 上面是这样定义它的

> func.apply(thisArg, [argsArray])

和 Function.prototype.call 类似，第一个参数是传递给*func*的*this*对象，但后面参数则是*func*的参数数组

```
Math.max.apply(null, [5, 6, 7, 1]) // 7
Math.max.apply(undefined, [2, 3, 4, 1]) // 4
```

Function.prototype.apply 也能够动态的改变*func*中的*this*对象

所以能够使用 call 的地方也能够使用 apply, 唯一需要改变的是把**参数列表**换成**参数数组**

```
const args = [1, 2, 3, 4]
Array.prototype.slice.apply(args)
// 等同于
args.slice()
Array.prototype.slice.apply(args, [-1])
// 等同于
args.slice(-1)
```

### Function.prototype.bind

MDN 上面这样定义它的

> fun.bind(thisArg[, arg1[, arg2[, ...]]])

它的第一个参数是传递给*func*的*this*对象，后面的参数则是传递给*func*的参数列表

```
const obj = {
  name: 'Object A',
  getName: function(){
    return this.name
  }
}
const unboundGetName = obj.getName
console.info(unboundGetName()) // this is means window, output is undefined

const boundGetName = unboundGetName.bind(obj)
console.info(boundGetName()) // this is bind to obj, output is Object A
```

_bind_ 还有一个比较有意思的用法则是可以使一个函数拥有预设的一些初始参数。

```
  function addSomeNumber(){
    return Array.prototype.slice.call(arguments).reduce((acc, value) => acc + value, 0)
  }

  addSomeNumber(1,2,3,4) // output: 10

  const alwaysAddTen = addSomeNumber.bind(null, 10)
  alwaysAddTen() // output: 10
  alwaysAddTen(5, 6, 7, 8, 9) // output: 45

  // 减少方法的参数
  function addNumber(number1, number2, number3){
    return number1 + number2 + number3
  }

  const addNewNumber = addNumber.bind(null, 1, 2)
  addNewNumber(3) // output: 6
  addNewNumber(10) // output: 13
```

从上面的例子可以看到，从*bind*的第二个参数开始，都是传递给*func*的参数，所以我们可以通过*bind*方法，给*func*默认指定一些参数

### 总结

*Function.prototype.call*和*Function.prototype.apply* 属于间接执行某个方法的另一种途径，同时它们拥有改变方法内部的*this*的对象的能力，也就是说，如果我们想改变某个方法内部的*this*的指向，则可以使用*call*或者*apply*。

*Function.prototype.bind*则是直接绑定方法的*this*指向，如果 bind 有两个以上的参数，那么后面的参数则作为方法的初始参数。因为*bind*完之后，方法并没有执行，所以还需要手动调用一次*bind*返回之后的函数
