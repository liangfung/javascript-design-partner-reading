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

