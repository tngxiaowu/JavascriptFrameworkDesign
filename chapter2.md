#Chapter 2
## P21
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