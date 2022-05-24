---
title: 红宝书系列（二）
date: 2021-01-14 08:56:30
categories:
- [Front-end, js]
---
## 基础5_6_10_8
### chapter 5 基本引用类型
**引用类型是把数据和功能组织到一起的结构**  
**1. Date**  
将日期保存为自协调世界时(UTC, Universal Time Coordinated)时间1970-01-01零时至今所经过的毫秒数。
``` javascript
let now = new Date();  // 保存当前日期和时间
let someDate = new Date(Date.parse("5/23/2019"));  // 创建一个表示2019-05-23的日期对象
let allFives = new Date(Date.UTC(2005, 4, 5, 17, 55, 55));  // GMT时间
let someDate1 = new Date("5/23/2019")  // 得到和someDate一样的日期对象，Date在后台调用Date.parse()
let allFives1 = new Date(2005, 4, 5, 17, 55, 55);  // 得到本地时间而非GMT时间，Date隐式调用Date.UTC()
```
<!-- more -->
Date.parse() 接收一个表示日期的字符串参数，返回表示该日期的毫秒数
Date.UTC(年,月,日,时,分,秒,毫秒) 其中月以0为起点 返回该日期的毫秒表示 
Date.now() 返回方法执行时日期和时间的毫秒数
+ 继承的方法 均被重写且返回值不同
  - toLocaleString() 返回与浏览器运行的本地环境一致的日期和时间，不包含时区信息
  - toString() 返回带时区信息的日期和时间
  - valueOf() 返回日期的毫秒表示
+ 日期格式化方法
  - toDateString()
  - toTimeString()
  - toLocaleDateString()
  - toLocaleTimeString()
  - toUTCString()
  这些方法都会因浏览器而异，因此不能用于在用户界面上一致的显示日期
+ 日期/时间组件方法
  getTime(), setTime(milliseconds), getFullYear(), getMonth(), getDate()...

**2. RegExp**  
let expression = /pattern/flags  
pattern: 正则表达式  
flags: g-全局模式 i-不区分大小写 m-多行模式 y-粘附模式 u-Unicode模式 s-dotAll模式，匹配任何字符  
元字符 { [ ( \ ^ $ | ) ] } ? * + 需转义  
分字面量模式 /\w\\\\hello\\\\123/ 和字符串模式(需二次转义) "\\w\\\\\\\\hello\\\\\\\\123"
+ 实例属性 global, ignoreCase, unicode, sticky, lastIndex, multiline, dotAll, source, flags
```javascript
let pattern = /\[bc\]at/i;
let pattern2 = new RegExp("\\[bc\\]at", "i");
```

+ 实例方法  
主要是exec(),用于配合捕获组使用  
test()

