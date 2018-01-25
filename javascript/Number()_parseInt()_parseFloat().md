有三个函数可以把一个变量转换成数字，分为是Number()、parseInt()、parseFloat()
## Number()
	1.Boolean
		true => 1
		false => 0
	2.null => 0
	3.undefined => 0
	4.String => 忽略前导不与x|b|o相连的0
		'' => 0
		只包含数字 => 整数
		有效浮点数 => 浮点数
		'0xef' => 视为16进制数
		'0b11' => 视为2进制数
		'0o12' => 视为8进制数
		'1.2e4' => 视为科学计数法
		否则 => NaN
	5.对象，如果有valueOf方法，调用valueOf，如果valueOf不返回原始值，就尝试调用toString，如果toString不返回原始值就报错，否则返回第一次原始值按1、2、3、4的逻辑转换成数字返回。
	var obj = {
       valueOf: function() {
           return {
	            valueOf: function() {
	                return 1;
	            },
	            toString: function() {
	                return 2;
	            }
        	 };
    	},
    	toString: function() {
           return {
	            valueOf: function() {
	                return 3;
	            },
	            toString: function() {
	                return 4;
	            }
	    	     };
    		}
		};
	} => 报错
> 注意：对象字面量默认的toString方法和valueOf方法如下

	var obj = {
		name: 'shadow',
		sayHi: function() {
			console.log('hi');
		}
	};
	obj.toString() => "[object Object]"
	obj.valueOf() => obj返回本身
## parseInt()
parseInt用于将字符串转换成数字，会忽略先导的空格，可以传第二个参数用来指定以什么进制解析字符串

	'' => NaN
	只包含数字 => 整数
	'0xef' => 视为16进制数
	'0b11' => 视为2进制数
	'0o12' => 视为8进制数
	否则 => NaN
> 区别于Number()方法，如果第一个字符不是数字或者负号，parseInt直接返回NaN，它不识别科学计数法'1.2e4'，也不识别；parseInt方法和Number方法一样都会忽略先导的0，一样不会将以0开头的数字识别为8进制数

## parseFloat()
parseFloat用于将字符串转换成浮点数，它与parseInt区别在于
 
 1. 它碰到的第一个小数点是有效的。
 2. 识别科学计数法。
 3. 不能指定第二个参数