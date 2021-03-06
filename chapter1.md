# chapter 1 
## 1.3 对象扩展
### P3
#### 1.prototype.js的扩展方法
```
	function extend(destination,source){
		//遍历对象内的属性/数组内的元素
		for( var property in source){
			destination[property] = source[property]
		}
			return destination;
		}
		//小demo
		var obj1 = {a:function(){console.log('hello world')}};
		var obj2 = {};
		extend(obj2,obj1);
		console.log('obj2',obj2);
		//obj2就有obj1中的方法了
		obj2.a();
		
```

#### 2.Object.keys
```
	Object.keys = Object.keys || function(obj){
		var a = [];
		//这是一句厉害的代码 还是不太理解 它的扩展性是从哪里来的？
		for(a[a.length] in obj);
		return a;
	}
```

>> 解释：```for(a[a.length] in obj);```这句代码的意思：初始a.length = 0
第一次遍历：a[0] = 遍历属性1，a.length = 1，然后开始第二次遍历……直到遍历完成


###P5 
#### 1.针对Object.assign的补丁
Object.assign是ES6新增的对象方法。它的用处是将所有源对象上的可枚举属性全部赋值到目标对象上。如果浏览器不支持assign方法，我们可以将它打补丁。以下是Object.assign的补丁。

```
		//将目标转换为对象 并且将null和undefined排除在外 
		//原因是null和undefined强制转化为对象后都是空对象
		function ToObject(val){
			if(val === null || val === void 0){
				throw new TypeError('Object.assign can not be with null or undefined');
			}
			return Object(val);
		}

		//如果没有object.assign 那么可以用这个方法来代替
		module.export = Object.assign || function(target,source){
			//定义源对象
			var from;
			//可遍历的属性集合
			var keys;
			//定义目标对象
			var To = ToObject(target);
			//根据参数的数量 
			//s之所以设置为1，因为第二个设置为源对象 第一个是目标对象
			//双重循环遍历，利用Object.keys遍历出源对象的所有可枚举属性
			for(var s = 1; s<arguments.length; s++){
				from = arguments[s];
				keys = Object.keys(Object(from));
				for(var i = 0; i<keys.length; i++){
					To[keys[i]] = from[keys[i]];
				}
			}

			return To;
		}


```

************

## 1.4 数组化
### p6
#### 1.使用Array.prototype.slice.call将类数组转换为数组
Demo:

```
	var obj = {
			//索引值形式 
			'0': 'a',
			'1':'b',
			'2':'c',
			//length必写 长度是多少就是生成多少长度的数组
			'length': 3
		 }
	//输出['a','b','c']
	Array.prototype.slice.call(obj)

```

原理：
V8引擎下slice代码源码：

```
function slice(start, end) {   
	//并不要求这个对象是数组 只要这个对象有Length属性
	var len = ToUint32(this.length), result = [];   
	for(var i = start; i < end; i++) {   
   		result.push(this[i]);   
	}   
    	return result;   
	}       

```

缺点：IE上不能运行

Array.prototype.slice.call(arguments)能将具有length属性的对象转成数组，除了IE下的节点集合（**因为ie下的dom对象是以com对象的形式实现的，js对象与com对象不能进行转换**） 

参考文献：





#### 2. jQuery中将类数组转换为数组的方式 

```
	const makeArray = function(array){
		//定义一个新的数组容器
		var ret = [];
		//首先判断输入的不是null
		if(array !== null){
			var i = array.length;
			//对输入的参数进行判断 其中判断是否为字符串/是否为函数/是否为window对象 
			if( i == null || typeof array == 'string' || jQuery.isFunction(array) || window.setInterval ){
				ret[0] = array;
			}
			else{
				while(i){
					ret[--i] = array[i];
					}
				}
			}
			return ret;
		}

```
****


### P7 
#### 1. Ext下的toArray方法

```
	 var toArray =  function(){
	 		//通过isIE这个外部遍历判断是否处于IE浏览器中
			return isIE ? 
			// 在IE浏览器环境下 多了一个res的参数 
			function(a,i,j,res){
			// res为数组容器
				res = [];
			// 遍历这个com对象 将它所有的元素放进res中
			// 因为each方法还是非常关键的 剔除了一些非索引的属性
				Ext.each(a,function(v){
					res.push(v);
				})
			//对这个进行slice操作
				return res.slice(i||0, j||res.length);
				}
			//如果不是IE浏览器 那就好办了 直接上Array.prototype.slice.call
				:
				function(a,i,j){
					return Array.prototype.slice.call(a, i||0,j||a.length);
				}
		 }();//注意这里的() 这里是直接执行的 也就是根据isIE判断返回哪个函数

```
****
### P10
#### 1. 判断是否是Array的方法
##### 1.1 Prototype.js提供的方法
利用&&符号的特性，比如 ```s1 && s2 && s3```,如果s1为true，那么就会继续执行s2，直到碰到为false的,结束执行。

