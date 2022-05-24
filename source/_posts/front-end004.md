---
title: 红宝书系列（四）
date: 2021-01-14 09:02:14
categories:
- [Front-end, js]
---

## 应用_BOM_DOM
### chapter 12 BOM
BOM核心是window对象  
**1、window对象**  
+ Global作用域 
+ 窗口关系 
如果当前窗口是最上层窗口，parent等于top对象(均等于window)  
self对象是终极window属性，始终指向window，实则就是同一对象  
+ 窗口位置与像素比 
提供screenLeft和screenTop属性用于表示窗口相对于屏幕左侧和顶部的位置，可使用moveTo()和moveBy()方法移动窗口  
<!-- more -->
物理像素与逻辑像素之间的缩放系数->window.devicePixelRatio  
+ 窗口大小
innerWidth innerHeight 页面视口大小 outerWidth outerHeight 浏览器窗口自身大小  
```javascript
// 确定页面视口大小  
let pageWidth = window.innerWidth,
    pageHeight = window.innerHeight;
if(typeof pageWidth != "number") {
    if(document.compatMode == "CSS1Compat") { //
        pageWidth = document.documentElement.clientWidth;
        pageHeight = document.documentElement.clientHeight;
    } else {
        pageWidth = document.body.clientWidth;
        pageHeight = document.body.clientHeight;
    }
}
```
可使用resizeTo()和resizeBy()方法调整窗口大小
+ 视口位置
度量文档相对于视口滚动距离的属性window.pageXoffset/window.scrollX和window.pageYoffset/window.scrollY  
可使用scroll() scrollTo() scrollBy()方法滚动页面  
```javascript
window.scrollTo({
    left: 100,
    top: 100,
    behavior: 'smooth'
})
```
+ 导航与打开新窗口
window.open()接收四个参数：要加载的URL、目标窗口、特性字符串和表示新窗口在浏览器历史记录中是否替代当前加载页面的布尔值  
返回一个对新建窗口的引用; 新创建窗口的window对象有一个属性opener，指向打开它的窗口，把opener设置为null表示新打开的标签页不需要与打开它的标签页通信，因此可以在独立进程中运行。一旦切断无法恢复。  
浏览器对弹窗施加了限制  
```javascript
let blocked = false;
try {
    let wroxWin = window.open("http://www.wrox.com", "_blank");
    if(wroxWin == null) {
        blocked = true;
    }
} catch(ex) {
    blocked = true;
}
```
+ 定时器
因js是单线程，为调度不同代码执行，js维护了一个任务队列  
要取消等待中的排期任务，可以调用clearTimeout()方法并传入超时ID  
+ 系统对话框



### chapter 13 客户端检测

### chapter 14 DOM

### chapter 15 DOM扩展

### chapter 16 DOM2和DOM3