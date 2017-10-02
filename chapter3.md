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

var fn = function() {
  console.log(2)
}
var func = fn.before(function(){
  console.log(1)
}).after(function(){
  console.log(3)
})
func();
```

## 4.高阶函数的常用实现

#### 柯里化currying
下面实现一个cost函数来记录一个月的总花销，虽然还不是完整的柯里化，先了解柯里化的思想
``` javascript
var cost = (function (){
  var arr = [];
  return function() {
    if(arguments.length === 0) {
      var money = 0;
      for(var i = 0; i<arr.length; i++) {
        money += arr[i]
      }
      return money;
    } else {
      [].push.apply(arr, arguments)
    }
  }
})()

cost(100);
cost(200);
cost();   // 300
```

经过上面的例子，可以编写一个通用的currying函数，其接受一个函数作为参数，就是要被柯里化的函数
``` javascript
var currying = function(fn) {
  var args = [];
  return function() {
    if (arguments.length === 0) {
      return fn.apply(this, args)
    } else {
      [].push.apply(args, arguments);
      return arguments.callee;
    }
  }
}

var cost = (function(){
  var money = 0;
  return function() {
    for (var i = 0; i < arguments.length; i++) {
      money += arguments[i]
    };
    return money;
  }
})()

// 调用currying函数，将cost柯里化
cost = currying(cost);
cost(100);   // 未真正求出结果, 暂存参数
cost(200);   // 未真正求出结果, 暂存参数
cost(300);   // 未真正求出结果, 暂存参数
cost();  // 600
```

#### 高阶函数实现节流

在JS中，函数大多是由于用户的操作主动触发调用的。但是在少数情况下，函数的触发不是由用户直接控制的，函数有可能被非常频繁的调用，造成大的性能问题
1. 函数被频繁调用的场景
    - window.onresize事件 
    - mouseover事件（如拖拽）
    - 上传进度
2. 节流的原理
借助settimeout控制事件触发的频率
3. 实现
``` javascript
var throttle = function(fn, interval) {
  var __self = fn;  // 需要被节流的函数引用
  var firstTime = true;  // 是否为第一次触发
  var timer;   // 定时器
  return function() {
    // 保存this, 因为下面有setInterval,直接只用this会改变this的指向，
    // 否则，会作为直接调用的方式，this一直指向window(严格模式下为undefined)
    var __me = this;
    // 同样的道理，存起来，不然的话，在setInterval里面调用fn的时候，arguments则是setInterval的回调函数的实参
    var args = arguments;
    if(firstTime) {
      // 如果是第一次触发，不需要延迟执行
      __self.apply(__me, args);
      return firstTime = false;
    }
    if(timer) {
      // 如果定时器还在，则表明还在延迟时间内，不调用
      return false;
    }
    timer = setInterval(function(){  // 延迟调用
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