+ RegExp构造函数属性  
input($_), lastMatch($&), lastParen($+), leftContext($`), rightContext($') 括号里为其属性名简写  
RegExp.$1~RegExp.$9 存储为9个捕获组的匹配项  
```javascript
let text = "this has been a long summer";
let pattern = /(..)or(.)/g;
if(pattern.test(text)) {
    console.log(RegExp.$1);  // sh
    console.log(RegExp.$2);  // t
}
```

**3. 原始值包装类型**  
Boolean, Number, String  
让原始值拥有对象的行为
```javascript
let s1 = "some text";
let s2 = s1.substring(2);  // 原始值不是对象，逻辑上不应该有方法

// 第二行以读模式访问s1时，后台会执行以下三步
// 1 创建String类型实例
// 2 调用实例上特定方法
// 3 销毁实例
let s1 = new String("some text");
let s2 = s1.substring(2);
s1 = null;
```
+ 所有对象在布尔表达式中自动转为true
+ Number  
toFixed()返回包含指定小数点位数的数值字符串  
toExponential() 返回以科学计数法表示的数值字符串  
toPrecision() 根据情况返回最合理的输出结果
Number.isInteger()用于辨别是否保存为整数  
Number.isSafeInteger() 数值范围Number.MIN_SAFE_INTEGER - Number.MAX_SAFE_INTEGER
+ String  
  - js字符 charAt(index), charCodeAt()/codePointAt()识别32位码点, fromCharCode()/fromCodePoint()
  - normalize() 为解决Unicode字符有多种编码方式问题 而进行规范化
  - 操作方法 concat()拼接字符串(多用+), 提取子字符串：slice(), substr(), substring(startIndex, num)
  - 位置方法 indexOf(), lastIndexOf()
  - 包含方法 startsWith(), endsWith(), includes()
  - trim(), repeat(), padEnd(), padStart()
  - 迭代与解构  原型上有@@iterator方法，可通过for-of迭代，通过解构操作符(...)解构
  - 大小写转换 toLowerCase(), toLocaleLowerCase(), toUpperCase(), toLocaleUpperCase()
  - 模式匹配方法 match(), search(), replace(), split()
  - localeCompare() 比较两个字符串顺序
  - HTML方法 bold(), strike(), italics()...

**4. 单例内置对象**
+ Global  
  - URL编码方法 encodeURI(), encodeURIComponent(), decodeURI(), decodeURIComponent()
  - eval()方法 一个完整的ECMAScript解释器，接收一个要执行的字符串参数，对XSS利用暴露很大的攻击面
  - Global对象属性 NaN, undefined, Object, Array, String, Date, Error...
  - window对象 浏览器将window实现为Global对象的代理
+ Math
  - 对象属性 Math.E, Math.PI, Math.SQRT2...
  - min(), max()
  - 舍入方法 Math.ceil(), Math.floor(), Math.round(), Math.fround()
  - random()方法返回一个[0,1)范围内的随机数
  ```
  number = Math.floor(Math.random()*total_number_of_choices + first_possible_value)
  ```
  - 其他方法 Math.abs(x), Math.pow(x, power), Math.sqrt(x), Math.trunc(x)... 

 
### chapter 6 集合引用类型
**1. Object**  
可通过构造函数，对象字面量定义，用 . 或 [] 存取对象属性，如果要通过变量存取或属性名包含可能导致语法错误的字符，或包含关键字/保留字，或数字字符都可以使用[]存取对象属性  

**2. Array**  
Array.from() 第一个参数是一个类数组对象，即任何可迭代解构; 第二个可选参数是映射函数; 第三个可选参数是用于指定映射函数中this的值  
Array.of() 可以把一组参数转换为数组，用于替代之前Array.prototype.slice.call(arguments)  
+ 检测数组  
假定只有一个全局执行上下文，使用instanceof，如果有多个框架则可能涉及不同的全局执行上下文，则数组的构造函数不同  
为解决这个问题，ECMAScript提供了Array.isArray()判定
+ 迭代器方法  
ES6中，Array原型上暴露3个用于检索数组内容方法，keys(), values(), entries()这些方法都返回迭代器  
+ 复制和填充方法  
copyWithin(), fill()
+ 转换方法  
toString(), toLocaleString(),返回逗号分隔的字符串，可通过join()方法指定分隔符 valueOf()
+ 栈、队列方法  
栈：后进先出 push(), pop()  
队列：先进先出 shift(), push(), unshift()在数组开头添加任意多个值，返回新数组长度
+ 排序方法
reverse(), sort() 默认sort会在每项上调用String()转型函数，然后比较字符串按升序排序  
sort()可以接收一个比较函数。 两者均返回调用他们数组的引用。
+ 操作方法
concat(), slice(), splice()可用作删除、插入、替换使用
+ 搜索和位置方法 
两项必须严格相等 indexOf(), lastIndexOf(), includes()  
断言函数 find(), findIndex()
+ 迭代方法
every(), filter(), forEach(), map(), some(), forEach()相当于使用for循环遍历数组  
+ 归并方法
reduce(),从第一遍历到最后 reduceRight() 从最后遍历到第一项

**3. 定型数组**
为解决WebGL与JavaScript运行时传递数组需额外进行数值转型，提高与WebGL等原生库交换二进制数据的效率    
Float32Array是定型数组中可用的第一个"类型"
+ ArrayBuffer
Float32Array是一种”视图“，允许js运行时访问一块名为ArrayBuffer的预分配内存。  
ArrayBuffer是所有定型数组引用及视图的基本单位。
ArrayBuffer()是一普通的构造函数，可用于在内存中分配特定数量的字节空间。一经创建不能再调整大小，可通过slice()复制全部或部分到一个新实例。要读取写入必须通过视图。
+ DataView
专为文件I/O和网络I/O设计，其API支持对缓冲数据的高度控制。
```javascript
const buf = new ArrayBuffer(2);
const view = new DataView(buf);
view.setUint16(0, 0x0002, true);  // true启用小端字节序(最低有效位保存在第一个字节，最高有效位保存在最后一个字节)
// 缓冲内容 0000 0010 0000 0000
alert(view.getUint8(0));  // 2
```
ElementType: Int8, Uint8, Int16, Int32, Float32...DataView为每种类型都暴露get,set方法
+ 定型数组
另一种形式的ArrayBuffer视图，特定于一种ElementType且遵循系统原生的字节序。  
行为类似普通数组，但由于数组缓冲无法调整大小，concat(), push()等方法不适用。可通过set()和subarray()快速复制数据。
利用定型数组API构建拼接能力  
```javascript
function typedArrayConcat(typedArrayConstructor, ...typedArrays) {
    const numElements = typedArrays.reduce((x, y) => (x.length || x) + y.length);
    const resultArray = new typedArrayConstructor(numElements);
    let currentOffset = 0;
    typedArrays.map(x => {
        resultArray.set(x, currentOffset);
        currentOffset += x.length;
    });
    return resultArray;
}
const concatArray = typedArrayConcat(Int32Array, Int8Array.of(1, 2, 3), Int16Array.from([4, 5, 6, 7]), Float32Array.of(8, 9));
```
+ 上溢和下溢
不会影响到其他索引，有符号数组上溢下溢自动变成二补数形式(补码)，无符号数组上下溢只取最低有效位

**4. Map**  
可以给Map构造函数传入一个可迭代对象初始化实例
```javascript
const m = new Map({
    [Symbol.iterator]: function*() {
        yield ["key1", "val1"];
        yield ["key2", "val2"];
        yield ["key3", "val3"];
    }
})
alert(m.size);  // 3
```
可通过set()添加键值对，get()和has()进行查询，delete()和clear()删除值  
Object只能通过数值、字符串或符号作为键，Map可以使用任何js数据类型作为键  
Object不维护迭代顺序，Map实例会维护键值对的插入顺序  
Map实例提供一迭代器，Symbol.iterator属性引用entries(),也可用forEach(callback, opt_thisArg)  
keys()和values()分别返回以插入顺序生成键和值的迭代器  

**5. WeakMap**  
WeakMap的键只能是Object或者继承自Object的类型，保证只能通过键对象的引用才能取得值   
弱键，不属于正式的引用，不会阻止垃圾回收，不可迭代  
使用：私有变量，DOM节点元数据

**6. Set**  
创建并初始化实例，给Set构造函数传入一个可迭代对象，可用add()增加，has()查询，delete()和clear()删除
Symbol.iterator属性引用values()别名keys()取得迭代器
定义正式集合操作 如交集、并集、差集等
```javascript
class XSet extends Set {
    union(...sets) {
        return XSet.union(this, ...sets);
    }
    // ...其他
    static union(a, ...bSets) {  // 返回两个或多个集合的并集
        const unionSet = new XSet(a);
        for(const b of bSets) {
            for(const bValue of b) {
                unionSet.add(bValue);
            }
        }
        return unionSet;
    }
    // ...
}
```

**7. WeakSet**
弱集合中的值只能是Object或继承自Object的类型  
可用于给对象打标签，若被打标签的对象被删除，弱集合对其引用不影响垃圾回收程序对其回收

### chapter 10 函数
函数是对象，每个函数都是Function类型的实例，函数名是指向函数对象的指针，不一定与函数本身紧密绑定  
实例化函数对象的方式：*函数声明、函数表达式*、箭头函数以及使用Function构造函数  
**1. 箭头函数**
语法简洁，但不能使用arguments, super和new.target, 也不能用作构造函数，没有prototype属性  
  
ES6的所有函数对象都会暴露一个只读的name属性，arguments对象始终会与对应的命名参数同步，在严格模式下，在函数中重写arguments对象会报语法错误。  
没有函数签名，因此不存在函数重载，后定义的会覆盖先定义的函数，默认参数按顺序被初始化，后定义的可以引用先定义的参数。  
*函数声明*会被提升(预先读取并添加到执行上下文),必须写函数名称，*函数表达式*必须等到执行到那一行，可为匿名函数  
函数作为值可作参数传给另一函数，或作为一函数的返回值  
函数内部两特殊对象：arguments, this, ES6新增new.target，其中arguments.callee属性指向arguments所在函数的指针  
**this**：箭头函数中this会保留**定义**该函数时的上下文，标准函数的this是把函数当成方法**调用**的上下文对象(即函数被调用时才能确定this引用的对象)  
ES5给函数对象添加了caller属性，这个属性引用的是调用当前函数的函数，在全局作用域中调用为null  
ES6新增了检测函数是否使用new关键字调用的new.target属性  
  
函数属性和方法：length(保存函数定义的命名参数个数)和prototype(保存引用类型所有实例方法，不可枚举)属性  
apply()和call()方法，以指定的this值来调用函数，bind()方法会创建一个this绑定到传给bind()的对象的函数实例  
**递归**调用  
```javascript
const factorial = (function f(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * f(num-1);
    }
});
```
尾递归(即外部函数的返回值是一个内部函数的返回值)优化  
需在严格模式下，减少栈内存  
```javascript
// 无优化
function fib(n) {
    if(n < 2) {
        return n;
    }
    return fib(n-1) + fib(n-2);
}
// 尾调用优化
"use strict"
function fib(n) {
  return fibImpl(0, 1, n);
}
function fibImpl(a, b, n) {
    if(n === 0) {
        return a;
    }
    return fibImpl(b, a+b, n-1);
}
```
**闭包**指引用另一函数作用域中变量的函数  
每个执行上下文中都有一个包含其中变量的对象，全局上下文的叫变量对象，函数局部上下文的叫活动对象  
闭包会保留包含函数的作用域(活动对象保留，作用域链\[\[Scope]]销毁)，需谨慎使用   
闭包在被函数返回之后，其作用域会一直保存在内存中，直到闭包被销毁  
闭包中的this, 因this和arguments都不能直接在内部函数中访问，因此需将其引用用另一个变量保存供闭包访问  
```javascript
window.identity = 'The Window';
let object = {
    identity: 'My Object',
    getIdentityFunc() {
        let that = this;
        return function() {
            return that.identity;
        };
    }
};
```
IE9之前使用引用计数，把HTML元素保存在某闭包作用域中则该元素不能被销毁，在不用时需置为null以解除引用  
```javascript
function assignHandler() {
    // 存在循环引用， elementObj.onclick = callbackFunc & callbackFunc.args = elementObj
    let element = document.getElementById('someElement');
    element.onclick = () => console.log(element.id);
}
```
立即调用函数表达式(IIFE) 可以模拟块级作用域  
在for循环中使用let会为每个循环创建独立的变量  
  
私有变量&静态私有变量(实现实例共享私有变量和函数，特权方法定义在原型上)  
```javascript
// eg1：私有变量
function MyObject() {
    let privateVariable = 10;
    function privateFunction() {
        return false;
    }
    // 特权方法
    this.publicMethod = function(){
        privateVariable++;
        return privateFunction();
    }
}
// eg2：私有变量 - 定义私有变量和特权方法以隐藏不能被直接修改的数据
function Person(name) {
    this.getName = function() {
        return name;
    };
    this.setName = function(value) {
        name = value;
    }
}
// eg3：静态私有变量
(function() {
    let privateVariable = 10;
    function privateFunction() {
        return false;
    }
    MyObject = function() {};
    MyObject.prototype.publicMethod = function() {
        privateVariable++;
        return privateFunction();
    };
})();
```
模块模式&模块增强模式
```javascript
// 模块增强，返回单例对象(某特定类型的实例，添加额外属性和方法)
let application = function() {
    let components = new Array();
    components.push(new BaseComponent());
    let app = new BaseComponent();
    app.getComponentCount = function() {
        return components.length;
    }
    app.registerComponent = function(component) {
        if(typeof component == "object") {
            components.push(component);
        }
    };
    return app;
}
```
### chapter 8 对象、类与面向对象编程
ECMA-262将对象定义为一组属性的*无序集合*，可把ES的对象想象成散列表，其内容是一组名/值对，值可是数据或函数  
**1. 理解对象**  
+ 属性的类型 - 使用内部特性描述属性特征，如\[\[Enumerable]]
  - 数据属性  
  \[\[Configurable]]: 表示属性是否可以 通过delete删除并重新定义、修改特性以及改为访问器属性 默认为true;  
  \[\[Enumerable]]: 是否可通过for-in循环返回 默认为true;  
  \[\[Writable]]: 是否可被修改 默认为true;  
  \[\[Value]]: 属性实际的值 默认为undefined;  
  需通过Object.defineProperty()修改属性默认特征，不指定默认均为false  
  - 访问器属性 - 包含一个getter函数一个setter函数  
  \[\[Configurable]]: 表示属性是否可以 通过delete删除并重新定义、修改特性以及改为数据属性 默认为true;  
  \[\[Enumerable]]: 是否可通过for-in循环返回 默认为true;  
  \[\[Get]]: 获取(getter)函数 默认为undefined;  
  \[\[Set]]: 设置(setter)函数 默认为undefined;  
+ 通过Object.defineProperties()定义多个属性
+ 通过Object.getOwnPropertyDescriptor()取得指定属性的属性描述符，Object.getOwnPropertyDescriptors()获取所有  
+ 合并对象 Object.assign(dest, ...src) 执行的是浅复制  
+ 对象相等判定 Object.is()解决部分===操作符也无法解决的特殊情况  
```javascript
function recursivelyCheckEqual(x, ...rest) {
    return Object.is(x, rest[0]) && (rest.length < 2 || recursivelyCheckEqual(...rest));
}
```
+ 增强的对象语法
  - 属性值简写 
  - 可计算属性
  - 简写方法名
```javascript
const methodKey = 'sayName';
let person = {
    [methodKey](name) {  // 计算属性键'[xx]'与简写方法名相互兼容
        console.log(`My name is ${name}`);
    }
}
```
+ 对象解构
```javascript
let person = {
    name: 'Matt',
    age: 27,
    job: {
        title: 'Software engineer'
    }
}
let {name: personName, age: personAge} = person;

