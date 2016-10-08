---
title: 响应式网页中，如何只用 CSS 实现 div 的高和宽保持固定比例
date: 2016-05-22 20:16:00
tags: 前端
---

## 引言：
  
如果 div 里是`<img>`，原生就支持。

```xml
.item img {
    float: left;
    margin:5%;
    <!- keystone -->
    width: 20%;
    <!- end -->
}
```
{% jsfiddle rg2uqjet result,html,css dark %}

## 正文：

#### 1.使用**媒体查询**

```xml
@media only screen and (min-width: 100px) and (max-width: 640px) {
   div{
      width: 100px;
      height: 100px;
   }
}
@media only screen and (min-width: 641px) and (max-width: 789px) {
   div{
      width: 200px;
      height: 200px;
   }
}
```


#### 2.利用 **padding-bottom** 是百分比值的话，参考的是父元素的 width 而不是 height

```xml
.item {
    float: left;
    background-color: darkslategrey;
    border: 1px solid black;
    margin: 5%;
    <!- keystone -->
    width: 20%;
    height: 0;
    padding-bottom: 20%;
    <!- end -->
}
```

{% jsfiddle 6bq6fL9r result,html,css dark %}
 

#### 3.使用 vw 属性，他是参考整个 viewport（视窗）的宽度【**vw、vh、vmin、vmax** 是 css3 新属性，支持主流浏览器且 IE10 以上】

```xml
item {
    float: left;
    background-color: darkslategrey;
    border: 1px solid black;
    margin: 5%;
    <!- keystone -->
    width: 20vw;
    height: 20vw;
    <!- end -->
}
```

{% jsfiddle ze7mbd4w result,html,css dark %}

---

#### Reference：

[ CSS 中 margin-top / bottom( padding-top/bottom ) 百分比为何以最近的块级祖先元素的宽度而不是高度作计算？](https://www.zhihu.com/question/20983035)
 
