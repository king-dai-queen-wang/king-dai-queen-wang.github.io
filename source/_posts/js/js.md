---
layout: '[material]'
title: js高级
date: 2018-10-12 21:44:39
tags:
categories: JS
---
### 数据类型

* 基本(值)类型
    * String
    * Number
    * boolean
    * undefined(定义了没有赋值，初始化undefined)
    * null(在变量初始化使用，结束前用null来把之前指向的数据让垃圾回收了)
    * Symbol
* 对象（引用）类型
    * Object
    * Array（特殊对象,可以执行）
    * Function（特殊对象，有数字下标的属性，内部数据是有序的 ）

* 判断
    * typeof: 只会返回这几种基本数据类型的string表达（"number", 'string', 'boolean', 'undefined', 'object', 'symbol', 'function','）
       * 不能判断null与Object，Array与Object，都会返回'object'
    * instanceof: 判断对象的具体类型
    * ===
### 内存
* 一块内存包含两个数据
    * 内部存储的数据（一般数据和地址数据）
    * 内存地址值数据
* 内存分类
    * 栈： 全局变量，局部变量（空间较小）
    * 堆: 对象（空间较大）
* 数据变量内存的关系
    * var a = '123'; 即栈内存里存了 a = ‘123’；
    * var b = {name: 123} 即栈内存里存了{name：123}的地址值比如0x12，即栈存了b=  0x123;
    * 即赋值的时候占内存都存了变量的值，只是这个值有可能是基础类型（值），有可能是引用类型（地址值），
### 原型
https://www.bilibili.com/video/BV1tt41147MM?p=19
   1. * 任何函数（类也是Function）都有prototype属性（称为显式原型）,默认指向空对象（即原型对象）但Object不符合Object.prototype instanceof Object 为false；还有__proto__属性，因为所有的function（）{} 都相当于new Function出来的
       * 原型对象（即prototype指向的对象）中有一个属性constructor，指向函数对象自己
       * 每个实例对象都有一个__proto__,称为隐式原型
       * Function/类的prototype 是定义时候加的，而实例对象的__proto__是new的时候加的
       * 实例对象的__proto__和其Function/类 的prototype 都指向同一个东西，（原型对象），其内存栈里存了原型对象的地址值，
       * 所有的函数的__proto__都是一样的var fn = new Function(),Function = new Function(),Object 也是Function（  Object.__proto__ === Function.__proto__）
       * 所有函数都是Function 的实例（包括Function本身）Function.__proto__ === Function.prototype
       * Object原型对象是原型链的尽头
       * 读取对象属性值的时候，会自动到原型链查找，设置对象属性值的时候不会查找原型链，如果当前对象没有此属性，则直接添加该属性
   
   https://www.bilibili.com/video/BV1tt41147MM?p=21
   2.  * instanceof: A(实例对象) instanceof B(构造函数),如果B函数的显式原型对象在A对象的（隐式__proto__）原型链上，返回true，否则返回false
### 执行上下文
1.全局执行上下文
   * 在执行全局代码前将window确定为全局执行上下文
   * 对全局变量进行预处理
       * var定义的全局变量 ==》 undefined，添加为window的属性
       * function声明的全局函数 ==> 赋值（fun）， 添加为window的方法
       * this ==> 赋值（调用函数的对象）
   * ==》 开始执行函数体代码
   
2.函数执行上下文
   * 在调用函数之前，准备执行函数体之前，创建对应的函数执行上下文对象（虚拟的，存在与栈中）
   * 对局部数据进行预处理
        * 形参变量 ==》 赋值（实参） ==》 添加为执行上下文发属性
        * arguements =》 赋值（实参列表），添加为执行上下文的属性
        * var 定义的局部变量 ==》 undefined，添加为执行上下文的属性
        * function声明的函数 ==》 赋值（fun），添加为执行上下文的方法
        * this =》 赋值（调用函数的对象）
        * ===》开始执行函数体代码
        
3.作用域与执行上下文的区别
   * 区别1：
        * 全局作用域之外，每个函数都会创建自己的作用域，作用域在函数定义时候就已经确定了。而不是在函数调用时候
        * 全局执行上下文环境是在在全局作用域确定之后，js代码马上执行之前创建
        * 函数执行上下文环境是在调用函数时候，函数体代码执行之前创建
   * 区别2：
        * 作用域是静态的，只要在函数定义好了就一直存在。且不会变化。
        * 上下文环境是动态的，调用函数时候创建，函数调用结束时候上下文环境就被释放
   * 联系
        * 上下文环境（对象）是从属于所在的作用域
        * 全局上下文环境 ==》 全局作用域
        * 函数上下文环境 === 》 对应函数作用域 
    
###闭包
1. 如何产生闭包？

        当一个嵌套的内部函数（子）函数引用了外部函数（父）函数的变量（函数）时，就产生了闭包
2. 闭包是什么？

        使用chrome查看： 
        * 闭包是嵌入的函数
        * 包含被引用变量（函数）的对象（）
        * 注意： 闭包存在与嵌套的内部函数中
3. 产生闭包的条件？
 
        * 函数嵌套
        * 内部函数引用了外部函数的数据（变量/函数）
```js
    function f1() {
        var a = 2;
        function f2() {
            a++;
            console.log(a);
        }
    }
     f1();
    // f1 嵌套了f2，而且f2中的a引用了父f1中的a，形成闭包
    // 使用函数内部的变量在函数执行完成后，仍然存活在内存中（延长了局部变量的生命周期）
    // 在函数外部可以操作（读写）到函数内部的数据（变量/函数）

```
