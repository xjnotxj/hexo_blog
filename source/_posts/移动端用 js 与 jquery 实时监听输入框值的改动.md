---
title: 移动端用 js 与 jquery 实时监听输入框值的改动
date: 2016-06-10 22:01:00
tags: 前端
categories: 技术
---
  
## 背景：

在一次移动端 H5 开发中，需要监听输入框值的实时变动。

onchange 事件肯定抛弃，因为只能失去焦点才触发。

而 keyPress 在 Android 可以触发，iOS 不可以。

又不想用 Android 和 iOS 都可以触发的 keyDown 和 keyUp。

于是，百度出了新东西：**oninput**！【需要配合 **propertychange**，兼容 IE9 以下版本】

## 用法:

#### JS:

```
if(isIE) 
{ 
　　document.getElementById("input").onpropertychange = keys(); 
} 
else 
{ 
　　document.getElementById("input").addEventListener("input", keys, false);
}
```

#### jQuery：

```
<!- input propertychange -->
$('input').bind('input propertychange', function() { 
　　//进行相关操作 
});
```

> 下面的写法**不支持**：
```xml
<!- N/A -->
$('input').input(function() {
　　//进行相关操作 
});
```
> 

---

#### Reference：

[ js与jquery实时监听输入框值的oninput与onpropertychange方法](http://www.jb51.net/article/60789.htm) 