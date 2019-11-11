---
title: angular_ngModel
date: 2018-12-11 11:37:50
tags:
categories: JS
---
## ngModel

今天我们的colin大神给我们讲解了一下angular中ngModel双向绑定的实现原理。虽然听的不是很懂，但我觉得自己实现以下

* ControlValueAccessor

    ControlValueAccessor 是一个**连接表单模型和视图（DOM元素）的接口**，自定义的表单控件必须实现这个接口，它的作用是：

    把 **form模型中值映射到视图中**, <u>当视图发生变化时，通知 form directives 或 form controls</u>
    Angular 引入这个接口的原因是，不同的输入控件数据更新方式是不一样的。例如，对于我们常用的文本输入框来说，我们是设置它的 value 值，而对于复选框 (checkbox) 我们是设置它的 checked 属性。实际上，不同类型的输入控件都有一个 ControlValueAccessor，用来更新视图
    
    Angular 中常见的 ControlValueAccessor 有：
    
        DefaultValueAccessor - 用于 text 和 textarea 类型的输入控件
        
        SelectControlValueAccessor - 用于 select 选择控件
        
        CheckboxControlValueAccessor - 用于 checkbox 复选控件
