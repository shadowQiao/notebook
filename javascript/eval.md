## eval
eval的基本用法是，如果传入非字符串直接返回，如果传入字符串就把字符串当js代码一样执行，并返回最后一个表达式或语句的值

* eval里面定义的变量和函数不会被提升
* eval使用调用它的变量作用域

eval在严格模式下，也就是说eval中的字符串以use strict开头或者eval代码运行在严格模式下。

* 可以查询或更改局部变量，但是不能再局部变量中定义新的变量和函数
* 并且eval在严格模式下为保留字，不能覆盖eval

例如：

	eval('"use strict"; var a = "sb";');
	console.log(a) => 报错
	Uncaught ReferenceError: a is not defined
## 全局eval
es3不允许对eval赋予别名，但是大部分实现都是通过别名调用时，eval当全局代码执行。只有IE9以前还是当局部eval调用