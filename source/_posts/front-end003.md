---
title: 红宝书系列（三）
date: 2021-01-14 09:01:55
categories:
- [Front-end, js]
---

## 进阶11_7_9
### chapter 11 期约与异步函数
**1. 异步编程**  
在js这种单线程事件循环模型中，同步与异步操作是代码所要依赖的核心机制。  
在排定回调后基本没办法知道系统状态何时变化，需在状态更新后通知其他代码。  
使用嵌套回调，不好维护。  
<!-- more -->
**2. 期约(promise)**  
对尚不存在结果的一个替身，ES6新增正式的Promise(期约)引用类型，增加了对Promise/A+规范的完善支持  
+ 期约基础
1、期约状态机 待定(pending)、解决(resolved)、拒绝(rejected)  
2、解决值、拒绝理由及期约实例  值或理由都是包含原始值或对象的不可修改的引用，可选，默认为undefined  
3、通过执行函数控制期约状态 执行器函数是同步执行的，初始化期约  
4、Promise.resolve() 实例化一个解决的期约，是一个幂等方法，这个静态方法能包装任何非期约值  
5、Promise.reject() 实例化一个拒绝的期约并抛出一个异步错误，不能通过try/catch捕获，只能通过拒绝处理程序捕获，非幂等  
6、同步/异步执行的二元性 需通过异步模式捕获错误，期约是同步对象，但也是异步执行模式的媒介  
```javascript
try {
    Promise.reject(new Error('bar'));
} catch(e) {
    console.log(e);  // 捕获不到
}
// 抛出错误
// Uncaught(in promise)Error: bar
```
拒绝期约的错误没有抛到执行同步代码的线程里，而是通过浏览器异步消息队列来处理的  
代码一旦以异步模式执行，唯一与之交互的方式就是使用异步结构即期约的方法  
+ 期约的实例方法  
是连接外部同步代码与内部异步代码之间的桥梁，可以访问异步操作返回的数据  
1、实现Thenable接口 ES的Promise类型实现了Thenable接口  
2、Promise.prototype.then() 接受最多两个参数：onResolved处理程序和onRejected处理程序，返回一个新的期约实例  
onRejected和onResolved处理程序返回的值都会被Promise.resolve()包装  
3、Promise.prototype.catch() 相当于调用Promise.prototype.then(null, onRejected)  
4、Promise.prototype.finally() 给期约添加onFinally处理程序，在期约转换为解决或拒绝状态都会执行，但不知道期约的状态是解决还是拒绝，主要用于添加清理代码。状态无关的方法，大多数情况下表现为父期约的传递(已解决和被拒绝)，若返回待定期约或抛出错误则返回相应期约(待定或拒绝)  
5、非重入期约方法 当期约进入落定状态，与状态相关的处理程序被推进消息队列，处理程序后的同步代码会在处理程序之前执行  
拒绝期约类似于throw()表达式，异步错误只能通过异步的onRejected处理程序捕获  
+ 期约连锁与合成  
期约的处理程序是按照他们添加的顺序执行的，有向非循环图是体现期约连锁可能性的准确表达，可将异步任务串行化  
Promise类提供两个将多个期约实例组合成一个期约的静态方法：Promise.all()和Promise.race()  
Promise.all()创建的期约会在一组期约全部解决后再解决。接收一个可迭代对象，返回新期约。如果有一个待定则合成期约待定，一个拒绝则合成的期约也会拒绝  
Promise.race()返回一个包装期约，是一组集合中最先解决或拒绝的期约的镜像  
合成的期约会静默处理所有包含期约的拒绝操作  
串行期约合成 基于后续期约使用之前期约的返回值来串联期约
```javascript
function addTwo(x) { return x+2; }
function addThree(x) { return x+3; }
function addFive(x) { return x+5; }
function compose(...fns) {
    return x => fns.reduce((promise, fn) => promise.then(fn), Promise.resolve(x));
}
let addTen = compose(addTwo, addThree, addFive);
addTen(8).then(console.log);  // 18
```
+ 期约扩展
ES6期约实现不具备期约取消和进度追踪特性  
1、期约取消  
```javascript
class CancelToken {
    constructor(cancelFn) {
        this.promise = new Promise((resolve, reject) => {
            cancelFn(() => {
                setTimeout(console.log, 0, 'delay cancelled');
                resolve();
            });
        });
    }
}
function cancellableDelayedResolve(delay) {
    setTimeout(console.log, 0, 'set delay');
    return new Promise((resolve, reject) => {
        const id = setTimeout(() => {
            setTimeout(console.log, 0, 'delayed resolve');
            resolve();
        }, delay);
        const cancelToken = new CancelToken((cancelCallback) => cancelButton.addEventListener('click', cancelCallback));
        cancelToken.promise.then(() => clearTimeout(id));
    })
}
startButton.addEventListener('click', () => cancellableDelayedResolve(1000));
```
2、期约进度通知
```javascript
class TrackablePromise extends Promise {
    constructor(executor) {
        const notifyHandlers = [];
        super((resolve, reject) => {
            return executor(resolve, reject, (status) => {
                notifyHandlers.map((handler) => handler(status));
            });
        });
        this.notifyHandlers = notifyHandlers;
    }
    notify(notifyHandler) {
        this.notifyHandlers.push(notifyHandler);
        return this;
    }
}

let p = new TrackablePromise((resolve, reject, notify) => {
    function countdown(x) {
        if(x>0) {
            notify(`${20*x}% remaining`);
            setTimeout(() => countdown(x-1), 1000);
        } else {
            resolve();
        }
    }
    countdown(5);
})

p.notify((x) => setTimeout(console.log, 0, x));
p.then(() => setTimeout(console.log, 0, 'completed'));
// notify函数会返回期约，所以可以连缀调用
p.notify((x) => setTimeout(console.log, 0, 'a:', x))
    .notify((x) => setTimeout(console.log, 0, 'b:', x));
```
**3. 异步函数**  
ES8中提供async/await，通过promise异步产生的值不用放到异步处理程序中也能获取到  
ES对函数进行扩展，新增两关键字：async, await  
1、async 用于声明异步函数, 若return返回了值，这个值会被Promise.resolve()包装成一个期约对象  
在异步函数中抛出错误会返回拒绝的期约，但函数中拒绝期约的错误不会被捕获  
2、await 用于暂停异步函数代码的执行，等待期约解决。与生成器函数中的yield一样，让出js运行时的执行线程  
等待会抛出错误的同步操作，会返回拒绝的期约，对拒绝的期约使用await会将拒绝期约返回  
异步函数的特质不会扩展到嵌套函数，await只能直接出现在异步函数的定义中  
js运行时碰到await时会记录在哪里暂停执行，等到await右边值可用，会向消息队列推送一个恢复异步函数执行的任务  
```javascript
async function sleep(delay) {
    return new Promise((resolve) => setTimeout(resolve, delay));
}
async function foo() {
    const t0 = Date.now();
    await sleep(1500);
    console.log(Date.now - t0);
}
foo();  // 1502
```
平行执行 如果顺序不必须保证，可以先一次性初始化所有期约，再分别等待它们的结果  
```javascript
async function randomDelay(id) {
    const delay = Math.random() * 1000;
    return new Promise((resolve) => setTimeout(() => {
        console.log(`${id} finished`); 
        resolve();    
    }, delay));
}
async function foo() {
    const t0 = Date.now();
    const promises = Array(5).fill(null).map((_, i) => randomDelay(i));
    for(const p of promises) {
        await p;
    }
}
// 期约没按照顺序执行，但await按顺序收到了每个期约的值
```
栈追踪与内存管理 js创建期约时会尽可能保留完整调用栈，会更占内存;使用异步函数，栈追踪信息可准确反应当前调用栈，性能更优  
异步函数是将期约应用于js函数的结果，异步函数可以暂停执行而不会阻塞主线程  

