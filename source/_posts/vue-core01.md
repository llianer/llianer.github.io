---
title: vue-core01
date: 2022-03-12 11:38:06
categories:
- [Front-end, vue]
---
## 源码解读《深入浅出Vue.js》
2022最新出的一本书《Vue.js设计与实现》读起来感觉更好，读书笔记持续更新中...
https://mubucm.com/doc/1MnFBFxCP2T
### Object的变化侦测
<!-- more -->
``` javascript
// 收集依赖、删除依赖或者向依赖发送通知
export default class Dep {
    constructor() {
        this.subs = [];
    }
    addSub(sub) {
        this.subs.push(sub);
    }
    removeSub(sub) {
        remove(this.subs, sub);
    }
    depend() {
        if(window.target) {
            this.addSub(window.target);
        }
    }
    notify() {
        const subs = this.subs.slice();
        for(let i=0,l=subs.length; i<l; i++) {
            subs[i].update();
        }
    }
}
function defineReactive(data, key, val) {
    let dep = new Dep();
    Object.defineProperty(data, key, {
        enumerable: true,
        configurable: true,
        get: function() {
            dep.depend();
            return val;
        },
        set: function(newVal) {
            if(val === newVal) {
                return;
            }
            val = newVal;
            dep.notify();
        }
    })
}
export default class Watcher {
    constructor(vm, expOrFn, cb) {
        this.vm = vm;
        this.getter = parsePath(expOrFn);
        this.cb = cb;
        this.value = this.get();
    }
    get() {
        window.target = this;
        let value = this.getter.call(this.vm, this.vm);
        window.target = undefined;
        return value;
    }
    update() {
        const oldValue = this.value;
        this.value = this.get();
        this.cb.call(this.vm, this.value, oldValue);
    }
}
// 将一个数据内的所有属性（包括子属性）都转成getter/setter形式，然后追踪变化
export class Observer {
    constructor(value) {
        this.value = value;
        if(!Array.isArray(value)) {
            this.walk(value);
        }
    }
    walk(obj) {
        const keys = Object.keys(obj);
        for(let i=0; i<keys.length; i++) {
            defineReactive(obj, keys[i], obj[keys[i]]);
        }
    }
}
```
+ ES6之前没法侦测到一个新属性的添加和删除，为解决这个问题，vue提供两个api vm.$set与vm.$delete

