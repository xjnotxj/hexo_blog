---
title: 移动端上传照片 预览 + Draw on Canvas's Demo（解决 iOS 等设备照片旋转 90 度的 bug）
date: 2016-06-11 22:12:00
tags: 前端
categories: 技术
---

## 背景：

本人的一个移动端 H5 项目，需求如下:

> `需求一`：手机相册选取或拍摄照片后在页面上**预览**
> `需求二`：然后**绘制在 canvas** 画布上

这里，我们先看一个 demo:
{% jsfiddle 83qfqpk8 html,css,result %}

> `需求一`： `drawTempPhoto` 方法
> `需求二`： `drawPhoto` 方法

### 操作步骤：
![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160611221139058-105612097.png)
1.点击选择文件，拍摄一张照片，此时"预览："文字下会显示你刚才拍摄的照片
2.再点击"draw on Canvas"，该按钮下的画布会绘制你刚才拍摄的照片

### 正常的结果：
![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160611221140824-608883375.png)

## 正文：
### 一、让 input file 支持 **拍照 + 相册** 选取

```php
<input accept="image/*" type="file" id="file" />
//有一些特殊的安卓机还需要加上 capture="camera" 属性才能支持拍照
```

### 二、`需求一`的**预览**功能使用 html5 提供的新 API：**FileReader**

>介绍：
 FileReader 接口提供了一个异步 API，使用该 API 可以在浏览器主线程中异步访问文件系统，读取文件中的数据。
>
 API 文档：
 https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader
>
 兼容性：
 主流浏览器都支持。除了 IE 有点问题，详情看下图：
![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160611221142027-1855846622.png)
>

#### 使用方法（使用 `drawTempPhoto` 方法实现`需求一`）：

```php
//绘制照片
function drawTempPhoto() {

    //检验是否为图像文件
    var file = document.getElementById("file").files[0];
    if (!/image\/\w+/.test(file.type)) {
        alert("看清楚哦，这个需要图片！");
        return false;
    }
    var reader = new FileReader();
    //将文件以 Data URL 形式读入页面
    reader.readAsDataURL(file);
    reader.onload = function (e) {

        //预览效果
        var img = $("#photo")[0];
        //加载图片，此处的 this.result 为 base64 格式
        img.src = this.result;

    }

}
```
>![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160611221143355-1662776781.png)
>在本人的三星 note5 的手机中，`需求一`会出现照片预览逆时针旋转 90 度的 bug（其实用下面介绍的的 exif.js 方法，可以知道 iOS 和三星所拍的照片 Orientation 值都是 6，而前者做了预览的修正，后者则直接显示了出来，导致了旋转）。

#### 解决方案:

***判断是否为三星手机设备，然后把预览的图片顺时针旋转 90 度恢复正常（例如可以用 canvas 技术模拟预览效果）。***

于是修改的 `drawTempPhoto` 方法如下【line：21-27】：

```php
//绘制照片
    function drawTempPhoto() {

        //检验是否为图像文件
        var file = document.getElementById("file").files[0];
        if (!/image\/\w+/.test(file.type)) {
            alert("看清楚哦，这个需要图片！");
            return false;
        }
        var reader = new FileReader();
        //将文件以 Data URL 形式读入页面
        reader.readAsDataURL(file);
        reader.onload = function (e) {

            //预览效果
            var img = $("#photo")[0];
            //加载图片，此处的 this.result 为 base64 格式
            img.src = this.result;
            img.onload = function(){

                //获取照片的拍摄方向
                var orient = getPhotoOrientation(img);
                alert("orient1:" + orient);
                //判断是否是三星手机
　　          // if (isSamsung) {
                      // 做旋转的适配……
                // }

            };

        }

    }
```

### 三、`需求二`绘制到 Canvas，使用 exif.js 解决 iOS（包括三星 note5）等一些手机照片旋转 90 度的 bug

在 iOS 上，表现如下：
![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160611221144371-343240701.png)

可见，在 **iOS** 上第一张图正常，而第二张图**逆时针旋转了 90 度**，而其他的**安卓**两步都是**正常的显示**。

那我们来看看 **exif.js** 是什么？

