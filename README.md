提到二维码，无论是专题、官网、公众号铺天盖地，遍布在生活的各个角落，
尤其我大鹅厂，连饮水机上都贴个二维码鼓励大家多喝水，爬楼梯的楼门上也贴着鼓励大家锻炼身体，
这福利，啧啧啧，试问还有谁？有谁？谁？与之媲美。

好了，不拍马屁了，即使拍再多年终奖也没多发，年会也是P都没中~>_<~

那么二维码有什么好说的呢？先给大家来个[DEMO](http://mx.qq.com/zorajianglab111/qrcode/qrcode.html)

![qrcode页面](http://mx.qq.com/zorajianglab111/qrcode/images/qrcode.png "有生产日期有生产厂商不确定有没有质量合格证的2有产品")

打开页面之后无论你按哪个二维码长按识别，识别出来都是左侧二维码。

检查下代码

HTML

```
<ul class="qrbox" id="qrbox">
  <li class="item">
    <img src="images/mweibo.jpg" alt="微博二维码">
    <h3 class="title">微博二维码</h3>
  </li>
  <li class="item">
    <img src="images/mwx.jpg" alt="微信二维码">
    <h3 class="title">微信二维码</h3>
  </li>
</ul>
```
CSS

```
*{padding:0;margin:0;-webkit-user-select: none; -moz-user-select: none; -ms-user-select: none; user-select: none; }
ul{list-style:none;}
.qrbox {overflow:hidden;padding:10px 0;text-align: center;}
.qrbox img{ width:100%; }
.qrbox .item{float:left; width:35%; margin-left:10%;}
```

并无异常。这时候经同事点播，微信识别二维码的原理是 *截屏逻辑* 。
就是你长按的时候相当于将当前手机屏幕截屏，识别截屏后的图片，这样一张图片有两个二维码图的时候当然只会识别出一个。

因此要解决这个问题就要保证在同一截屏内只存在 _一个二维码_ 。

解决方案：

1. 加大间距保证截屏内只有一个二维码
  
    此种方案可以通过加padding或者margin的方式，但并不是我们觉得完美的方式。

2. 识别时隐藏掉其他的二维码
  
    此时又分两种交互方式。但原理都是长按时隐藏掉其他干扰的二维码。

    (1). [方案一](http://mx.qq.com/zorajianglab111/qrcode/qrcode-hide-siblings.html)

      ![识别时隐藏掉其他二维码](http://mx.qq.com/zorajianglab111/qrcode/images/qrcode-hide-siblings.png "有生产日期有生产厂商不确定有没有质量合格证的2有产品")

    (2). [方案二](http://mx.qq.com/zorajianglab111/qrcode/qrcode-show-onmask.html)

      ![识别时让二维码在弹出层中](http://mx.qq.com/zorajianglab111/qrcode/images/qrcode-show-onmask.png "有生产日期有生产厂商不确定有没有质量合格证的2有产品")

方案一代码：    
HTML

```
<ul class="qrbox" id="qrbox">
  <li class="item">
    <img src="images/mweibo.jpg" alt="微博二维码">
    <h3 class="title">微博二维码</h3>
  </li>
  <li class="item">
    <img src="images/mwx.jpg" alt="微信二维码">
    <h3 class="title">微信二维码</h3>
  </li>
</ul>
```

CSS

```
*{padding:0;margin:0;-webkit-user-select: none; -moz-user-select: none; -ms-user-select: none; user-select: none; }
ul{list-style:none;}
.qrbox {overflow:hidden;padding:10px 0;text-align: center;}
.qrbox img{ width:100%; }
.qrbox .item{float:left; width:35%; margin-left:10%;}

```

Javascript

```
var items = document.getElementsByClassName('item');
var itemsLength = items.length;
function getChildren(n, skipMe){
  var arr = [];
  for ( ; n; n = n.nextSibling ) 
     if ( n.nodeType == 1 && n != skipMe)
        arr.push( n );        
  return arr;
};

function getSiblings(n) {
    return getChildren(n.parentNode.firstChild, n);
}
for(var i = 0; i<itemsLength; i++){
  items[i].addEventListener('touchstart', function(){
    var _self = this;
    var siblingElem = getSiblings(_self);
    var siblingElemLength = siblingElem.length;
    for (var i = 0; i < siblingElemLength; i++) {
      siblingElem[i].style.visibility = 'hidden';
    }
    setTimeout(function(){
      for (var i = 0; i < siblingElemLength; i++) {
        siblingElem[i].style.visibility = 'visible';
      }
    }, 1000);
    
  },false);
}

```

方案二代码：   
HTML

```
<ul class="qrbox" id="qrbox">
  <li class="item">
    <img src="images/mweibo.jpg" alt="微博二维码">
    <h3 class="title">微博二维码</h3>
  </li>
  <li class="item">
    <img src="images/mwx.jpg" alt="微信二维码">
    <h3 class="title">微信二维码</h3>
  </li>
</ul>
<div class="mask" id="mask"></div>
```

CSS

```
*{padding:0;margin:0;-webkit-user-select: none; -moz-user-select: none; -ms-user-select: none; user-select: none; }
ul{list-style:none;}
.qrbox {overflow:hidden;padding:10px 0;text-align: center;}
.qrbox img{ width:100%; }
.qrbox .item{float:left; width:35%; margin-left:10%;}
.mask{display:none;text-align:center;width:100%;height:100%;position:absolute;left:0;top:0;z-index:1000;background-color:#000;}
.mask img{position:absolute;top:50%;left:50%;margin-top:-90px;margin-left:-90px;}
```

Javascript

```
var items = document.getElementsByClassName('item');
var itemsLength = items.length;
var mask = document.getElementById('mask');
for(var i=0; i<itemsLength; i++){
  items[i].addEventListener('touchstart', function(){
    var _self = this;
    var clnQrImg = this.querySelector('img').cloneNode(true);
    mask.appendChild(clnQrImg);
    mask.style.display = 'block';
    mask.querySelector('img').addEventListener('touchend', function(){
      mask.style.display = 'none';
      mask.innerHTML = '';
    },false);
  },false);
}
```


那我们就稍微探究下二维码。

## 什么是二维码？

二维码又称QR Code，QR全称Quick Response，是一个近几年来移动设备上超流行的一种编码方式。

可参考[二维码的生成细节和原理](http://coolshell.cn/articles/10590.html)来学习了一下二维码图片生成的相关细节。

## 微信识别二维码的原理

采用的是 `截屏逻辑`，
当客户端发现用户在网页的img标签内进行长按操作时，会立刻截屏并且启动二维码识别算法。    
所以这里用于二维码识别的图片是截屏，而不是之前有人提到的img标签中的图片。    
之所以使用截屏，可以不用考虑网络传输因素，最快的得到识别结果，    
否则就要走一次图片下载的逻辑，用户长按后等待的时间会加长，体验上也失去了快感。    
所以考虑先截屏，截屏失败后再下载的逻辑。

可参考 [网页中二维码识别规则](http://mp.weixin.qq.com/s?__biz=NzA3OTQ2OTgw&mid=205918916&idx=1&sn=d729ef70ea1e7fc50e649352a63a2564&scene=1&key=c76941211a49ab586ba4831f2f6fa42fbb2525460003fa8e19bc205b9b25facbf60da78977da071ddbc41a5be6a7b9fa&ascene=0&uin=NTE3ODg0NjE1&devicetype=iMac%20MacBookPro12,1%20OSX%20OSX%2010.10.3%20build%2814D136%29&version=11020012&pass_ticket=WPa8XnvIzBrBOA6RHy2RMECcrsuN2QiksK8Y1Z/D3Odk49uIsXOex7EV2NXN9m5X)

文章提到网页中二维码无法被识别的原因有如下三个：

1. 二维码显示信息不全，在长按时只有部分可见，如下

![长按时只有部分可见的二维码图片](http://mx.qq.com/zorajianglab111/qrcode/images/incomplete.png "这是别人的图")

2. 二维码周围信息过于复杂，在整个截屏中二维码算法无法正确识别，如下

![周围信息复杂的二维码图片](http://mx.qq.com/zorajianglab111/qrcode/images/complicated.png "这是别人的图")

3. 网页没有加载完成，微信的识别js没有启动。


## 微信可识别的二维码的大小

二维码的可识别大小跟url的长度有关。
二维码不要太大，否则容易跳出屏幕。通常160*160就可以。


## 总结

针对以上所说我们总结下微信长按识别二维码无法正常识别的bug

1. img标签 需要将二维码图片单独切出，img 标签形式展现，用bg无效

2. 二维码根据url的长度，不要低于50px,不要超过设备屏幕宽度。

3. 2或2张以上二维码无法在同一视窗被识别，识别时隐藏掉干扰项。
