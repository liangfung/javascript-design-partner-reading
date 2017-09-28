## 第三章：闭包和高阶函数

>高阶函数实现节流
``` javascript
var throttle = function(fn, interval) {
  var firstTime = true;
  var timer;
  return function() {
    // 保存this, 因为下面有setInterval,直接只用this会改变this的指向，作为直接调用的方式，一直指向window(严格模式下为undefined)
    var __me = this;
    // 同样的道理，存起来，免得在setInterval里面调用fn的时候，arguments变为setInterval的回调函数的实参
    var args = arguments;
    if(firstTime) {
      fn.apply(__me, args);
    }
    if(timer) {
      return false;
    }
    timer = setInterval(function(){
      clearTimeout(timer);
      timer = null;
      fn.apply(__me, args)
    }, interval || 500)
  }
}
```
