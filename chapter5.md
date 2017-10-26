# 第五章 策略模式

>定义：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换

策略模式的目标是将实现算法的功能和算法的实现分开

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

## 策略模式之面向对象

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


## 策略模式之JS的实现

**重点**
在策略模式中起码包括两个对象，**策略类**和**环境类**。**策略类封装了算法**，并负责具体的计算工作。第二个部分是环境类Context，**Context接收客户端的请求并且把请求委托给一个策略类**，那说明Context类需要维持对某个策略类的引用

``` js
// 策略类
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

// 环境类
var getBouns = function(level, salary) {
  return strategy[level](salary)
}
```
简单的实现了策略模式，其实在我们的日常开发中，或多或少都会有用到这种模式，原来这就是'高大上'的策略模式呀

## 策略模式之表单验证
表单验证的普通写法可能是这样的
```js
var registerForm = document.getElementById('registerForm')
registerForm.onsubmit = function() {
  if (registerForm.userName.value === '') {
    alert('名字不能为空')
    return false
  }
  if (registerForm.password.value.length < 6) {
    alert('密码长度不少于6位')
    return false 
  }
}
```
这种写法的缺点
- 当校验规则多了之后，onsubmit函数会很庞大
- 增加规则的话要深入到onsubmit函数里面改写，不符合开放-封闭原则
- 校验规则复用到别的表单的话，需要复制很多次，复用性差，不便于统一修改

### 使用策略模式重写
策略模式需要两部分，策略类和Context类。

先封装策略类
```js
var strategies = {
  isEmpty: function(value, errorMsg) {            // 不为空
    if (value === '') return errorMsg
  },
  minLength: function(value, length, errorMsg) {  // 限制长度
    if (value.length < length) return errorMsg
  },
  isMobild: function(value, errorMsg) {           // 是否为手机号
    if(!/^1[3|5|8][0-9]{9}/.text(value)) return errorMsg
  }
}
```
接下来是Validate类。Validate作为Context类，需要将用户请求委托给策略类，实现思想如下
```js
var validateFunc = function() {
  var validator = new Validate();
  // 添加校验规则
  validator.add(registerForm.userName, 'isNotEmpty', '用户名不能为空');
  validator.add(registerForm.password, 'minLength:6', '密码长度不少于6位');
  validator.add(registerForm.phoneNumber, 'isMobile', '手机号码格式不正确');
  
  var errorMsg = validator.start();    // 启动校验
  return errorMsg;
}

var registerForm = document.getElementById('registerForm');
registerForm.onsubmit = function() {
  var errorMsg = dalidateFunc();
  if (errorMsg) {
    alert(errorMsg)
    return false;
  }
}
```
创建validator对象，通过validator.add()添加校验规则。再使用validator.start()启用校验。如果返回的errorMsg有字符串，说明有项目没有通过校验，于是返回false来阻止表单的提交。

**Validate类的实现**
```js
var Validate = function() {
  this.cache = [];
}
Validate.prototype.add = function(dom, rule, errorMsg) {
  var ary = rule.split(':');
  this.cache.push(function(){
    var strategy = ary.shift()    // 用户挑选的strategy
    ary.unshift(dom);             // 规则作用的dom
    ary.push(errorMsg);           // 错误信息
    return strategies[strategy].apply(dom, ary)
  })
}
```