### chapter 7 迭代器与生成器 
最简单的迭代 循环遍历数组，迭代在有序集合上进行。es6后支持了迭代器模式  
可迭代对象 - 实现了Iterable接口，可通过迭代器Iterator消费  
+ 迭代器模式  
迭代器是按需创建的一次性对象，每个迭代器都会关联一个可迭代对象，而迭代器会暴露迭代其关联可迭代对象的API  
1、实现Iterable接口(可迭代协议)要求：1、支持迭代的自我识别 2、创建实现Iterator接口的对象  
意味着必须暴露一个属性作为默认迭代器，使用Symbol.iterator作为键，这个属性须引用一个迭代器工厂函数，返回一个新迭代器  
很多内置类型实现了Iterable接口：字符串、数组、映射、集合、arguments对象、NodeList等DOM集合类型  
这些类型接收可迭代对象的原生语言特性：for-of循环、数组解构、扩展操作符、Array.from()、创建集合、创建映射、Promise.all()及Promise.race()接收由期约组成的可迭代对象、yield * 操作符  
2、迭代器协议  
调用迭代器的next()方法返回一个IteratorResult对象，包含两个属性：done和value  
```javascript
// 自定义迭代器
class Counter {
    constructor(limit) {
        this.limit = limit;
    }
    [Symbol.iterator]() {
        let count = 1, limit = this.limit;
        return {
            next() {  // 使用闭包形式，每创建一迭代器就对应一新计数器，使得实例可以被迭代多次
                if(count < limit) {
                    return { done: false, value: count++ };
                } else {
                    return { done: true, value: undefined };
                }
            }
        };
    }
}
let counter = new Counter(3);
for(let i of counter) { console.log(i); }
```
Symbol.iterator属性引用的工厂函数会返回相同的迭代器  
3、提前终止迭代器  
可选的return()方法用于指定在迭代器提前关闭时执行的逻辑，必须返回一个有效的IteratorResult对象    
- for-of循环通过break、continue、return或throw提前退出;
- 解构操作并未消费所有值。
数组迭代器不能关闭，检测迭代器是否可以关闭可以通过测试迭代器实例的return属性是不是函数对象  
+ 生成器  
拥有在一个函数块内暂停和恢复代码执行的能力。使用生成器可以自定义迭代器和实现协程  
1、基础 生成器的形式是一个函数，函数名称前加一个星号(*)表示它是一生成器  
调用生成器函数会产生一个生成器对象，一开始处于暂停状态，实现了Iterator接口，因此有next()方法，调用next()方法会让生成器开始或恢复执行  
生成器函数只会在初次调用next()方法后开始执行  
生成器对象实现了Iterable接口，默认的迭代器是自引用的  
```javascript
function* generatorFn() {}
const g = generatorFn();
console.log(g === g[Symbol.iterator]());  // true
```
2、通过yield中断执行 yield关键字可以让生成器停止和开始执行  
yield有点像函数的中间返回语句，它生成的值会出现在next()方法返回的对象里。通过yield关键字退出的生成器函数会处在done: false状态;通过return关键字退出的生成器函数会处于done: true状态。  
- 生成器对象作为可迭代对象  
- 使用yield实现输入和输出 
- 产生可迭代对象
- 使用yield*实现递归算法
```javascript
// 生成随机双向图
class Node {
    constructor(id) {
        this.id = id;
        this.neighbors = new Set();
    }
    connect(node) {
        if(node !== this) {
            this.neighbors.add(node);
            node.neighbors.add(this);
        }
    }
}
class RandomGraph {
    constructor(size) {
        this.nodes = new Set();
        for(let i=0; i<size; ++i) {
            this.nodes.add(new Node(i));
        }
        const threshold = 1/size;
        for(const x of this.nodes) {
            for(const y of this.nodes) {
                if(Math.random() < threshold) {
                    x.connect(y);
                }
            }
        }
    }
    print() {
        for(const node of this.nodes) {
            const ids = [...node.neighbors]
                .map(n => n.id)
                .join(',');
            console.log(`${node.id}: ${ids}`);
        }
    }
    isConnected() {
        const visitedNodes = new Set();
        function* traverse(nodes) {
            for(const node of nodes) {
                if(!visitedNodes.has(node)) {
                    yield node;
                    yield* traverse(node.neighbors);
                }
            }
        }
        const firstNode = this.nodes[Symbol.iterator]().next().value;
        for(const node of traverse([firstNode])) {
            visitedNodes.add(node);
        }
        return visitedNodes.size === this.nodes.size;
    }
}
```
3、生成器作为默认迭代器
4、提前终止生成器
生成器对象的return()和throw()方法都可以用于强制生成器进入关闭状态  

