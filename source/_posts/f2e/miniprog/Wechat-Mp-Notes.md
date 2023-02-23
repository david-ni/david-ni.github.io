# 小程序开发踩坑记

1. 小程序样式中无法使用本地文件

```less
## wxss
.bg{
  ## 使用相对路径无法加载图片，需要使用网络地址，比如http://static.xxx.com/images/xxx.png
  background-image: url("../../image/image-1.png")
}
```

2. `wxml`无法直接使用函数

```xml
<!--错误使用方式，无法直接调用-->
<view>{{methodExample()}}</view>

<!--正确使用: 需要调用wxs的方法-->
<wxs module="wxsUtils">
module.exports.methodExample = function(){ .... } 
</wxs>
<view>{{wxsUtils.methodExample()}}</view>
```

3. `wxs`设置的样式优先级比组件` wxml` 里面定义的样式高

`wxs`中可以使用`setStyle`来设置对应样式，而`wxml`只能通过触发`setData`中的变量进而更新样式。

这里需要注意的是：**`wxs`设置的样式优先级比组件` wxml` 里面定义的样式高**，我们可以通过`WxsPropObserver`来监听`wxml`中属性的变化，再适时调用`wxs`的方法改变样式

```xml
<wxs module="test" src="./test.wxs"></wxs>
<view change:prop="{{test.propObserver}}" prop="{{propValue}}" bindtouchmove="{{test.touchmove}}" class="movable"></view>
```

