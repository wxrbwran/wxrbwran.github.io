---
title: 'Javascript 数据结构:栈'
date: 2016-09-30 17:03:14
tags: 
	- javascript
---
>  栈是一种遵从后进先出(LIFO)原则的有序集合。新添加的或待删除的元素都保存在栈的 末尾,称作栈顶,另一端就叫栈底。在栈里,新元素都靠近栈顶,旧元素都接近栈底。
在现实生活中也能发现很多栈的例子。例如,下图里的一摞书或者餐厅里堆放的盘子。

### 栈的创建
栈的方法:
  - push(element(s)):添加一个(或几个)新元素到栈顶。 
  - pop():移除栈顶的元素,同时返回被移除的元素。 
  - peek():返回栈顶的元素,不对栈做任何修改(这个方法不会移除栈顶的元素,仅仅返回它)。
  - isEmpty():如果栈里没有任何元素就返回true,否则返回false。 
  - clear():移除栈里的所有元素。 
  - size():返回栈里的元素个数。这个方法和数组的length属性很类似。
使用数组来保存栈里的元素。	
{% codeblock Stack lang:javascript %}
	funcion Stack(){
		var items = [];
		this.push = function(ele){
			items.push(ele)
		};
		this.pop = function(){
			return items.pop()
		};
		this.peek = function(){
			return items[items.length-1]
		};
		this.isEmpty = function(){
			return items.length ===0
		};
		this.clear = function(){
			items.lenth=0
		};
		this.size = function(){
			return items.length
		};
		this.print = function(){
			console.log(items.toString())
		}
	}
{% endcodeblock %}
  