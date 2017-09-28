# 第三章：闭包和高阶函数

复习了一下闭包，最主要的是学习到，结合闭包使用高阶函数来基本实现一些简单的设计模式
主要内容有四：
1. [函数作为参数传入](#1函数作为参数传入)
2. [函数作为返回值输出](#2函数作为返回值输出)
3. [高阶函数实现AOP](#3高阶函数实现aop)
4. [高阶函数的常用实现](#4高阶函数的常用实现)

## 1.函数作为参数传入
这个就不陌生了，回调嘛。常见的有
- ajax的回调函数
- js的原生高阶函数，比如Array.prototype.map, Array.prototype.reduce, Array.prototype.sort等等，都需要传入回调函数

## 2.函数作为返回值输出
这个比较灵活，比较能体现函数式编程的精妙之处
例子：
>判断数据类型的方法
Object.prototype.call(obj)是个判断数据类型的神器

``` javascript
var isType = function(type) {
 return function(obj) {
  return Object.prototype.toString.call(obj) === '[object ' + type + ']';
 }
}

var isString = isType('String');
var isArray = isType('Number');

isString('a');    // true
isNumber(1);      // true
```

## 3.高阶函数实现AOP
AOP(面向切面编程),就是指把与业务无关的功能模块抽离出来，这些跟业务功能无关的功能通常是日志统计，安全控制，异常处理等等，抽离出来之后再通过“动态zhi”可以让核心功能模块更加的纯净。
下面的例子是增加一个before和after函数
``` javascript
Function.prototype.before = function(beforeFn) {
  var self = this;
  return function() {
    beforeFn.apply(this, arguments);
    return self.apply(this, arguments);
  }
}

Function.prototype.after = function(afterFn) {
  var self = this;
  return function() {
    var ret = self.apply(this, arguments);
    afterFn.apply(this, arguments);
    return ret;
  }
}
```

## 4.高阶函数的常用实现

#### 高阶函数实现节流
``` javascript
var throttle = function(fn, interval) {
  var __self = fn; 
  var firstTime = true;
  var timer;
  return function() {
    // 保存this, 因为下面有setInterval,直接只用this会改变this的指向，
    // 否则，会作为直接调用的方式，this一直指向window(严格模式下为undefined)
    var __me = this;
    // 同样的道理，存起来，不然的话，在setInterval里面调用fn的时候，arguments则是setInterval的回调函数的实参
    var args = arguments;
    if(firstTime) {
      __self.apply(__me, args);
      return firstTime = false;
    }
    if(timer) {
      return false;
    }
    timer = setInterval(function(){
      clearTimeout(timer);
      timer = null;
      __self.apply(__me, args)
    }, interval || 500)
  }
}

window.onresize = throttle( function(){
  console.log(1)
}, 500)
```
