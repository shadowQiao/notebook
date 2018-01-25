# this关键字
this关键字是执行上下文的一个属性，其值在**进入上下文阶段就确定**了，并且在执行代码阶段是**不能改变**的。

	activeExecutionContext = {
		VO: {
			arguments,
			...
		}, // 变量对象
		this: thisValue // this关键字
	}
首先介绍一下引用类型的值，一个拥有两个属性的“对象”，base属性（属性所属的对象）以及该base对象中的propertyName属性：
	
	var valueOfReferenceType = {
  		base: ,
  		propertyName: 
	};
	
引用类型的值有以下

1. 当处理一个标识符的时候，标识符其实就是变量名，函数名，函数参数名以及全局对象的未受限的属性
2. 或者进行属性访问的时候
## 默认绑定
	默认绑定是绑定到window对象，绑定null或者undefined，也转化为window对象
	
## 隐式绑定
this关键值取值的规则如下
> 函数上下文中的this值是函数调用者提供，并且由调用表达式的形式而定的。如果在调用括号()的左边，有引用类型的值，那么this的值就会设置为该引用类型值的**base对象**。所有其它情况下，this的值总为null，会隐式转换为**全局对象**。

有特殊的几种情况

1. 当base属性隶属于活动对象时，相当于null。
2. with和catch会加长作用域链，所以会有以下几个现象

例1

	var x = 10;
	with ({
	  	foo: function () {
	    	alert(this.x);
	  	},
	  	x: 20
	}) {
	  	foo(); // 20
	}
	try {
		throw function () {
			alert(this);
		};
	} catch (e) {
		e(); // __catchObject - in ES3, global - fixed in ES5
	}
### 丢失绑定
使用隐式绑定，经常会丢失绑定

例2

	function foo() { 
		console.log( this.a );
	}	var obj = { 
		a: 2,		foo: foo 
	};	var a = "oops, global"; // a是全局对象的属性 	setTimeout(obj.foo, 100); // "oops, global"
## 显式绑定
显式绑定也就是直接指定函数执行的this值

例3

	function foo() { 
		console.log( this.a );	}	var obj = { 
		a:2	};	foo.call( obj ); // 2
显式绑定也没有解决丢失绑定的问题
## 硬绑定
例4

	function foo() { 
		console.log( this.a );	}	var obj = { 
		a:2	};	var bar = function() { 
		foo.call( obj );	}; 	bar(); // 2 	setTimeout( bar, 100 ); 
 	// 2	// 硬绑定的 bar 不可能再修改它的 this
	bar.call( window ); // 2
所有的函数都继承了Function.prototype上的bind方法，为函数提供了硬绑定

例5

	var obj = {
		name: 'tutu'
	};
	function sayHi() {
		console.log(this.name + ' say hello');
	}
	var newHi = sayHi.bind(obj);
	newHi(); // tutu say hello
我们可以自己实现一个bind方法
	
	if (!Function.prototype.bind) {
		Function.prototype.bind = function(obj) {
			var args = Array.prototype.slice.call(arguments, 1),
			func = this;
			return function() {
				var argsAll = args.concat(Array.prototype.slice.call(arguments, 0));
				func.apply(obj, args);
			};
		}
	}
同时MDN提供了一种实现办法

	if (!Function.prototype.bind){ 
	Function.prototype.bind = function(oThis) {		if (typeof this !== "function") { 
		// 与 ECMAScript 5 最接近的 
		// 内部 IsCallable 函数 		
			throw new TypeError("Function.prototype.bind - what is trying to be bound is not callable"); 
		}		var aArgs = Array.prototype.slice.call( arguments, 1 ), 
			fToBind = this,			fNOP = function(){}, 
			fBound = function(){				return fToBind.apply((this instanceof fNOP && oThis ? this : oThis), aArgs.concat(Array.prototype.slice.call( arguments )); 
			};		fNOP.prototype = this.prototype;		fBound.prototype = new fNOP(); 
		return fBound;
	}
### 软绑定
硬绑定存在的问题在于，几乎无法再改变this的指向；为了解决这个问题，我们采用软绑定

	if (!Function.prototype.softBind) {
		Function.prototype.softBind = function(obj) {
			var fn = this;
			var args = [].slice.call(arguments, 1);
			var bound = function() {
				return fn.apply((!this || this === (window || global)) ? obj: this, args.concat([].slice.call(arguments, 0));
			};
			bound.prototype = Object.create(fn.prototype);
			return bound;
		};
	} 
			
## new绑定
实际上并不存在所谓的“构造函数”，只有对于函数的“构造调用”，作为“构造函数”调用的函数就是个普通函数。使用new来调用时，会执行以下几个步骤：

1. 创建一个新的对象
2. 将对象的原型指向构造函数的prototype属性
3. 将对象作为this值，执行构造函数的内容
4. 如果该函数返回原始值，忽略返回值，返回创建的这个新对象；如果返回对象，覆盖创建的新的对象

## 优先级
现在有四种this绑定方式

1. 默认绑定
2. 隐式绑定
3. 显式绑定
4. new绑定

它们的优先级是什么样的呢？

显式绑定 > 隐式绑定

	var obj = {
		name: 'shadow',
		sayHi: function() {
			console.log(this.name + ' say hello');
		}
	};
	var bindObj = {
		name: 'shadow'
	};
	obj.sayHi.call(bindObj); // shadow say hello
new绑定 > 隐式绑定

	var obj = {
		test: function(name) {
			this.name = name;
		}
	};
	var newObj = new obj.test('shadow');
	obj.name // undefined
	newObj.name // shadow
new绑定 > 显式绑定
	
	var obj = {};
	function test(name) {
		this.name = name;
	};
	var newObj = new test.bind(obj)('shadow')
	obj.name // undefined
	newObj.name // shadow
### 判断this
1. 是否在new中调用
2. 是否显式绑定
3. 是否隐式绑定
4. 如果都没有，使用默认绑定

参考

[this关键字](http://goddyzhao.tumblr.com/post/11218727474/this)