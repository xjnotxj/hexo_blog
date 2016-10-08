---
title: Web 中的触摸（touch）与手势（gesture）事件
date: 2016-06-08 17:46:00
tags: 前端
categories: 技术
---
  
## 一.触摸事件（TouchEvent）

#### 事件：

- **touchstart** : 当手指触摸屏幕时触发；即使已经有一个手指放在了屏幕上也会触发

- **touchmove** : 当手指在屏幕上滑动时连续的触发。调用 preventDefault() 可阻止滚动

- **touchend** : 当手指从屏幕上移开时触发

- **touchcancel** : 当系统停止跟踪触摸时触发。关于此事件的确切触发事件，文档中没有明确说明


#### 事件属性：

- e.**touches** : 当前屏幕上所有触摸点的集合列表

- e.**targetTouches** : 绑定事件的那个结点上的触摸点的集合列表

- e.**changedTouches** : 触发事件时改变的触摸点的集合

 

事件属性返回**数组**的每个 Touch 对象包含下列属性：
 
- clientX : 触摸目标在视口中的 X 坐标。

- clientY : 触摸目标在视口中的 Y 坐标。

- identifier : 表示触摸的唯一 ID。

- pageX : 触摸目标在页面中的 x 坐标。

- pageY : 触摸目标在页面中的 y 坐标。

- screenX : 触摸目标在屏幕中的 x 坐标。

- screenY : 触摸目标在屏幕中的 y 坐标。

- target : 触摸的 DOM 节点坐标。 

#### 例1.**单**个手指 touch

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0;" name="viewport">
    <title>single figure touch test</title>
    <style type="text/css">
        html, body {
            height: 100%;
            width: 100%;
            margin: 0;
            padding: 0
        }

        #canvas {
            position: relative;
            width: 100%;
            height: 100%;
        }

        .spirit { /* 方块的class名称*/
            position: absolute;
            width: 50px;
            height: 50px;
            background-color: red;
        }
    </style>
</head>

<body>

<div id="canvas"></div>

<script>

    // define global variable
    var canvas = document.getElementById("canvas"),
            spirit, startX, startY;

    // touch start listener
    function touchStart(event) {
        //防止手指的操作引起页面滑动
        event.preventDefault();
        if (spirit || !event.touches.length) return;
        var touch = event.touches[0];
        startX = touch.pageX;
        startY = touch.pageY;
        spirit = document.createElement("div");
        spirit.className = "spirit";
        //注意：一定要加‘px’单位
        spirit.style.left = startX + 'px';
        spirit.style.top = startY + 'px';
        canvas.appendChild(spirit);
    }
    // add touch start listener
    canvas.addEventListener("touchstart", touchStart, false);

    // touch move listener
    function touchMove(event) {
        //防止手指的操作引起页面滑动
        event.preventDefault();
        if (!spirit || !event.touches.length) return;
        var touch = event.touches[0],
                x = touch.pageX - startX,
                y = touch.pageY - startY;
        spirit.style.webkitTransform = 'translate(' + x + 'px, ' + y + 'px)';
    }
    // touch move listener
    canvas.addEventListener("touchmove", touchMove, false);

    // touch end listener
    function touchEnd(event) {
        if (!spirit) return;
        canvas.removeChild(spirit);
        spirit = null;
    }
    // touch end listener
    canvas.addEventListener("touchend", touchEnd, false);