>介绍：
Exif.js 提供了 JavaScript 读取图像的原始数据的功能扩展，例如：**拍照方向**、相机设备型号、拍摄时间、ISO 感光度、GPS 地理位置等数据。
>
兼容性：
EXIF 数据主要来自拍摄的照片，多用于移动端开发，PC 端也会用到，此插件兼容主流浏览器，IE10 以下不支持。
>
下载地址：
https://github.com/exif-js/exif-js
>

这里我们知道了 exif.js 其实是获取一张拍摄照片的元信息的，进而获取它最初的拍照方向，这很可能跟 iOS 图片旋转的 bug 有关。

而 exif.js 提供的一张照片的 **Orientation** 属性如下：
![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160611221145371-963458118.gif)

#### 使用方法：

1.我首先引入 `exif.js`：

```php
<script src="./js/exif.js"></script>
```

2.然后封装成 `getPhotoOrientation` 方法：

```php
//获取照片的元信息（拍摄方向）
function getPhotoOrientation(img){
  var orient;
  EXIF.getData(img, function () {
      orient = EXIF.getTag(this, 'Orientation');
  });
  return orient;
}
```

3.分别在操作步骤一选择图片和操作步骤二 draw on Canvas，Alert 照片的 Orientation 值，本人的测试机有限，请见谅，结果如下：

|手机型号|Orientation值|
|--------|--------|
|iphone 6S|**6**|
|小米4S|undefined|
|华为荣耀4A|1|
|PC|undefined|

可以看到，小米 4S 和 PC 都是 undefined，表示图片没有拍摄方向或者根本没记录，华为荣耀 4A 是 1，表示的是正确的方向，以上都没问题。但 iphone 6S 为 6！**所以之所以出现了图片的旋转，是因为它本身的 Orientation 就不正常！！**

***因此解决这个 bug 的思路是：获取到照片拍摄的方向，对 ios 照片进行角度旋转修正。***

#### 解决方案：

修改后的 demo:
{% jsfiddle k3z5ev26 javascript,html,css,result %}

除了上面引入的 `exif.js` 和添加的 `getPhotoOrientation` 方法，我又修改了 `drawPhoto` 方法：

1.旧 `drawPhoto` 方法：

```php
//绘制照片
function drawPhoto(photo, x, y, w, h) {

    var canvas = document.getElementById("canvas");
    if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        //draw on Canvas
        var img = new Image();
        img.onload = function () {

            var canvas_w = Number(ctx.canvas.width);
            var canvas_h = Number(ctx.canvas.height);

            // 执行 Canvas 的 drawImage 语句
            ctx.drawImage(img, x, y, w, h);

        }
        img.src = photo.src; // 设置图片源地址
    }
}
```

2.新 `drawPhoto` 方法【line：4-6，19-30】：
```php
//绘制照片
function drawPhoto(photo, x, y, w, h) {

    //获取照片的拍摄方向
    var orient = getPhotoOrientation(photo);
    alert("orient2:"+orient);

    var canvas = document.getElementById("canvas");
    if (canvas.getContext) {
        var ctx = canvas.getContext("2d");

        //draw on Canvas
        var img = new Image();
        img.onload = function () {

            var canvas_w = Number(ctx.canvas.width);
            var canvas_h = Number(ctx.canvas.height);

            //判断图片拍摄方向是否旋转了 90 度
            if (orient == 6) {
                ctx.save();//保存状态
                ctx.translate(canvas_w / 2, canvas_h / 2);//设置画布上的(0,0)位置，也就是旋转的中心点
                ctx.rotate(90 * Math.PI / 180);//把画布旋转 90 度
                // 执行 Canvas 的 drawImage 语句
                ctx.drawImage(img, Number(y) - canvas_h / 2, Number(x) - canvas_w / 2, h, w);//把图片绘制在画布 translate 之前的中心点，
                ctx.restore();//恢复状态
            } else {
                // 执行 Canvas 的 drawImage 语句
                ctx.drawImage(img, x, y, w, h);
            }

        }
        img.src = photo.src; // 设置图片源地址
    }
}
```

运行结果如下：
![](http://images2015.cnblogs.com/blog/896608/201606/896608-20160611221147277-517861637.png)

完美~
 
