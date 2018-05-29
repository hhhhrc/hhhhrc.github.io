---
layout:     post
title:      "JavaScript继承（一）"
subtitle:   " \"通过原型继承\""
date:    2018/5/29 8:09:12 
author:     "Hrc"
header-img: "img/extend.jpg"
catalog: true
tags:
    - JavaScript
---


# 继承 #

----------

## 原型链 ##
默认通过原型来实现继承链。  

对象A中，有一个隐藏属性_proto_，它指向另一个对象B，B的这个属性由指向C，直至链条末端的Object对象，该对象是JS中最高级父对象，语言中所有的对象都必须继承它。  

A->B->C->.....Object,被指向的可以看做是父对象。

原型链示例：

	function Shape(){
    this.name='shape';
    this.toString=function(){return this.name;};
    }
	function TwoDShape(){
    this.name='2D shape';
	}
	function Triangle(side,height){
    this.name='Triangle';
    this.side=side;
    this.height=height;
    this.getArea=function(){return this.side*this.height/2;};
	}

实现继承的代码：

	TwoDShape.prototype=new Shape();
	Triangle.prototype=new TwoDShape();

TwoDShape()继承了Shape的**实体**，也可以说是Shape()的实体覆盖了该对象的原型，类推。  
为什么在这里强调实体呢？  
因为它继承自实体，不是继承自Shape构造器，这确保了继承实现之后，对Shape的任何修改不会对子对象产生影响。

当对prototype的属性进行完全重写时，会产生影响的是constructor属性，这时需要**重置**constructor属性。


	TwoDShape.prototype.constructor=TwoDShape;
	Triangle.prototype.constructor=Triangle;

测试：

	var my=new Triangle(5,10);
	my.getArea();
	25
	my.toString();
	"Triangle"

可以看到继承了Shape()的方法。
  
我们来看一下构造器有无异常：

	my.constructor;
	<ƒ Triangle(side,height){
    this.name='Triangle';
    this.side=side;
    this.height=height;
    this.getArea=function(){return this.side*this.height/2;};
	}

没毛病。

通过instanceof操作符，可以验证my是不是上面三个构造器的实例，很明显是的。 
 
通过isPrototypeOf()方法验证时的小问题：

	Shape.isPrototypeOf(my);
	false
	Shape.prototype.isPrototypeOf(my);
	true  

为什么第一个是flase呢？  
因为my对象的原型不是构造器，而是一个实体。  
为什么第二个是true?  
我的理解是：由上面实现继承的语句得知，构造器的原型是一个个实体,my对象又是每一个构造器的实例，所以验证为true.

**tips**：学会将**共享属性迁移到原型**中去，因为每创建新对象时，属性就会被添加到this中，会出现很多相同的属性占用空间，将共有属性添加到原型中，会使代码更有效率。

实现：

	function Shape(){
	Shape.prototype.name='shape';}

需要强调的一点是，我们**必须在扩展原型对象之前完成继承关系的创建**。意思就是先继承（创建新实体覆盖对象原型），再扩展原型（共有属性共享）。

## 只继承于原型 ##

如果将所有可重用的属性都添加到原型中，那么仅仅依靠原型就可以完成继承了（不需要为继承关系创建新的实体）

为什么避免创建实体？  
因为new Shape方式会将Shape的属性设定为对象自身属性，代码不可重用。

问题在于将原型对象直接连接起来的话，因为都是引用传递，子对象和父对象指向同一个引用，这样会导致子对象对原型进行修改时，父对象也会被修改，当创建Shape对象时，显示的名字会是Triangle。

	function Shape(){}
	Shape.prototype.name='shape';
	Shape.prototype.toString=function(){return this.name;};

	function TwoDShape(){}
	TwoDShape.prototype=Shape.prototype;
	TwoDShape.prototype.constructor=TwoDShape;
	TwoDShape.prototype.name='2D shape';

	function Triangle(side,height){
	this.side=side;
	this.height=height;}
	Triangle.prototype=TwoDShape.prototype;
	Triangle.prototype.constructor=Triangle;
	Triangle.prototype.name='Triangle';
	Triangle.prototype.getArea=function(){return this.side*this.height/2;};

这样的方法提高了效率，但是依然存在问题。   
由于这里所采用的都是引用传递而不是值传递，子对象和父对象指向的是同一个对象，所以一旦子对象对原型进行了修改，父对象也受到影响。
例如上面如果有Triangle.prototype.name='Triangle';
下面再用new Shape()创建新对象时，nama属性也是Triangle。   
这显然不是我们想要的，那么如何解决这个问题呢，看下面  

我们只需创建一个空函数f()，将他的原型设置为父级构造器， 
  
	var F=function(){};
	F.prototype=Shape.prototype;
	TwoDShape.prototype=new F();

完整实例如下：

	function Shape(){}
	Shape.prototype.name='shape';
	Shape.prototype.toString=function(){return this.name;};
	.>ƒ (){return this.name;}
	function TwoDShape(){}
	var F=function(){};
	F.prototype=Shape.prototype;
	TwoDShape.prototype=new F();
	TwoDShape.prototype.constructor=TwoDShape;
	TwoDShape.prototype.name='2D shape';
	"2D shape"
	function Triangle(side,height){
	this.side=side;
	this.height=height;}
	var F=function(){};
	F.prototype=TwoDShape.prototype;
	Triangle.prototype=new F();
	Triangle.prototype.constructor=Triangle;
	Triangle.prototype.name='Triangle';
	Triangle.prototype.getArea=function(){return this.side*this.height/2;};
	

这样一来，有了一个中介来打破连锁关系，就可以在保持原型链的基础上使父对象摆脱子对象的影响了。

持续更新。



