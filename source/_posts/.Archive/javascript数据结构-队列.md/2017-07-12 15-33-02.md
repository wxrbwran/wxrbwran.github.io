---
title: 'Javascript数据结构:队列'
date: 2016-10-02 10:15:27
tags:
    - Javascript
    - 队列
---
>  队列是遵循FIFO(First In First Out,先进先出,也称为先来先服务)原则的一组有序的项。 队列在尾部添加新元素,并从顶部移除元素。最新添加的元素必须排在队列的末尾。
在现实中,最常见的队列的例子就是排队，在电影院、自助餐厅、杂货店收银台,我们也都会排队。排在第一位的人会先接受服务。

### 创建队列
队列方法
- enqueue(element(s)):向队列尾部添加一个(或多个)新的项。
- dequeue():移除队列的第一(即排在队列最前面的)项,并返回被移除的元素。 
- front():返回队列中第一个元素——最先被添加,也将是最先被移除的元素。队列不做任何变动(不移除元素,只返回元素信息——与Stack类的peek方法非常类似)。 
-  isEmpty():如果队列中不包含任何元素,返回true,否则返回false。 
- size():返回队列包含的元素个数,与数组的length属性类似。
{% codeblock Queue lang:javascript %}
	funcion Queue(){
		var items = [];
		this.enqueue = function(ele){
			items.push(ele)
		}
		this.dequeue = function(){
			return items.unshift();
		}
		this.front = function(){
			return items[0]
		}
		this.isEmpty = function(){
			return items.length===0
		}
		this.size = function(){
			return items.length
		}
		this.print = function(){
			console.log(items.toString())
		}
	}
{% endcodeblock %}