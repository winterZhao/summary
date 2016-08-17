##  水平居中和垂直居中
####  水平居中

1.  `text-align` 段落内水平居中,适用于小部分块级元素,h,p标签;

```html
<p style="text-align:center;">hello world</p>
<h3 style="text-align:center;">hello,nihao</h3>
```

2. `margin:0 auto` 水平居中`auto`,试用大部分块级元素div等;

```html
<div style="width:100px;height:100px;background-color:red;margin:0 auto;"></div>
```

####  垂直居中

1. `line-height=height` ;   单行文本垂直居中; 缺点:不能用百分比;

```html
<div style="width:200px;height:100px;border:1px solid black;
line-height:100px;">hello world</div>
```
2. 盒子容器padding-top = padding-bottom ; 多行文本;

```html
<p style="width:100px;padding:10px;border:1px solid red;">全球最大的中文搜索引擎、致力于让网民
更便捷地获取信息，找到所求。百度超过千亿的中文网页数据库，可以瞬间找到相关的搜索结果。</p>
```
注意: 这里的宽高只能限制一个，由内容撑开。

3. 父元素display:table-cell;vertical-align:middle+子元素vertical-align:middle；+display:table;【正常文字行高等于容器的高;直接垂直居中;如果图片则需要给图片父容器加`vertical-align:middle;`如果既有图片又有内容则每一项容器都需要`vertical-align:middle;`

```js
<style>
    .a {display:table;}
    .b {display:table-cell;border:1px solid black;width:100px;text-align:center;line-height:30px;}
</style>
<div class="a">
    <div class="b"><span>hello</span></div>
    <div class="b"><span>hello</span></div>
    <div class="b"><span>hello</span></div>
</div>
```
表单单元格自动具有垂直居中的功能;`display:table-cell`的样式效果类似于行内垂直居中，如果想元素单独一行，需要在外层元素的父元素`display:table`;

4. display:inline-block；

```html
<div style="width:500px;height:300px;background-color:yellow;">
    <img style="height:100%;display:inline-block;vertical-align:middle;"
    src="http://img3.imgtn.bdimg.com/it/u=1826553975,3450069094&fm=206&gp=0.jpg" alt=""/>
    <span style="display:inline-block;vertical-align:middle;">hello world</span>
</div>
```
当只有单元素需要参照物的时候可以使用伪元素;
**注**单子元素垂直居中采用`display:inline-block`与`display:table-cell`的区别;前者元素单独一行显示，后者可与其他元素同行显示；

```html
<style>
        .b {width:400px;height:400px;background-color:red;}
        span,.b:after{display:inline-block;vertical-align:middle;}
        .b:after{content:'';height:100%;}
     </style>
<div class="b"><span>hello world</span></div>
```

#### 水平垂直居中

1. 绝对定位1 position:absolute;left:0;top:0;bottom:0;right:0;margin:auto;

```html
<div style="width:200px;height:200px;background-color:red;position:relative;">
    <div style="width:100px;height:100px;background-color:yellowgreen;position:absolute;
    left:0;right:0;bottom:0;top:0;margin:auto;"></div>
</div>
```
父元素必须定位;父子都得有宽高;否则相对于body定位;且只能用px;不能用百分比;

2. 绝对定位2 :  position:absolute;left:50%;top:50%;margin:-width/2 0 0 -height/2;

```html
<div style="width:200px;height:200px;background-color:red;position:relative;">
    <div style="width:100px;height:100px;background-color:yellowgreen;position:absolute;
    left:50%;top:50%;margin:-50px 0 0 -50px"></div>
</div>
```
父元素必须定位;父子都得有宽高;否则相对于body定位;且只能用px;不能用百分比;

3. translate : 绝对定位;父元素必须定位;父子都得有宽高;translate,适用于无线;可以使用百分比,有兼容性;

```html
<div style="width:200px;height:200px;background-color:red;position:relative;">
    <div style="width:100px;height:100px;background-color:yellowgreen;position:absolute;
    left:50%;top:50%;transform: translate(-50%,-50%);"></div>
</div>
```


4. flex: 父元素display:flex；justify-content:center;align-items:center;(移动端);
缺点:兼容性

```html
<div style="width:300px;height:100px;background-color:red;display:flex;justify-content:center;
align-items:center;">
    <span>hello heihei</span>
</div>
```

http://www.w3cplus.com/css/vertically-center-content-with-css