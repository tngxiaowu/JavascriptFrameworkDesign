# chapter 1 

## 1.4 数组化
###p6
jQuery中将类数组转换为数组的方式 

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