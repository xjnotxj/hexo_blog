---
title: 从重置 input file 标签中看 jQuery 的  .val() 和 .attr(“value”) 区别
date: 2016-06-10 21:28:00
tags: 前端
---
  
## 背景：

在**清空 input file 标签选中值**时，分别用了以下方法，发现有的对有的错：

```
        【√】$("#file")[0].value = "";
        【√】$("#file")[0].value = null;

        【×】$("#file").attr("value","");
        【×】$("#file").attr("value",null);
        
        【√】$("#file").val("");
        【√】$("#file").val(null);
```

为什么同样是改变 value 值，得到的结果却不同呢？

## 释疑：

阅 stackoverflow《jQuery .val() vs .attr(“value”)》Question，里面解答如下：

>The gist is that .attr(...) is only getting the objects value at the start (when the html is created). val() is getting the object's property value which can change many times.

翻译过来就是：

>.val() 设置的是 input 的 value 属性，input 是 HTMLInputElement 的实例，value 是通过 setter 方法定义的，当被赋值时，就会把值写到 input 里面；而改变 value 属性的方法，实际上操作的是 dom 的 value 属性，会触发浏览器的 repaint，更新 input 的值。

---

#### Reference：

[ forms - jQuery .val() vs .attr("value") - Stack Overflow ](http://stackoverflow.com/questions/8312820/jquery-val-vs-attrvalue) 
 
