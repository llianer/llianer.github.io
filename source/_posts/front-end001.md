---
title: 红宝书系列（一）
date: 2021-01-12 09:07:15
categories:
- [Front-end, js]
---
## 基础01-04
### chapter 1 什么是javascript
+ js实现 
  - 核心(ECMAScript)
  - 文档对象模型(DOM) 用于在HTML中使用扩展的XML
  - 浏览器对象模型(BOM)

<!-- more -->

### chapter 2 HTML中的javascript
+ \<script\>元素
  - async 异步 不保证执行次序
  - defer 脚本可延迟到文档完全被解析和显示之后再执行
  - type 脚本语言的内容类型(MIME)
+ 动态加载
  - 要想让预加载器知道动态请求文件的存在，可在文档头部显式声明
  ``` html
  <link rel="preload" href="gibberish.js">
  ```
+ \<noscript\>元素
  - 浏览器不支持脚本或浏览器对脚本的支持被关闭，包含在\<noscript\>中的内容会被渲染

### chapter 3 语言基础
**1. 语法**
  - 区分大小写
  - 标识符 即变量、函数、属性或函数参数的名称
    - 第一个字符必须是字母、下划线或美元符号
    - 剩下其他可以是字母、下划线、美元符号或数字
  - 注释 // 单行  /* 多行 */
  - 严格模式 脚本开头加"use strict" 预处理指令
  - 语句  

**2. 关键字与保留字**  

**3. 变量**
  - var操作符 声明范围是函数作用域
    - 定义的变量会成为包含它的函数的局部变量;
    - 非严格模式下函数内定义变量时省略var可创建一全局变量;
    - 声明提升，自动提升到函数作用域顶部
  - let操作符 声明范围是块作用域
    - 暂时性死区 let声明的变量不会在作用域中被提升
    - 全局声明不会成为window对象的属性，且须确保页面不会声明同一个变量
    - 不能依赖条件声明模式 即不能判断如果一个变量没有声明则进行声明
    - for循环中会为每次迭代声明一个独立变量实例
  - const声明
    - 声明时必须同时初始化且不可修改
  - 最佳实践
    - 不使用var const优先，let次之  

