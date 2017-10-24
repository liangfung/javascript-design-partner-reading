# 第五章 策略模式

策略模式就是将一系列的算法封装起来，并且使他们可以相互替换，具体的做法是将实现算法的功能和算法的实现分开

下面的例子是使用策略模式来计算奖金。
最初的代码的实现
``` js
var calculateBouns = function(performanceLevel, salary) {
  if(performmanceLevel === 'S') {
    return salary * 4
  }
  if(performmanceLevel === 'A') {
    return salary * 3
  }
  if(performmanceLevel === 'B') {
    return salary * 2
  }
}

calculateBouns('S', 20000);    // 80000
calculateBouns('A', 10000);    // 30000
```

使用面向对象的思路，以策略模式重写
```js 
var performanceS = function(){}
performanceS.prototype.calculate = function(salary) {
  return salary * 4
}

var performanceA = function(){}
performanceA.prototype.calculate = function(salary) {
  return salary * 3
}

var performanceB = function(){}
performanceB.prototype.calculate = function(salary) {
  return salary * 2
}

var getBouns = function() {
  this.salary = null
  this.strategy = null
}
```


