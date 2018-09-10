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

### Function.prototype.bind
