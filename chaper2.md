## 第二章 this,call & apply

this是javascript中比较灵活的东西。this是由运行时的上下文决定的，而非词法作用域决定。

call和apply是在设计模式中非常常用的两个方法。特别像 **arguments** 这样的类数组，常常会利用call和apply方法来借用Array.prototype对象的方法

### 自己实现bind方法
``` javascript
Function.prototype.bind = function(context) {
  var self = this;  // 保存原函数 
  var context = [].shift.call(arguments); // 保存要绑定的this上下文
  var args = [].slice.call(arguments); // 其他的实参转成数组
  return function() {    // 返回一个函数
    return self.apply(context, [].concat.apply(args, [].slice.call(arguments)));
    // 执行新的函数的时候，会将之前传入的context作为新函数的this
    // 并且整合两次传入的参数作为新函数的参数
  }
}

// 调用
var obj = {
  name: '小明'
}
var func = function(a, b, c, d) {
  alert(this.name);  // "小明"
  alert([a, b, c, d]); // [1, 2, 3, 4]
}.bind(obj, 1, 2)

func(3, 4);
```

### bind 和 call/apply的区别
- call/apply是直接调用函数，改变this的指向
- bind是返回一个新的绑定了this的函数

### 借用其他对象的方法

在操作arguments的时候，我们会非常频繁的向Array.prototype对象借用方法

例子
``` javascript
(function(){
 [].push.call(arguments,3)
 console.log(arguments)
})(1, 2) // 输出[1, 2, 3]
```

想把arguments转成真正的数组的时候，我们可以使用Array.prototype.slice方法，想截去arguments列表中的第一个元素的时候，可以使用Array.prototype.shift方法。下面以V8的源码为例，以 Array.prototype.push为例，剖析其中的原理
``` javascript
function ArrayPush = function() {
  var n = TO_UINT32(this.length);   // 被push的对象的length
  var m = %_ArgumentsLength();      // push的参数个数
  for(var i = 0; i < m; i++) {
    this[n + i] = %_Arguments[i];  // 复制元素(1)
  }
  this.length = n + m;   // 修正length属性(2)
  return this.length;
}
```

从上述的代码中的（1）和（2）可以看出来，push是一个属性复制的过程，在复制属性的时候**顺便修改了length属性**，但是，通过代码可以发现，使用Array.prototype.push需要满足以下两点
- 对象本身可以**存取属性**
- 目标对象需要**有length属性**，且**可读可写**

关于这两个条件，可以通过下面的例子来验证一下。

1. 对象本身可以存取属性。对number类型的数据无法存取，使用 Array.prototype.push的时候，是否可以成功？
``` javascript
var a = 1;
Array.prototype.call(a, 2);
console.log(a.length);   // undefined
console.log(a[0]);   // undefined
```