**4. 数据类型**
  - typeof操作符
    - 一般用于原始值类型判断
    - typeof null 返回"object"
    - 可通过typeof区分函数和其他对象
  - Undefined类型
    - 声明但没有初始化
    - 未声明
  - Null类型
    - null值表示一个空对象的指针
  - Boolean类型
  - Number类型
    - 浮点值
      - 使用内存空间是存储整数值的两倍
      - 精确度在算数计算中远不如整数精确，最高可达17位小数
    - 范围 Number.MIN_VALUE - Number.MAX_VALUE
      - 超过范围 -Infinity Infinity  isFinite()
    - NaN isNaN()
    - 数值转换 Number() parseInt() parseFloat()
  - String类型 16位Unicode字符序列
    - 不可变(immutable)
    - toString() String()
    - 模板字面量 字符串插值 \`${}\`
    - 标签函数 String.raw\`\u00A9\`
  - Symbol类型
    - 其实例 唯一、不可变，确保对象属性使用唯一标识符，不会产生属性冲突
    - 使用全局符号注册表 Symbol.for() 共享重用实例 Symbol.keyFor()查询全局注册表
    - 内置符号属性
    - Symbol.iterator 一个返回对象默认迭代器的方法，即实现迭代器API的函数，由for-of语句使用
      - 由Symbol.iterator函数生成的对象可通过显示调用next()方法返回，也可隐式通过生成器函数返回
    - Symbol.asyncIterator 实现异步迭代器API的函数 for-await-of循环利用这个函数执行异步迭代操作
    ``` javascript
    class Emitter {
        constructor(max) {
            this.max = max;
            this.asyncIdx = 0;
        }
        async *[Symbol.asyncIterator]() {
            while(this.asyncIdx < this.max) {
                yield new Promise((resolve) => resolve(this.asyncIdx++));
            }
        }
    }
    async function asyncCount() {
        let emiter = new Emitter(5);
        for await(const x of emitter) {
            console.log(x);
        }
    }
    ```
    - Symbol.hasInstance 表示一个方法，该方法决定一个构造器对象是否认可一个对象是它的实例。这个属性定义在Function原型上，instanceof操作符会在原型链上寻找这个属性定义，可以在继承的类上通过静态方法重新定义这个函数。
    ``` javascript
    class Bar {}
    class Baz extends Bar {
        static [Symbol.hasInstance]() { return false; }
    }
    let b = new Baz();
    console.log(b instanceof Baz);  // false
    ```
    - Symbol.isConcatSpreadable 一个布尔值，为true则可被打平(仅限数组、类数组对象)
    ``` javascript
    let init = ['foo'];
    let arrLikeObj = { length: 1, 0: 'baz'};
    arrLikeObj[Symbol.isConcatSpreadable] = true;
    console.log(init.concat(arrLikeObj));  // ['foo', 'baz']
    ```
    - Symbol.match 一个正则表达式方法，该方法用正则表达式去匹配字符串。由String.prototype.match()使用
    - Symbol.replace 一个正则表达式方法，该方法替换一个字符串中匹配的子串
    - Symbol.search 一个正则表达式方法，该方法返回字符串中匹配正则表达式的索引
    - Symbol.species 一个函数值，该函数作为创建派生对象的构造函数
    - Symbol.split 一个正则表达式方法，在匹配正则表达式的索引位置拆分字符串
    - Symbol.toPrimitive 一个方法，该方法将对象转换为相应的原始值
    - Symbol.toStringTag 一个字符串，该字符串用于创建对象的默认字符串描述
    - Symbol.unscopables 一个对象，从with环境中排除对应属性
  - Object类型
    - ECMAScript中，Object是所有对象的基类
    - 每个Object实例都有如下属性和方法
      - constructor hasOwnProperty(propertyNameStr) isPrototypeOf(obj) propertyIsEnumerable(propertyNameStr) toLocaleString() toString() valueOf()  

**5. 操作符** 在应用给对象时，操作符通常会调用valueOf()和/或toString()方法来取得可以计算的值
  - 一元操作符 ++/--/+/-
  - 位操作符 操作内存中表示数据的比特(位) 应用位操作符时，64位存储的数值转换为32位数值，执行位操作后把结果从32位转为64位存储
    - 按位非(~) 对数值取反并减1
    - 按位与(&) 
    - 按位或(|) 
    - 按位异或(^)
    - 左移(<<) 左移会保留操作数值的符号
    - 有符号右移(>>) 
    - 无符号右移(>>>)
  - 布尔操作符
    - 逻辑非(!) 始终返回布尔值
    - 逻辑与(&&) 并不一定会返回布尔值
    - 逻辑或(||) 也不一定会返回布尔值
  - 乘性操作符 乘*  除/  取模%
  - 指数操作符 **(Math.pow())
  - 加性操作符 + -
  - 关系操作符 < > <= >=
  - 相等操作符 == != === !==
  - 条件操作符 variable = boolean_expression ? true_value : false_value;
  - 赋值操作符 = += -=...
  - 逗号操作符 在一条语句中执行多个操作  

**6. 语句**
  - if 语句 if(condition) statement1 else statement2
  - do-while 语句 do { statement } while (expression)
  - while 语句 while(expression) statement
  - for 语句 for(initialization; expression; post-loop-expression) statement
  - for-in 语句 用于枚举对象中的**非符号(Symbol)键**属性 for(property in expression) statement
  - for-of 语句 用于遍历可迭代对象的元素
  - 标签语句 label: statement 可通过break或continue语句引用
  - break 和 continue 语句
  - with 语句 将代码作用域设置为特定的对象 严格模式下不允许使用
  - switch 语句 
  ```
  switch(expression) { 
    case value1: 
      statement
      break;
    case value2:
      statement
      break;
    default:
      statement
  }
  ```
+ 函数

### chapter 4 变量、作用域与内存
**1. 原始值与引用值**
  - 原始值 Undefined Null Boolean Number String Symbol 保存原始值的变量是**按值**访问，保存在栈内存
  - 引用值 保存在内存中的对象 不能直接操作对象所在的内存空间 保存引用值的变量是**按引用**访问的
    - 通过简单对象赋值，复制的值是一指针，指向存储在堆内存中的对象
    - 一般通过instanceof操作符判断引用值类型
  - 函数的参数都是按值传递  

**2. 执行上下文与作用域**
  - 每个上下文都有一个关联的**变量对象**，保存上下文中定义的所有变量和函数
  - 内部上下文可以通过作用域链访问外部上下文中的一切，反之不行
  - try/catch语句的catch块和with语句会在作用域链前端添加一个变量对象
  - var let const 作用域 在作用域链(可能涉及原型链)上查找标识符  

**3. 垃圾回收** 通过自动内存管理实现内存分配和闲置资源回收
  - 标记未使用的变量
    - 标记清理
    - 引用计数 存在循环引用问题
  - 性能 垃圾回收的时间调度
  - 内存管理 数据不用时设为null解除引用 
    - 使用const let 声明
    - 隐藏类和删除操作 在构造函数中一次性声明所有的属性以让类实例共享相同的隐藏类 动态删除属性与动态添加属性导致不再共享，最佳实践是把不想要的属性设置为null
    - 内存泄露 大部分由不合理的引用导致 使用闭包
    - 静态分配与对象池 减慢对象更替速度
