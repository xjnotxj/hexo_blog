---
title: 让 jQuery UI draggable 适配移动端
date: 2016-06-01 23:46:00
tags: 前端
categories: 技术
---
  
## 背景：
在移动端，本人要实现对某个元素的拖动，想到使用 jQuery UI 的 draggable 功能。但是发现此插件的拖动**只支持 PC 端，不支持移动端**。

## 原因：
原始的 jQuery UI 里，都是 **mousedown、mousemove、mouseup** 来描述拖拽和**鼠标**的点击事件，而在移动端里，肯定要新添 **touchstart、touchmove、touchend** 来描述拖拽和**手指**的点击事件。

## 实现demo：

```xml
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0;" name="viewport">
    <title>jQuery UI draggable 适配移动端</title>
</head>
<body>
<img id="img" src="http://placehold.it/200x100">

<script src="//cdn.bootcss.com/jquery/3.0.0-beta1/jquery.js"></script>
<script src="//cdn.bootcss.com/jqueryui/1.11.4/jquery-ui.min.js"></script>
<script>

    // jQuery UI draggable 适配移动端

    var moveFlag = 0; // 是否移动的flag
    // /iPad|iPhone|Android/.test( navigator.userAgent ) &&
    (function ($) {
        var proto = $.ui.mouse.prototype, _mouseInit = proto._mouseInit;
        $.extend(proto, {
            _mouseInit: function () {
                this.element.bind("touchstart." + this.widgetName, $.proxy(this, "_touchStart"));
                _mouseInit.apply(this, arguments);
            }, _touchStart: function (event) {
                this.element.bind("touchmove." + this.widgetName, $.proxy(this, "_touchMove")).bind("touchend." + this.widgetName, $.proxy(this, "_touchEnd"));
                this._modifyEvent(event);
                $(document).trigger($.Event("mouseup"));
                //reset mouseHandled flag in ui.mouse
                this._mouseDown(event);
                //console.log(this);
                //return false;

                <!- touchStart do something -->
                console.log("i touchStart!");

            }, _touchMove: function (event) {
                moveFlag = 1;
                this._modifyEvent(event);
                this._mouseMove(event);

                <!- touchMove do something -->
                console.log("i touchMove!");

            }, _touchEnd: function (event) {
                // 主动触发点击事件
                if (moveFlag == 0) {
                    var evt = document.createEvent('Event');
                    evt.initEvent('click', true, true);
                    this.handleElement[0].dispatchEvent(evt);
                }
                this.element.unbind("touchmove." + this.widgetName).unbind("touchend." + this.widgetName);
                this._mouseUp(event);
                moveFlag = 0;

                <!- touchEnd do something -->
                console.log("i touchEnd!");

            }, _modifyEvent: function (event) {
                event.which = 1;
                var target = event.originalEvent.targetTouches[0];
                event.pageX = target.clientX;
                event.pageY = target.clientY;
            }
        });
    })(jQuery);

</script>
<script>
    // my js
    $("#img").draggable();
</script>
</body>
</html>
```

![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160601233350492-1691798716.png)

---

#### Reference：

[ jQuery Ui Draggable 在移动端浏览器不起作用解决方案](http://www.ithao123.cn/content-766716.html) 