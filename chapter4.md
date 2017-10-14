# 第四章 单例模式

单例模式，顾名思义，就是一个构造函数只有一个实例。

下面是个简单的单例模式
```js
var Person = (function(){
  var instance = null;
  function init(name) {
    return {
      name: name
    }
  }
  return function(name) {
    if(!instance) {
      instance = init(name)
    }
    return instance;
  }
}())

var a = Person('小明');
var b = Person('小红');

a.name  // '小明'
b.name  // '小明'

a === b  // true
```

大体的思路就是这样，利用闭包保存一个实例的引用，当要生成实例的时候，始终返回该实例对象

下面写更完整的单例模式。
在实际编写的时候，会采用代理的模式，就是将实际的业务操作和单例化操作分开，就像上一章的[柯里化](https://github.com/liangfung/javascript-design-partner-reading/blob/master/chapter3.md#柯里化currying)的第二段代码一样，将函数作为柯里化函数的参数，更能解耦，如此一来，柯里化和单例化的函数的复用性就更高了。
```js
function Singleton(fn) {
  var instance;
  return function() {
    if(!instance) {
      instance = fn.apply(this, arguments)
    }
    return instance;
  }
}
```
