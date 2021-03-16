# ts-Decorator

## 装饰器

1. 装饰器简介

    > 装饰器是一种特殊类型的声明。它可以被附加在类声明，方法，属性或参数上，可以修改类的行为。装饰器本质就是一个function，可以注入到类，属性，方法，参数上来扩展其功能。常见的装饰器有：类装饰器，属性装饰器，方法装饰器，参数装饰器装饰器的写法： 普通装饰器（无法传参），装饰器工厂（有传参）

    **注意： 所有的装饰器只有在运行时候执行(未实例化之前)!!!**

2. 各种装饰器的介绍
    1. 类装饰器（普通装饰器,无法传参）

        ````typescript
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

        1. 类装饰器（重载构造函数例子）

        > 类装饰器表达式会在运行时候(未实例化之前)当函数被调用，类的**构造函数作为其唯一的参数**。如果类装饰器返回一个值，它会使用提供的构造函数来替换类的声明。

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
    http.getData(); // 我是修改后的数据---->
    ```

3. 属性装饰器

    属性装饰器表达式会在运行时候**(未实例化之前)**当做函数被直接调用，传入下列2个参数：

    - **对于静态成员来说是类的构造函数，对于实例成员是类的原型对象**
    - **成员的名字**

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

4.1 方法装饰器(1)

它会**被应用在方法的属性描述符上**，可以**监视，修改或者替换方法定义**。

**方法装饰会在运行时候(未实例化之前)**传入下列3个参数：

- **对于静态成员来说是类的构造函数，对于实例成员是类的原型对象，。**
- **成员的名字**
- **成员的属性描述符**

```tsx
function logMethod(params: any) {
	return function(target: any, methodName: string, desc: any) {
		console.log(params);
		console.log(target, methodName, desc);

		target.apiUrl = 'xxx';
		target.run = function() {
			console.log('this is run method')
		}
	}
}

class HttpClient{
    public url: string | undefined;
    static api: string = '';

    constructor() {
    }

		@logMethod('log method')
    getData() {
        console.log(this.url)
    }
}

运行结果：
------------------------------
log method

**对于静态成员来说是类的构造函数，对于实例成员是类的原型对象，。**
Object{
	getData: f()
	constructor: f HttpClient
	__proto__: Object
}

**成员的名字**
"getData"

**成员的属性描述符**
Object{
	configurable: true
	enumerable: true
	value: f() --> getData 方法
	writeable: true
	__proto__
}
--------------------------------

const aa = new HttpClient();
aa.run(); // this is run method
console.log(aa.apiUrl); // xxx
```

4.2  方法装饰器（2）

方法装饰器可以修改被装饰的方法

例子： **把一个无传参的方法a 改成要接受string参数的方法b：**

```tsx
function Get(param: any) {
	return function(target: any, methodName: any, desc: any) {
		const oldMethod = desc.value;  // desc.value 是被修饰的function
		desc.value = function(...args: any[]) {
			args = args.map(v => String(v));

			console.log(args)；

			// 如果要替换原来被修饰的方法，则不用写下面这句
			oldMethod.apply(this, args);
		}
	}
	
}

class HttpClient{
    public url: string | undefined;
    static api: string = '';

    constructor() {
    }

		@Get('http://123.com')
    getData() {
        console.log('我是getData方法')
    }
}

const http = new HttpClient();

http.getData('hello' , 'dwww' ， 123);
// 'hello' 'dwww' '123' '我是getData方法'
```

 5. 方法参数装饰器

**参数装饰器表达式会在运行时候(未实例化之前)当做函数被调用**，可以使用参数装饰器为类的原型增加一些元素数据，传入以下3个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员来说是类的原型对象
2. 方法的名字
3. 参数在函数参数列表的索引

```tsx
function logParams(param: any) {
	return function(target: any, methodName: string, paramIndex: number) {
		console.log(param， target, methodName, paramIndex)
	}
}

class HttpClient{
    public url: string | undefined;
    static api: string = '';

    constructor() {
    }

    getData(@logParams('uuid') uid: any) {
        console.log('我是getData方法,传入UUID: '+ uid)
    }
}

const http = new HttpClient();

// 对象实例过后，调用getData方法后，会触发logParams 装饰器函数，执行console.log(target, paramsName, paramIndex)
http.getData(123);

```

### 装饰器的执行顺序

```tsx
function logClass1(params: any) {
	return function(target: any) {
		console.log('类装饰器1', params, target)
	}
}

function logClass2(params: any) {
	return function(target: any) {
		console.log('类装饰器2', params, target)
	}
}

function logAttr1(params: any) {
	return function(target: any, attrName: string, desc: any) {
		console.log('属性装饰器1', params, target, attrName, desc)
	}
}
function logAttr2(params: any) {
	return function(target: any, attrName: string, desc: any) {
		console.log('属性装饰器2', params, target, attrName, desc)
	}
}

function logMethod1(params: any) {
	return function(target: any, methodName: string, desc: any) {
		console.log('方法装饰器1', params, target, methodName, desc)
	}
}
function logMethod2(params: any) {
	return function(target: any, methodName: string, desc: any) {
		console.log('方法装饰器2', params, target, methodName, desc)
	}
}
function logParams1(params: any) {
	return function(target: any, methodName: string, attrIndex: any) {
		console.log('方法参数装饰器1', params, target, methodName, attrIndex)
	}
}

function logParams2(params: any) {
	return function(target: any, methodName: string, attrIndex: any) {
		console.log('方法参数装饰器2', params, target, methodName, attrIndex)
	}
}

@logClass1('l')
@logClass2('k')
class HttpClient{
		@logAttr1('b')
		@logAttr2('a')
    public url: string | undefined;
    static api: string = '';

    constructor() {
    }

		@logMethod1('d')
		@logMethod2('c')
		setData() {

		}

		@logMethod1('h')
		@logMethod2('g')
    getData(@logParams1('f') uid: any, @logParams2('e') attr2) {
        console.log('我是getData方法,传入UUID: '+ uid)
    }

@logMethod1('j')
		@logMethod2('i')
		setData1() {

		}
}
```

 顺序： 属性 → 方法 → 方法参数 → 类 （若有多个装饰器装饰同一个.则按照离被装饰的属性 从近到远执行）
