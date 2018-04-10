---
layout:     post
title:      "JavaScript 原型"
subtitle:   " \"Prototype\""
date:       2018/4/10 13:05:45 
author:     "Hrc"
header-img: "img/prototype.jpg"
catalog: true
tags:
    - JavaScript
---

# 原型 #
### 每个函数都有一个prototype属性，该属性存储的就是原型对象: ###
![](https://i.imgur.com/5BWDJDe.png)

这样可以添加原型的方法和属性，
  
理解“**驻留**”概念：当你修改原型时，与之相关的对象会继承这一改变，包括已经创建的对象  

对象自身属性优先级高于原型属性

## 枚举属性： ##
![](https://i.imgur.com/Q288dqm.png)

是否可枚举可以通过propertyIsEnumerrable()来判断，  
会对所有非内建属性返回ture.  
大部分内建属性和原型链中的属性（包括可以显示的）是不可枚举的，  
如果调用是原型链上的某个对象，那么可以枚举。（见此部分最后一张图）


hadOwnProperty()来判断一个属性是自身属性还是原型属性


所有原型属性都会显示flase,但是不代表不可以枚举。


测试：


![](https://i.imgur.com/vkdqqkx.png)


![](https://i.imgur.com/hRwBP5n.png)


![](https://i.imgur.com/MkcrzPx.png)

![](https://i.imgur.com/xvF2K0h.png)


## isPrototypeOf() ##
告诉我们当前对象是否是另一个对象的原型


![](https://i.imgur.com/peQCPFX.png)

_proto_属性（实体对象属性）和prototype（构造器函数属性）功能相同，但**不建议**使用（IE不支持）。

## 扩展内建对象 ##
**内建对象**的构造器函数（Array,String,Object和Function）可以通过原型进行拓展，


例如只要在数组中添加新的方法，就可以使其在所有数组中可用。
下面演示添加一个在数组中查找特定的值的简单方法：

![](https://i.imgur.com/viDP5bu.png)

![](https://i.imgur.com/SaiieDb.png)



如果想通过原型给某个对象添加一个新属性，**务必**检查一下该属性是否存在。

## 原型陷阱 ##


1.当我们对原型对象执行**完全替换**时，可能会触发原型链中的异常。
2.prototype.constructor属性是**不可靠**的

当之前定义的原型对象被完全替换后，原有对象不能当问原型的新增属性，但是依然可以通过_proto_和原有原型对象保持联系。

此时constructor会发生错乱。
此时可以这样解决问题

    Dog.prototype={name:1};//Dog是构造函数,这是重写prototype
    Dog.prototype.constructor=Dog;//重置相应的constructor是一个好习惯



----------
2018/4/10 13:04:23 