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

var Bouns = function() {
  this.salary = null
  this.strategy = null
}
Bouns.prototype.setSalary = function(salary) {
  this.salary = salary
}
Bouns.prototype.setStrategy = function(strategy) {
  this.strategy = strategy
}
Bouns.prototype.getBouns = function() {
  return this.strategy.calculate(this.salary)
}

// 使用
var bouns = new Bouns()
bouns.setSalary(4000)
bouns.setStrategy(new performanceS())
bouns.getBouns() // 16000
```
在这里例子中，计算奖金的方法和计算奖金的策略对象是分开的。bouns对象保存的是工资，其**本身并没有计算的功能**，是把计算**委托**给了之前保存的**策略对象**

上面例子很好的提现了在面向对象编程中的策略模式的思想，下面的例子是在JS中使用策略模式的更普遍的方法
``` js
var strategy = {
  'S': function(salary) {
    return salary * 4
  },
  'A': function(salary) {
    return salary * 3
  },
  'B': function(salary) {
    return salary * 2
  }
};

var getBouns = function(level, salary) {
  return strategy[level](salary)
}
```


