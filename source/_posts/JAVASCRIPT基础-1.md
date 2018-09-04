title: JAVASCRIPT 基础-1
date: 2018-09-04 23:38:17
categories: 技术
tags:

- 技术
- 总结
- 基础

---

在 javascript 中，最常用的就是 Object 对象了，MDN 中有这么一段话

> 几乎所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例。

### 如何在 javascript 中如何创建一个 Object 对象

- **使用字面量**

```
let person = {
  name: 'bin hong',
  age: 28
}
let home = {} // 创建一个空的object对象
```

- **使用构造函数**

```
function Person(){
  this.name = 'bing hong'
  this.age = 28
  let location = 'guang dong' // 没有this关键字则是非成员变量
}
let person = new Person() // 使用new关键字
console.info(person) // person {name: "bing hong", age: 28}
```

- **使用 Object 对象**

```
let person = new Object()
person.name = 'bin hong'
person.age = 28
console.info(person) // {name: "bin hong", age: 28}
```

常用的就上面三种，当然还有下面几种也是能够创建，但是一般不会这么使用

- 使用原型 prototype

```
function Person(){}
Person.prototype.name = 'bin hong'
Person.prototype.age = 28
let person = new Person()
console.info(person) // Person {}
console.info(person.name) // bin hong
console.info(person.age) // 28
```

这里可能有人就会有疑问了，我这个创建出来的 _person_ 明明就是空的。为啥还能拿到*name*和*age*属性呢？
没错，确实是空的，因为我们把属性设置在了它的原型上面，javascript 中的查找会顺着当前对象->当前对象的原型->父类对象->父类原型这样的一级一级的往上的查找顺序来查找，直到找到为止，找不到则返回 _undefined_

原型的问题，我们可以放到下一篇来讲。
