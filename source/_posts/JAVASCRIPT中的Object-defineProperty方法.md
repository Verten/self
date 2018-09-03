title: JAVASCRIPT 中的 Object.defineProperty 方法
date: 2018-08-18 23:03:14
categories: 技术
tags:

- 技术
- 总结

---

之前对于 Javascript 的的理解一直停留在框架的使用上面，确实也需要理一理 Javascript 的一些基础知识了。
本篇就总结下个人理解的 Object 对象上面的 defineProperty 方法。

### defineProperty 方法不在 Object 的原型上面，而是在 Object 对象上面

这也就意味着，使用它的方式是*Object.defineProperty(obj, prop, descriptor)*

### defineProperty 方法的参数

- 第一个参数*obj*
  - 需要在其上面定义属性的对象
- 第二个参数*prop*
  - 需要定义的属性名称
- 第三个参数*descriptor*
  - 将要被定义的属性的描述符
  - 其值为 configurable, writable, enumerable, value, get, set

### 这里主要说一下第三个参数的作用，**描述符**

描述符分为**数据描述符**和**存取描述符**

**两者**都能都如下的可选键值

- configurable
  - 当且仅当该属性的 configurable 为 true 时，该属性*描述符*才能被改变，同时该属性也能从对应的对象上面删除。**默认为 false**
  - 如果该值为false的话，则试图改变除了writable之外的键值（并且writable也只能从true变为false），都会出错。除非新值和当前值相同。
  ```
  let obj = {}
  Object.defineProperty(obj, 'name', {
    value: 'binhong',
    writable: true,
    enumerable: true,
    configurable: false,
  }) // {name: "binhong"}
  //重新定义
  Object.defineProperty(obj, 'name', {
    value: 'binhong',
    writable: true,
    enumerable: false,
    configurable: false,
  }) // Uncaught TypeError: Cannot redefine property: name

  ```
- enumerable

  - 决定改属性能否被枚举，即通过*for...in*或者*for...of*所遍历。**默认值 false**

  ```
  let obj = {}
  Object.defineProperty(obj, 'name', {value: 'binhong', writable: true}) //{name: "binhong"}
  for(let o in obj){console.info(o)} // undefined
  Object.defineProperty(obj, 'age', {value: 28, writable: true, enumerable: true}) //{age: 28, name: "binhong"}
  for(let o in obj){console.info(o)} // age
  Object.keys(obj) //["age"]
  ```

**数据描述符**还能有下面额外的可选键值

- value
  - 该属性的值。可以是任何有效的 javascript 值。**默认为 undefined**
- writable

  - 当且仅当为 true 的时候，该属性的值才能改变。**默认值为 false**

  ```
  let obj = {}
  Object.defineProperty(obj, 'name', {value: 'binhong', writable: false}) //{name: "binhong"}
  obj.name = 'no_name'
  console.info(obj) // {name: "binhong"}
  ```

**存取描述符**还能有下面额外的可选键值

- get
  - 给需要添加的属性提供 getter 方法。当访问该属性的时候，会调用该方法，方法执行时没有参数传入，但是会有*this*对象。**默认值为 undefined**
- set
  - 给需要添加的属性提供 setter 方法。当给该属性赋值的时候，会调用该方法，方法执行时传递唯一参数，即改属性的新的值。**默认值为 undefined**

下面这张表格列出了数据描述符和存取描述符同时能拥有的键值

| -   | configurable | enumerable | writable | value | get | set |
| --- | :----------: | :--------: | :------: | :---: |:-: | :-: |
| 数据描述符 |     Yes      |    Yes     |   Yes    |  Yes  | No  | No  |
| 存取描述符 |     Yes      |    Yes     |    No    |  No   | Yes | Yes |

**如果一个属性的描述符不具有任意一个value，writable，set和get，那么它将被认为是一个数据描述符。
一个描述符如果同时拥有（value或writable）和（get或set），则会产生一个异常。**

```
let o = {}
Object.defineProperty(o, 'name', {
  value: 'binhong',
  get: function(){
    return this.name
  }
})
// Uncaught TypeError: Invalid property descriptor. Cannot both specify accessors and a value or writable attribute
```

下面有两个例子写了基本的**数据描述符**和**存取描述符**
```
// 添加一个数据描述符
let obj = {}
Object.defineProperty(obj, 'name', {
  configurable: true,
  enumerable: true,
  writable: true,
  value: 'binhong'
}) // {name: "binhong"}

//添加一个存取描述符
let anotherObj = {}
Object.defineProperty(anotherObj, 'name', {
  configurable: true,
  enumerable: true,
  get: function(){
    return `Hello, my name is: ${this._name}` 
  },
  set: function(newValue){
    this._name = newValue // 使用一个临时变量来存放
  }
})

```