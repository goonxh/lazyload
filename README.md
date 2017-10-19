# lazyload
懒加载与木桶布局的说明及实例

## 懒加载

假如一个图片网站有成千上万的图片，如果用户一打开页面，所有的图片都去同时去加载，浏览器就会卡主等到图片全部加载完成，如果用户发现这些图片并不是他想要的那么就会白白浪费很多流量，这样的体验并不好，所以通过检测图片是否出现在用户的视窗中，如果出现了就去加载，如果没出现就不去加载，这种方法就叫做懒加载。
### 懒加载的思路

1.我们将页面上所有<img>标签src属性中的图片地址，放在一个我们自定义的属性中，比如：

	<img src="" datasrc="xxx.png">
这样页面打开后图片也不会加载，自定义属性的名字可以随便起，只要不和已有的属性名重复就行；
2.获取整个窗口高度和滚动的距离，img标签的位置。如果img标签的位置小于窗口高度加滚动的距离，那么该img标签就出现在了用户的视窗中；
3.检查出现用户视窗中的img标签的图片否加载过，如果没有那么就加载该图片。

### 实现：
1.图片示例:

	<img src="" alt="" datasrc="1.jpg">
2.写一个判断img标签是否出现在视窗中的函数
```javascript
function checkImg(value){ //参数value表示我们传入的img标签
    var scrollLength = $(window).scrollTop(), //scrollLength代表滚动的距离
    wimdowHeight = $(window).height(), //wimdowHeight代表窗口高度
    imgOffset = value.offset().top; //imgOffset代表img标签的位置
    if(imgOffset <  (scrollLength+wimdowHeight)){
        return true; //表示该img标签已出现在视窗中
    }else{
        return false; //表示该img标签没有出现在视窗中
    }
}
```
3.写一个判断img标签是否加载过的函数
```javascript
function loadImg(value){
    value.attr('src',value.attr('data-src'));
}
```
只需要判断传入的这个img标签的src中的地址，是不是和自定义属性data-src相等，就可以判断出该图片是否加载


4.写一个加载图片的函数
```javascript
function loadImg(value){
    value.attr('src',value.attr('data-src'));
}
```
将传入的img标签的src中的图片地址换成datasrc中的地址即可。

5.写一个函数，遍历所有的img标签，当img标签满足出现在视窗且没有加载过的条件时，加载该img标签
```javascript
function render(){
    $('.wrap img').each(function(){
        if(checkImg($(this)) && !isLoad($(this))){
            loadImg($(this));
        }
    })
}
```
6.给window绑定scroll事件，我们需要在用户打开页面时就执行一次函数render，如果不先执行一次，用户打开页面没有滚动的时候，就一张图片都看不到
```javascript
$(function(){
    render();
    var lock;
    $(window).on('scroll',function(){
        if(lock){
            clearTimeout(lock);
        }
        lock = setTimeout(function(){
            render();
        },500)
    })
})
```
setTimeout是为了性能考虑，因为一般滚动一次会触发好几次scroll事件，所以这样写让最后一次事件再去执行render()，也就是用户连续滚动停下来的500毫秒后去加载图片。

## 木桶布局
实现CSS：
	```css
		*{
			margin: 0;
		}
		body{
			padding: 50px 0;
			overflow-x: hidden;
		}
		.wrap{
			display: flex;
			flex-wrap: wrap;
		}
		.wrap img{
			margin: 3px;
			padding: 5px;
			height: 200px;
			background: #ccc;
			flex-grow: 1;
			object-fit: cover;
			transition: .3s;
		}
		.wrap:after{
			display: block;
			content: '';
			flex-grow: 9999;
		}
		.wrap img:hover{
			transform: scale(1.2);
			box-shadow: 0 0 20px #fff;
			z-index: 9999;
		}
	```
[代码地址](https://coding.net/u/goonxh/p/lazyload/git/blob/master/index.html)
[预览地址](http://goonxh.coding.me/lazyload/)
