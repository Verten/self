title: JAVASCRIPT 基础
date: 2018-08-20 11:04:15
categories: 技术
tags:

- 技术
- 总结

---

### JAVASCRIPT 中的变量提升

MDN 中说明

> Only declarations are hoisted

所以看看下面的代码

```
console.info(a) //undefined
var a = 1
```

```
a = 2
console.info(a) //2
var a
```

我们来拆解一下
第一种写法等同于

```
console.info(a)
var a
a = 1
```

根据变量提升的规则

```
var a
console.info(a)
a = 1
```

所以，打印出 undefined 也就通俗易懂了

第二种写法根据变量提升的原则

```
var a
a = 2
console.info(a)
```

所以也就打印出 2

变量提升的规则同样适用于方法声明

```
myFunc() // Error, myFunc is not a function!
var myFunc = function(){
  console.info('my function')
}
```

```
helloWorld() //Hello World!
function helloWorld(){
  console.info('Hello World!')
}
```

```
function goodMorning(){
  console.info('Good Morning!')
}
goodMorning() // Good Morning!
```

有时候变量提升会让我们困惑，则可以使用 ES6 新引入的*let*或者*const*关键字来声明变量，*let*和*const*声明的变量不存在提升

```
tmp = 2 // ReferenceError: can't access lexical declaration `tmp' before initialization
console.info(tmp)
let tmp
```
