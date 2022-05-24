---
title: JS 继承
date: 2021-01-10 09:07:15
categories:
- [Front-end, js]
---
***背景：*** 曾一次面试，面试官上来就问JS实现继承的方式，当时没怎么搞清楚，一知半解，之后网上查过资料，这里参照红宝书结合自己的理解对这一知识点进行梳理。
### 原型链继承
***基本构想：*** 每个构造函数都有一原型对象，其实例有个内部指针（即 [[Prototype]]/\_\_proto\_\_）指向原型; 如果原型是另一个类型的实例，意味着原型本身有个内部指针指向另一个原型。
``` javascript
function A() {
    this.property = true;
}
A.prototype.getA = function() {
    return this.property;
}
function B() {
    this.bProperty = false;
}
// B 通过原型链方式继承 A 的实例属性和原型方法
// 下面赋值重写了B的原型 重写前 B.prototype.constructor = B
B.prototype = new A();
// 由于 B 的原型的constructor被重写指向A， 所以 B 对应实例的constructor属性也指向 A
let instance = new B();
// 注意：通过对象字面量添加方法会使继承失效
B.prototype = { someMethod() {} }
```
<!-- more -->
+ 问题
  - 原型中包含引用值时，造成实例共享引用值;
  - 子类型实例化时不能给构造函数传参

### 借用构造函数继承
``` javascript
function B() {
    A.call(this);
    //  ...
}
```
+ 问题
  - 要在构造函数中定义方法，因此不能重用;
  - 子类不能访问父类原型上定义的方法

### 组合继承
***基本思路：***使用原型链继承原型上的属性和方法，通过借用构造函数继承实例属性
``` javascript
function B() {
    A.call(this);
    //  ...
}
B.prototype = new A();
```
+ 问题
  - 调用两次父类构造函数

### 原型式继承
***用处：***不自定义类型可通过原型实现对象间的信息共享
``` javascript
funtion create(obj) {
    function F() {}
    F.prototype = obj;
    return new F();
}
let person = {
    name: 'Lily',
    likes: ["book", "computer"]
}
// create 是对传入person对象的一次浅复制
// 新对象的原型上既有原始值属性又有引用值属性
let anoPerson = create(person);
```
+ 问题
  - 属性中包含的引用值始终在相关对象间共享

### 寄生式继承
***基本思路：***创建一个实现继承的函数，以某种方式增强对象，然后返回这个对象（与原型式继承类似，适合主要关注对象，不在乎类型和构造函数的场景）
``` javascript
function createAno(original) {
    let clone = create(original);  // 创建新对象
    clone.sayHi = function() {};  // 增强新对象
    return clone;
}
```
+ 问题
  - 给对象添加的函数难以重用

### 寄生式组合继承
***基本思路：***通过借用构造函数继承属性，但不通过调用父类构造函数给子类原型赋值（解决组合式继承调用两次父类构造函数问题），而是使用寄生式继承来继承父类原型，然后将返回的继承了父类原型的新对象赋值给子类原型。
``` javascript
function inheritProto(fu, zi) {
    let prototype = create(fu.prototype);  // 创建父类原型副本
    prototype.constructor = zi;  // 解决由于下面一行重写原型导致的constructor丢失问题
    zi.prototype = prototype;
}
inheritProto(A, B);
```
+ 问题
  - 算是引用类型继承的最佳实践，站在巨人的肩膀上～

<i>来自《javascript高级程序设计》第四版 阅读整理</i>