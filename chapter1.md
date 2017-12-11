# chapter 1 

## 1.4 数组化
###p6
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