// 解构在内部使用ToObject()把源数据结构转换为对象
let { length } = 'lotus';
console.log(length);  // 5

// 嵌套解构
let { job: { title }} = person;
console.log(title);  // Software engineer

// 部分解构
let personName, personBar, personAge;
try {
  ({name: personName, foo: {bar: personBar}, age: personAge} = person);
} catch(e) {}
console.log(personName, personBar, personAge);  // Matt, undefined, undefined
```

**2. 创建对象**  
+ 工厂模式
+ 构造函数模式
``` javascript
// 工厂模式
function createPerson(name, age, job) {
    let o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        console.log(this.name);
    };
    return o;
}
let person1 = createPerson("Lotus", 25, "Software Engineer");
// 构造函数模式
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function() {
        console.log(this.name);
    }
}
let person2 = new Person("Lotus", 25, "Software Engineer");
/*要创建Person实例，使用new操作符调用构造函数会执行如下操作
(1) 在内存创建一个新对象
(2) 这个新对象内部的[[Prototype]]特性被赋值为构造函数的prototype属性
(3) 构造函数内部this被赋值为这个新对象(即this指向新对象)
(4) 执行构造函数内部的代码(给新对象添加属性)
(5) 如构造函数返回非空对象，则返回该对象，否则返回刚创建的新对象。
*/
```
问题：构造函数中定义的方法会在每个实例上都创建一遍
+ 原型模式  
每个函数都会创建一个prototype属性，是一个对象，包含应由特定引用类型的实例共享的属性和方法。  
实例对象与构造函数原型之间有直接联系(实例的内部\[\[Prototype]]指针被赋值为构造函数的原型对象)，实例与构造函数间没有。  
isPrototype()会在传入参数\[\[Prototype]]指向调用它的对象时返回true，Object.getPrototypeOf()返回的对象就是传入对象的原型对象。  
为避免使用Object.setPrototypeOf()重写对象的原型继承关系而导致的性能下降，可以通过Object.create()来创建一个新对象并指定原型。  
*in操作符*  
```javascript
// 判断一属性是否是原型属性
function hasPrototypeProperty(object, name) {
    return !object.hasOwnProperty(name) && (name in object); 
}