```
	function isArray(arr){
			//首先判断输入的参数是否为null 更重要的是'splice' in null会报语法错误
			return arr != null 
			//判断它的类型是否为object
			&& typeof arr === "object" 
			//判断splice或者join是不是在数组中
			&& 'splice' in arr 
			&& 'join' in arr;
		}
```


**************
### P11
#### 1.如何判断null/undefined/NaN
```
	//判断是否是Null
	function isNull(obj){
		return obj === null;
	}
	
	//判断是否是NaN
	function isNaN(obj){
		return obj !== obj;
	}
	
	//判断是否是undefined
	function isUndefined(obj){
			//Q：为什么这里要用void 0来代替undefined?
			return obj === void 0;
	}
```

>> 知识点总结：		
>> 1. undefined在js全局作用域中它是一个只读的属性，不可以重写.但是在局部作用域中，它是可以重新写入的。		
>> 2. void表达式无论表达式内容是什么，返回的都是undefined，所以void 0是最短小便捷的，因此选择了这个形式。

参考文章	
1.[Why does void 0 replace undefined](https://github.com/hanzichi/underscore-analysis/issues/1)


*************
### P12 
#### 1.Javascript几个比较“独特”的属性

##### 1.1 a !== a
```
	console.log(NaN === NaN)
```

##### 1.2 a == b && b != a

```
	//在IE678中显示flase(其他现代浏览器中显示false)
	console.log(document == window);
	//在IE678中显示true(其他现代浏览器中显示false)
	console.log(window == document);

```

##### 1.3 a == !a

```
	//案例1
	{
		let a = '0';
		//这里的运算顺序是 '0'转换为数字0 然后右边的!a为false 0 == false 自动跳入Boolean环境
		console.log(a == !a);
	}
	//案例2：
	{
		let a = [];
		console.log(a == !a); //输出true
	}
	
```

>> 知识点：函数运算符的优先级 

##### 1.4 a === a+100
```
	var a =  Infinity;
	console.log(a == a+100);
```


>> 知识点：infinity

##### 1.5

```
	console.log(3>2>1); //输出false
	console.log(1<2<3); //输出true

```

>>知识点：		
>>**javascript的隐式计算:** 3>2=>输出true,true>1,这时候boolean会自动地转换成1,1>1,输出false。		

***********
 


### P14
#### 1.jQuery中判断数据类型的方法
思路： 1.定义一个对象 以键值对的形式存储相应的数据类型
	   2.根据typeof的缺陷去做一个合理的判断 先处理null/undefined类型 再处理Number/String/Bollean，最后将难处理的function date obj等类型进行对象的映射！


```
	var class2type = {}; //定义一个变量
	jQuery.each("Boolean Number String Function Null Array Date RegExp Object Error".split(" "),function(i,name){
			class2type[ "[object " + name + "]" ] = name.toLowerCase();
	}) //将数据类型存储到对象中
	core_toString = class2type.toString; //定义一个core.toSting变量

	jQuery.type = function(obj){
		/** 知识点1:判断null == null或者 undefined == null都会返回为true */
		/** 知识点2：String(obj) 首先会看obj有没有toString方法，有的话直接调用。而没有
		toString方法的只有null和undefined，则返回相应的null和undefined.
		*/
		//这里主要判断null和undefined类型，利用知识点1，当传入的obj为null或者undefined时，也返回它们自己的类型(null或者undefined)；
		if(obj == null){
			return String(obj);
		}
		
		//当Obj的类型不为null/undefined时
		/**知识点3：因为tyeof的缺陷 Number/String/Boolean都可以进行判断 剩下就是当typeof为"object"时剩下的一些数据类型判断*/
		/**知识点4：typeof obj ==="function"是为了修补5.5以下版本的safri会RegExptypeof		为function(浏览器Bug);
		*/
			return typeof obj === "object" || typeof obj ==="function" ?
		
		//core_toString.call(obj) 相当于 obj.toString();转化为字符串
		//这样core_toString.call(obj)可以暴露出很多(例如[object Function]等）
			class2type[ core_toString.call(obj)] || "object" :
			typeof obj;
		}	
	
```
 1. 疑问1：class2type[ "[object " + name + "]" ]这种写法怎么暴露不出来？
 2. 疑问2： core_toString.call(obj)这句话会编译成什么样子？
 	core_toString.call(obj) 相当于 obj.toString();转化为字符串 自己可以玩一下
 3. jQuery.type的执行顺序为？	
	一旦执行了return,就表明这个函数已经运行完，如下代码所示：
	
	```
		function demo(){
			var a = 1;
			//如果if中条件为true 那么执行线路1 并且执行完if内的语句结束 否则执行线路2
			//线路1
			if(true){
				a = 2;
				return a ;
			}
			//线路2
			return false ? 
			a = 3 :
			a = 4
		}
	```
	
	
#### 参考
[1.jQuery源码学习之七](http://blog.csdn.net/hdchangchang/article/details/38331455)

#### 2.判断是否是window对象
```
	function isWindow(obj){
		return obj.window = obj;
	}
```
这个方法很不健壮，我们可以创造出一个假的对象出来。

**?:**	我们为什么要判断一个对象是否为window对象呢？意义何在？

参考:		
[1.为什么设计window.window?](https://www.zhihu.com/question/23535540?sort=created)

****

### P15 
#### 1.jQuery.isNaN()
在jQuery 1.7之后，大神用isNumberic方法代替了NaN方法。这个方法不仅对数字有效，而且对所有长得像数字的字符串也有效~

```
	jQuery.isNumberic = function(obj){
			//条件1 是否是NaN 如果是 立马输出false
			//条件2 判断这个数是不是在js的数字范围内~
			return !isNaN(parseFloat(obj)) && isFinite(obj);
		}
	
	//demo
	isNumberic(1) //输出true;
	isNumberic('1') //输出true

```

>>需要理解的点：		
1. psrseFloat的转换；		
需要再去理解parseFloat这个函数。	
2.isFinite函数的源码	
为什么输入isFinite('1')输出的也是true?

到了2.1版本，更加“飘逸”了。

```

	jQuery.isNumberic = function(obj){
		
			return obj - parseFloat(obj) >= 0;
		}

```

>>知识点：		
>>1. js的隐式转换

## 1.6 domReady
### P18
#### 1.为什么需要domReady机制？
通常我们写的js脚本需要在html文本的DOM和相应资源加载完毕之后才能执行。试想如果一个页面包含100张图片，网速又很慢的情况，如果没有加载完这些图片资源，那么绑定的事件就无法生效，会“假”死一会。所以我们需要一个事件，在dom节点加载完成之后就将js脚本执行绑定。这个事件就是DomContentLoaded事件，或者也可以说是domReady事件。对于高级的浏览器来说，他们是有这个属性的。		

```
	if (document.addEventListener) {
		document.addEventListener( "DOMContentLoaded", completed, false );
 	}
```	

然而对于NB的IE或者说低版本的IE，如何去做兼容则是个问题。	

相关参考文章：		
[《谈谈DOMContentLoaded：Javascript中的domReady引入机制》](https://www.cnblogs.com/horve/p/4092064.html)

#### 2.各框架对于兼容性的尝试：
- IE模拟DOMContentLoaded

```
	function IEContentLoaded(w,fn) {
			var d = w.document, done = false;
			//只执行一次的回调函数Init
			init = function(){
				if(!done){
					done = true;
					fn();
				}
			};
			(function(){
				try{
					// 通过IE中的document.documentElement.doScroll(‘left’)来判断DOM树是否创建完毕
					// dom树创建完之前叫调用doScroll会抛出错误
					d.documentElement.doScroll('left');
				}catch(e){
					//延迟使用一次
					//这里的arguments.callee指向的是闭包里的匿名函数。自己再执行一次。如果没有false，那么就过了~
					setTimeout(arguments.callee,50);
					return;
				}
				//前面抛出错误会阻塞init的执行，没有错误的话表示DOM数已经创建完毕，马上执行Init
				init();
			})();
			//监听document的加载状态
			d.onreadystatechange = function(){
				if(d.readyState == 'complete'){
				d.onreadystatechange = null;
				init();
				}
			}
		}
```

>> 相关知识点：		
>> arguments.callee 