### chapter 9 代理与反射
**1. 基础**  
从很多方面看，代理类似C++指针，因为它可用作目标对象的替身，但又完全独立于目标对象  
代理通过Proxy构造函数创建，接收两参数，目标对象和处理程序对象  
Proxy.prototype是undefined,因此不能使用instanceof操作符  
1、定义捕获器 - 处理程序对象中定义的基本操作的拦截器,如get()  
get()捕获器会接收到目标对象、要查询的属性和代理对象三个参数，可基于这些参数重建原始操作  
但也不需要手动重建原始行为，可通过全局Reflect对象上(封装了原始行为)的同名方法来轻松重建  
处理程序对象中所有可以捕获的方法都有对应的(Reflect)API方法  
```javascript
const target = {
    foo: 'bar'
};
const proxy = new Proxy(target, Reflect);  // 创建捕获所有方法且都转发给对应反射API的空代理
const proxy1 = new Proxy(target, {
    get(target, property, receiver) {
        console.log('get()');
        return Reflect.get(...arguments);
    }
})
```
捕获处理程序的行为必须遵循“捕获不变式”(trap invariant)",防止捕获器定义出过于反常的行为  
Proxy暴露了revocable()方法，支持撤销代理对象与目标对象的关联，撤销操作不可逆且幂等  
```javascript
const target = {
    foo: 'bar'
};
const handler = {
    get() {
        return 'intercepted';
    }
};
// 在实例化时同时生成撤销函数(revoke)和代理对象
const { proxy, revoke } = Proxy.revocable(target, handler);
console.log(proxy.foo);  // intercepted
revoke();
console.log(proxy.foo);  // TypeError
```
大多数反射API方法在Object类型上有对应的方法，Object上的方法适用于通用程序，反射方法适用于细粒度的对象控制与操作  
返回状态标记(布尔值，表示操作是否成功)的反射方法：
- Reflect.defineProperty()
- Reflect.preventExtensions()
- Reflect.setPrototypeOf()
- Reflect.set()
- Reflect.deleteProperty()  
使用反射方法替代操作符：  
- Reflect.get()替代对象属性访问操作符
- Reflect.set()替代赋值操作符
- Reflect.has()替代in或with()
- Reflect.deleteProperty()替代delete
- Reflect.construct()替代new  

可在一个目标对象上构建多层拦截，代理另一个代理  
代理中的问题 - 代理中的this，目标对象依赖对象标识时; Date类型方法的执行依赖内部槽位\[\[NumberDate]],而代理对象上不存在这个内部槽位  

**2. 代理捕获器与反射方法**   
只要在代理上调用，所有捕获器都会拦截它们对应的反射API操作  
代理可捕获13种不同的基本操作 - get(),set(),has(),defineProperty(),  
getOwnPropertyDescriptor(),deleteProperty(),ownKeys(),  
getPrototypeOf(),setPrototypeOf(),isExtensible(),  
preventExtensions(),apply(),construct()  
  
**3. 代理模式**  
1、跟踪属性访问  
2、隐藏属性  
3、属性验证  
4、函数与构造函数参数验证  
5、数据绑定与可观察对象  
```javascript
const userList = [];
function emit(newValue) {
    console.log(newValue);
}
const proxy = new Proxy(userList, {
    set(target, property, value, receiver) {
        const result = Reflect.set(...arguments);
        if(result) {
            emit(Reflect.get(target, property, receiver));
        }
        return result;
    }
})
```