</script>
</body>
</html>
```

#### 例2.**多**个手指 touch 

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0;" name="viewport">
    <title>multi figure touch test</title>
    <style type="text/css">
        html, body {
            margin: 0;
            padding: 0;
            height: 100%;
            width: 100%;
        }

        #canvas {
            width: 100%;
            height: 100%;
            position: relative;
        }

        .spirit {
            position: absolute;
            width: 70px;
            height: 70px;
        }
    </style>
</head>
<body>
<div id="canvas"></div>
</body>
<script type="text/javascript">
    var canvas = document.getElementById("canvas"),
            stacks = [{
                color: "red"
            }, {
                color: "blue"
            }, {
                color: "green"
            }, {
                color: "black"
            }, {
                color: "yellow"
            }, {
                color: "orange"
            }];

    function getEmptyStack() {
        var i = 0, s, l = stacks.length;
        for (; i < l; i++) {
            s = stacks[i];
            if (!s.touchId)
                return s;
        }
        return null;
    }

    function getStackByTouchId(touchId) {
        var i = 0, s, l = stacks.length;
        for (; i < l; i++) {
            s = stacks[i];
            if (s.touchId == touchId)
                return s;
        }
        return null;
    }

    function touchStart(e) {
        e.preventDefault();
        var touches = e.changedTouches,
                i = 0, l = touches.length, touch, stack;
        for (; i < l; i++) {
            touch = touches[i];
            stack = getStackByTouchId(touch.identifier);
            if (stack) return;
            stack = getEmptyStack();
            if (stack) {
                stack.touchId = touch.identifier;
                stack.spirit = document.createElement("div");
                stack.spirit.className = "spirit";
                stack.startX = touch.pageX;
                stack.startY = touch.pageY;
                stack.spirit.style.left = touch.pageX + "px";
                stack.spirit.style.top = touch.pageY + "px";
                stack.spirit.style.backgroundColor = stack.color;
                canvas.appendChild(stack.spirit);
            } else { // stack list is full
                return;
            }
        }
    }

    function touchMove(e) {
        e.preventDefault();
        var touches = e.targetTouches,
                i = 0, l = touches.length, touch, stack;
        for (; i < l; i++) {
            touch = touches[i];
            stack = getStackByTouchId(touch.identifier);
            if (stack) {
                var x = touch.pageX - stack.startX,
                        y = touch.pageY - stack.startY,
                        spirit = stack.spirit;
                spirit.style.webkitTransform = 'translate(' + x + 'px, ' + y + 'px)';
            }
        }
    }

    function touchEnd(e) {
        var touches = e.changedTouches,
                i = 0, l = touches.length, touch, stack;
        for (; i < l; i++) {
            touch = touches[i];
            stack = getStackByTouchId(touch.identifier);
            if (stack) {
                stack.touchId = null;
                canvas.removeChild(stack.spirit);
                stack.spirit = null;
            }
        }
    }

    //当触控被中断
    function touchCancel(e) {
        var touches = e.changedTouches,
                i = 0, l = touches.length, touch, stack;
        for (; i < l; i++) {
            touch = touches[i];
            stack = getStackByTouchId(touch.identifier);
            if (stack) {
                stack.touchId = null;
                canvas.removeChild(stack.spirit);
                stack.spirit = null;
            }
        }
    }

    canvas.addEventListener("touchstart", touchStart, false);
    canvas.addEventListener("touchmove", touchMove, false);
    canvas.addEventListener("touchend", touchEnd, false);
    canvas.addEventListener("touchCancel", touchCancel, false);
</script>
</html>
```

#### 例3.**单**个手指放下并滑动

1.手指滑动时，整个 page 会跟着移动，除非 **preventDefault** 了 body 的 touchmove
2.当手指停下来后， body 的 **onscroll** 将会触发


## 二.手势事件（GestureEvent）

**GestureEvent** 包含手指点击（click），轻拂（flick），双击（double-click），两只手指的分开/闭合（scale）、转动（rotate），手指分开比例（scale）和手指转动角度（rotation）信息。


#### 事件：

 - **esturestart** : 当有两根或多根手指放到屏幕上的时候触发
 - **gesturechange** : 当有两根或多根手指在屏幕上，并且有手指移动的时候触发
 - **gestureend** : 当倒数第二根手指提起的时候触发，结束gesture
 

#### 事件属性：

 - e.**rotation** : 两只手指的转动
 - e.**scale** : 两只手指的分开/闭合

#### 例1.依次放下两根手指

1.第一根手指放下，触发 touchstart
2.第二根手指放下，触发 gesturestart 
3.触发第二根手指的 touchstart 
4.立即触发 gesturechange 
5.手指移动，持续触发 gesturechange
6.第二根手指提起，触发 gestureend
7.触发第二根手指的 touchend 
**8.触发 touchstart ！注意，多根手指在屏幕上，提起一根，会刷新一次全局 touch！重新触发第一根手指的 touchstart**
9.提起第一根手指，触发 touchend 

#### 例2.让 element 随着你的两根手指运动而转动

```
e.target.style.webkitTransform = 'scale(' + e.scale + startScale + ') rotate(' + e.rotation + startRotation + 'deg)';
```

---

#### Reference：

 - [指尖下的 js ——多触式 web 前端开发之一：对于 Touch 的处理](http://www.cnblogs.com/pifoo/archive/2011/05/23/webkit-touch-event-1.html) 
 - [触摸与手势事件](https://www.w3cmm.com/javascript/touch.html) 