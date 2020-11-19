---
layout: '[material]'
title: ts 装饰器
date: 2018-10-12 21:44:39
tags:
categories: ts
---


## 装饰器

1. 装饰器简介

	> **装饰器**是一种特殊类型的声明。它可以**被附加在类声明**，**方法**，**属性**或**参数**上，**可以修改类的行为**。
	>
	> **装饰器本质就是一个*function***，可以注入到类，属性，方法，参数上来扩展其功能。常见的装饰器有：***类装饰器，属性装饰器，方法装饰器，参数装饰器***
	>
	> **装饰器的写法**： **普通装饰器（无法传参），装饰器工厂（有传参）**

2. 各种装饰器的介绍

	1. 类装饰器（普通装饰器,无法传参）

		```typescript
		function logClass(params: any) { // 普通装饰器
		    console.log(params) // 打印类的构造器 如下
		    /*
		    	ƒ HttpClient() {}
		    */
		    params.prototype.apiUrl = '动态扩展的属性值XXX';
		    params.prototype.run = function() {
		        console.log('动态扩展的run方法');
		    }
		}
		@logClass // 此次为类装饰器,
		class HttpClient{
		    constructor() {
		        
		    }
		    
		    getData() {
		        
		    }
		}
		
		var http = new HttpClient();
		console.log(http.apiUrl); // 动态扩展的属性值XXX
		console.log(http.run()); // 动态扩展的run方法
		```

		

		1. 类装饰器（装饰器工厂，可传参）

		```typescript
		function logClass(params: string) {
		    return function(target: any) {
		        console.log(params, target);
		        target.prototype.apiUrl = params;
		    }
		}
		
		@logClass("/hello") // 此次为类装饰器,
		class HttpClient{
		    constructor() {
		        
		    }
		    
		    getData() {
		        
		    }
		}
		
		var http = new HttpClient();
		console.log(http.apiUrl);
		```
		1.  类装饰器（重载构造函数例子）

		> 类装饰器表达式会在运行时候当函数被调用，类的构造函数作为其唯一的参数。**如果类装饰器返回一个值，它会使用提供的构造函数来替换类的声明。**

	```typescript
	function logClass(target: any) {
	    return class extends target{
	        apiUrl: any = '我是修改后的数据';
	        getData() {
	            this.apiUrl = this.apiUrl + '---->'
	            console.log(this.apiUrl);
	        }
	    }
	}
	@logClass
	class HttpClient{
		public apiUrl: string | undefined;
		constructor() {
			this.apiUrl = '我是构造函数里面的apiUrl'
		}
		getData() {
			console.log(this.apiUrl);
		}
	}
	var http = new HttpClient();
	http.getData();	// 我是修改后的数据---->
	```

2. 属性装饰器

	属性装饰器表达式会在运行时候当做函数被直接调用，传入下列2个参数：

	* **对于静态成员来说是类的构造函数，对于实例成员是类的原型对象**
	* **成员的名字**

```typescript
function logClass(param: any) {
    return function (target) {
        console.log('类装饰器--》', param, target);
        //  类装饰器--》 undefined<param> ƒ HttpClient() {}<target>
    
    }
}

function logProperty(param: any) {
    return function (target, attr) {
        target[attr] = param;
        console.log("属性装饰器--》", param, target, attr);
        // 属性装饰器--》 /logAttr <param> { url: "/logAttr"constructor: ƒ HttpClient()__proto__: Object  }<target> url<attr>
    }
}

function logStaticProperty(param: any) {
    return function (target, attr) {
        target[attr] = param;
        console.log("static属性装饰器--》", param, target, attr);
        //  static属性装饰器--》 /static api<param> ƒ HttpClient() {}<target> api<attr>
    }
}

@logClass()
class HttpClient{

    @logProperty("/logAttr")
    public url: string | undefined;

    @logStaticProperty('/static api')
    static api: string = '';

	constructor() {
	}
    getData() {
        console.log(this.url)
	}
}
var http = new HttpClient();
console.log(http.getData()); // /logAttr
```

3. 方法装饰器

	它会**被应用在方法的属性描述符上**，可以**监视，修改或者替换方法定义**。

	**方法装饰会在运行时候**传入下列3个参数：

	* **对于静态成员来说是类的构造函数，对于实例成员是类的原型对象，。**
	* **成员的名字**
	* **成员的属性描述符**

```

```