// 重写整个原型会切断最初原型与构造函数的联系
function Person() {};
Person.prototype = {  // 重写
    name: "Lotus",
    age: 25,
    job: "",
    sayName() { console.log(this.name); }
};
// 恢复constructor
Object.defineProperty(Person.prototype, "constructor", {
    enumerable: false,
    value: Person
});
```
问题：弱化向构造函数传初始化参数的能力以及它的共享特性(包含引用值的属性)
Object.keys()和for-in循环顺序不确定，Object.getOwnPropertyNames()、Object.getOwnPropertySymbols()和Object.assign()枚举顺序是确定的。  
+ 对象迭代
ES8新增两静态方法Object.values(),Object.entries()用于将对象转换为序列化的*可迭代*的格式，执行的是对象的浅复制  

**3. 继承**
见另一篇

**4. 类**  
+ 定义  
类声明及类表达式 被定义前均不能引用 受块作用域限制
+ 类构造函数  
1、实例化 js解释器知道使用new和类意味着应使用类中构造函数进行实例化
实例化后会成为普通的实例方法，但仍要使用new调用  
2、把类当成特殊函数  可做参数传递，可立即实例化  
+ 实例、原型和类成员  
1、实例成员 每个实例都对应一个唯一的成员对象  
2、原型方法与访问器 在类块中定义的方法为原型方法 支持获取和设置访问器  
3、静态类方法 使用static关键字前缀，this引用类自身，适合作为实例工厂  
4、非函数原型和类成员 在类定义外部，手动添加  
5、迭代器与生成器方法 
```javascript
class Person {
    constructor() {
        this.nicknames = ['Jack', 'J-Dog'];
    }
    // 添加一个默认迭代器
    *[Symbol.iterator]() {
        yield *this.nicknames.entries();
    }
    // 或直接返回迭代器实例
    [Symbol.iterator]() {
        return this.nicknames.entries();
    }
}
let p = new Person();
for(let [idx, nickname] of p) {
    console.log(nickname);
}
```
+ 继承
使用extends关键字，不仅可以继承一个类，也可以继承普通的构造函数
1、构造函数、HomeObject和super()  
super只能在派生类构造函数和静态方法中使用  
调用super()会调用父类构造函数，并将返回的实例赋值给this  
如果在子类中显示定义了构造函数，则必须在其中调用super()或在其中返回一个对象  
2、抽象基类  
可供其他类继承，本身不会被实例化  
```javascript
class Vehicle {
    constructor() {
        if(new.target === Vehicle) {
            throw new Error('Vehicle cannot be directly instantiated');
        }
        // 要求子类必须定义某个方法，原型方法在调用类构造函数之前就已经存在
        if(!this.foo) {
            throw new Error('Inheriting class must define foo()');
        }
    }
}
class Bus extends Vehicle {
    foo() {}
}
```
3、继承内置类型  
有些内置类型的方法会返回新实例，默认情况下返回的实例与原始实例类型一致，但可通过覆盖Symbol.species访问器，这个访问器决定在创建返回的实例时使用的类  
4、类混入  
很多js框架(特别是React)已抛弃混入模式，转向组合模式(把方法提取到独立的类和辅助对象中，然后组合起来)
```javascript
class Vehicle{}
let FooMixin = (Superclass) => class extends Superclass {
    foo() { console.log('foo'); }
};
let BarMixin = (Superclass) => class extends Superclass {
    bar() { console.log('bar'); }
};
let BazMixin = (Superclass) => class extends Superclass {
    baz() { console.log('baz'); }
};
function mix(BaseClass, ...Mixins) {
    return Mixins.reduce((accumulator, current) => current(accumulator), BaseClass);
}
class Bus extends mix(Vehicle, FooMixin, BarMixin, BazMixin) {}
```

