## 第二章 this,call & apply

this是javascript中比较灵活的东西。this是由运行时的上下文决定的，而非词法作用域决定。

call和apply是在设计模式中非常常用的两个方法。特别像 **arguments** 这样的类数组，常常会利用call和apply方法来借用Array.prototype对象的方法

>自己实现bind方法
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
```

**bind 和 call/apply的区别**
- call/apply是直接调用函数，改变this的指向
- bind是返回一个新的绑定了this的函数
