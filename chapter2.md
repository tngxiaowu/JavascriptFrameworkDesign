# Chapter 2
## P23
### 1.contains方法(判断一个字符串中是否包含另一个字符串)
```
	function contains(target,it){
			//其实就是使用了indexOf方法
			return target.indexOf(it) != -1;
		}

```


****

## P24
### 1.字符串repeat操作的多种方法
#### 1.1 方法1
```
	function repeat(target,n){
			return new Array(n+1).join(target);
	}
```

>> 知识点1:使用数组的join方法		
>> join方法将数组内的元素拼成一个字符串，它有一个参数，代表分隔符，如果里面有没传参，那么就默认为逗号。		
>> 知识点2：new Array构建			
>> new Array(n)创造了一个长度为n的数组(console.log出来的结果是[empty x n])。

#### 1.2 方法2
```
	function repeat(target,n){
			return Array.prototype.join.call({
				length: n+1
			},target);
		}
```
>> 知识点3：类数组对象		
>> 类数组对象可以**通过call/apply的方式使用数组的方法**。

#### 1.3 方法3
```
	var repeat = (function(){
			//利用闭包将join和obj缓存起来~
			var join = Array.prototype.join, obj = {};
			return function(target,n){
				obj.length = n+1;
				join.call(obj,target);
			}
		})()
```
		
*********


## P26
### 1.byteLen(计算字符串所字节长度)
#### 1.1 

思路：先计算字符串的长度，然后遍历字符串中的每一个字符串，如果它的unicode编码大于255，那么字节长度加一。

```
	function byteLen(str){
		var byteLength = str.length, i=0;
		//如果这里的str.length写成byteLength会产生问题
		for(; i< str.length; i++){
			if(str.charCodeAt(i)>255){
				byteLength++;
			}
		} 
		return length;
	}
```