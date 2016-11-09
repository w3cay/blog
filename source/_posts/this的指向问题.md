---
title: this的指向问题
date: 2015-08-25 17:53:55
tags: [javascript]
---

引言：javascript的this关键字是个非常让人头疼的部分，this的指向真是让人“出乎意料”。看了《javascript高级程序设计》中的关于this对象的描述，差不多有了一个清晰的认识，为了防止忘记和分享所以做此总结。

## this在不同情况下的指向
###全局环境中的this
```
	alert(this);  //window
```
在全局环境中this指向全局对象，在浏览器中自然就指向Window。
### 全局环境中函数调用
```
var name="Window";
function object(){
	var name = "Object";
	console.log(this.name);
}
object();   //Window
```
这里this指向了全局对象，即Window。在这个例子中即使object函数内部也定义了name属性，但是javascript中遵循谁调用了函数，那么这个this就指向谁。在严格模式中，则是undefined。
### 作为对象的方法调用函数
```
var name = "Window";
var o = {
	name : "object",
	sayName : function(){
		console.log(this.name);
	}
}
o.sayName();  //object
```
这次this指向的是定义的对象o，即当前对象。正好印证了谁调用指向谁的规律。
### 作为构造函数
```
new Person();
```
函数内部的this指向新创建的对象。
### 闭包内部函数
```
var name = "Window";
var o = {
	name : "Object",
	sayName : function(){
		return function(){
			return this.name;
		}
	}
}
console.log(o.sayName()());  //Window
```
存在闭包的情况下，函数的this指向与上面单纯的对象的函数的this指向又不太一样。这是为什么呢？通过参考《javascript高级程序设计》中的讲解加上我自己的理解，我的解释是这样的：闭包其实返回的就是一个匿名函数，而匿名函数的执行环境具有全局性，因此其this对象通常指向window。而又根据函数的执行环境，每次搜索变量时，只搜索到其活动对象为止（此活动对象为window），所以就无法访问其外部函数的变量了。

那么如果想访问到外部作用域中的this，就需要将外部作用域中的this对象保存在闭包可以访问到的变量里。
```
var name = "Window";
var o = {
	name : "Object",
	sayName : function(){
		var that = this;  //注意这里
		return function(){
			return that.name;
		}
	}
}
console.log(o.sayName()());  //Object
```
### 使用call和apply
```
call( thisArg [,arg1,arg2,… ] ); //参数列表，arg1,arg2,...
apply(thisArg [,argArray] );    // 参数数组，argArray
```
两者都是将某个函数绑定到某个具体对象上使用，自然此时的this会被显式的设置为第一个参数。

因本人还是个菜鸟，以上描述如有错误请评论指出...