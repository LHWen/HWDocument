# css使用记录

HTML的样式属性

## 基础教程

```html
<head>
  <style type="text/css">
  	/** 高级语法  */
  	h1,h2,h3,h4,h5,h6 {
  	color: green;
  	}
  	/** id 选择器  */
  	#id1 {color: red;}
  	#id2 {color: green;}
  	#id1, #id2 {
  	  text-aling: right;
  	}
  	#div_pID p {
  	  color: red;
  	  margin: 5;
  	}
  	/** 类 选择器（class 与 id用法一致，写法差异(# -> .)）  */
  	.class1 {color: red;}
  	.class2 {color: green;}
  	/** 属性 选择器 */
		input[type="text"] {
		  width:150px;
		  display:block;
		  margin-bottom:10px;
		  background-color:yellow;
		  font-family: Verdana, Arial;
		}
	</style>
  
  <!-- 外部样式表 -->
  <link rel="stylesheet" type="text/css" href="xxxx/xxxx.css" />
</head>

<body>
  <p id = 'id1'></p>
	<p id = 'id2'></p>
	<div  id = 'div_pID'>
  	<p>内容</p>
	</div>
  
	<p class = 'class1'></p>
	<p class = 'class2'></p>
</body>
```

## 样式

### 背景

```css
p {
  /*纯色背景*/
  background-color: gray;
  /*上下左右添加内边距*/
  padding: 20px;
}

body {
  /*使用图片作为背景图*/
  background-image: url(../image/bg_img.png);
}
/* 下面例子为一个段落应用了一个背景，而不会对文档的其他部分应用背景：*/
p.flower {background-image: url(../image/bg_img1.png)}
/*行内元素设置背景图像，下面的例子为一个链接设置了背景图像*/
a.radio {background-image: url(../image/bg_img2.png);}

/*背景重复--平铺， 背景定位*/
body { 
  background-image: url(../image/bg_img.png);
  /*平铺属性，平铺方向, repeat (x,y轴)、repeat-y（Y轴）、repeat-x，
  no-repeat，不平铺-默认repeat平铺*/
  background-repeat: repeat-y;
  /*背景定位 与no-repeat 搭配使用，居中显示*/
  background-position:center;
  /*百分比对齐方式，与no-repeat 搭配使用
  50% 50% 中心与其元素的中心对齐，0% 0% 左上角
  还能使用px 定位
  background-position:50px 100px;
  */
  background-position:10% 20%;
  /*固定背景不跟随滑动*/
  background-attachment:fixed
 }
```

### 文本

#### 基础使用属性

```css
p {
  /* 字体颜色 */
  color:red;
  /* 背景颜色 */
  background-color:yellow;
  /* 字符间距 */
  letter-spacing: 20px;
  /* 百分比显示行间距，100% 表示正常间距，<100% 减少行间距，>100%增加 */
  line-height: 90%;
  /* 行间距，使用像素显示调整行间距 */
  line-height: 20px;
  /* 行间距，使用数值，默认行高大约是 1 */
  line-height: 2;
  /* 文本对齐方式, center:居中，left：左对齐(默认), right:右对齐 */
  text-align: right;
}
```

#### 文本缩进

```css
/* 文本缩进，可以使用负数  -0.5em，整体缩进，左右缩进，负数向右移动 */
p {text-indent: 5em;}
/** 还可以使用百分比 */
div {width: 500px;}
p {text-indent: 20%;}

<div>
<p>this is a paragragh</p>
</div>

```

#### 对齐以及其他属性

```css
/* 对齐方式， left、right 和 center 会导致元素中的文本分别左对齐、右对齐和居中 */
/** justify 左右居中对齐，文本分散按照一定间隔自动填充满一行 */
p {text-align: center;}

/*word-spacing 属性可以改变字（单词）之间的标准间隔。其默认值 normal 与设置值为 0 是一样的。表空间间隔距离，this is 这样两个字的距离*/
p.spread {word-spacing: 30px;}
p.tight {word-spacing: -0.5em;}
<p class="spread">This is a paragraph.</p>
<p class="tight">This is a paragraph.</p>

/* letter-spacing 属性与 word-spacing 的区别在于，字母间隔修改的是字符或字母之间的间隔。 */
h1 {letter-spacing: -0.5em}
h4 {letter-spacing: 20px}
<h1>This is header 1</h1>   
<h4>This is header 4</h4>   

/* text-transform 属性处理文本的大小写。这个属性有 4 个值
none: 默认值 none 对文本不做任何改动
uppercase: 将文本转换为全大写字符
lowercase: 将文本转换为全小写字符
capitalize: 每个单词的首字母大写
*/

/* 文本装饰属性： text-decoration 属性
none：会关闭原本应用到一个元素上的所有装饰
underline：会对元素加下划线
overline：会在文本的顶端画一个上划线
line-through：会在文本中间画一个贯穿线
blink：会让文本闪烁
*/
a {text-decoration:none;}
/*还可以在一个规则中结合多种装饰。如果希望所有超链接既有下划线，又有上划线，则规则如下：*/
a:link a:visited {text-decoration: underline overline;}

/*处理空白格 会影响到用户代理对源文档中的空格、换行和 tab 字符的处理
以下属性回忽略掉文档中的空格 换行 tab字符处理，全部处理为空格
慎用
*/
p {white-space: normal;}
```

### 字体

```css
/* font-style 属性最常用于规定斜体文本
normal - 文本正常显示
italic - 文本斜体显示
oblique - 文本倾斜显示
italic 和 oblique 文本在 web 浏览器中看上去完全一样
*/

/* font-variant 属性可以设定小型大写字母;T正常大小，其他都为大写小字体 */
p {font-variant: small-caps}
<p>This is a paragraph</p>

/* font-weight 字体加粗属性，normal：正常，bold加粗 */
p {font-weight: bold}

/* 设置字体大小 */
p {font-size:60px;}
```

### 链接

```css
/*
注释：为了使定义生效，a:hover 必须位于 a:link 和 a:visited 之后！！
注释：为了使定义生效，a:active 必须位于 a:hover 之后！！
*/
<style>
a:link {color:#FF0000;}    /* 未被访问的链接 */
a:visited {color:#00FF00;} /* 已被访问的链接 */
a:hover {color:#FF00FF;}   /* 鼠标指针移动到链接上 */
a:active {color:#0000FF;}  /* 正在被点击的链接 */
</style>

<style>
a:link {text-decoration:none;}    /* 未被访问的链接 去掉下划线 */
a:visited {text-decoration:none;} /* 已被访问的链接 去掉下划线  */
a:hover {text-decoration:underline;}   /* 鼠标指针移动到链接上 有下划线 */
a:active {text-decoration:underline;}  /* 正在被点击的链接 有下划线  */
</style>

/* 结合使用 */
<style>
a:link,a:visited
{
display:block;
font-weight:bold;
font-size:14px;
font-family:Verdana, Arial, Helvetica, sans-serif;
color:#FFFFFF;
background-color:#98bf21;
width:120px;
text-align:center;
padding:4px;
text-decoration:none;
}

a:hover,a:active
{
background-color:#7A991A;
}
</style>

<p><a href="https://www.baidu.com" target="_blank">这是一个链接</a></p>
```

### 边框

```css
/* 所有边框属性在一个声明之中 */
<style type="text/css">
/* 样式，颜色 */
p {border: medium double rgb(250,0,255)}
/* 设置四边框样式（border-style） dotted(正方形点虚线)、dashed(小长块点虚线)、solid(实线)、double(双线边框)、groove(类似凹进边框)、ridge(类似凸起边框)、inset(上左 凹陷效果)、outset(右下 凹陷)，none 默认无边框 */
p {border-style: dotted}
/* 设置每一边的不同边框，顺序为，上-右-下-左，两个值表示，上下跟左右 */
p {border-style: solid double}
/* 宽度 border-width，四边不一样宽度也是遵循，上-右-下-左 */
p {border-width: 5px}
/* 边框颜色，四边不一样颜色也是遵循，上-右-下-左 */
p {border-color: #0000ff}
/* 所有下边框属性在一个声明中（border-bottom、border-top、border-left、border-right） */
p {
  border-style:solid;
	border-bottom: thick dotted #ff0000;
  /* 设置下边框颜色 */
  border-bottom-color:#ff0000;
  /* 设置下边框样式 */
  border-bottom-style:inset;
  /* 设置下边框宽度，thin 常规宽度，这个为细线 */
  border-bottom-width: 15px;  
  
}
</style>
<p>Some text</p>
```

### 定位

```css
/* position 定位属性，relative(相对定位)、absolute(绝对定位)、fixed(固定定位) */
/* 
相对定位：相对于一个元素的正常位置来对其定位
绝对定位：使用绝对值来对元素进行定位
固定定位：相对于浏览器窗口来对元素进行定位
*/
div {
  position:relative;
  left:20px
}
p {
position:fixed;
top:30px;
right:5px;
}

/* 固定值、百分比、设置图像的上(下)边缘, top left bottom right */
img {
	position:absolute;
  top:0px;
  top:5%;
  /* 下边缘 */
  bottom: 0px;
}

/* 内容太大而超出规定区域时，设置溢出属性来规定相应的动作，滚动条 */
/* overflow 对溢出规定区域元素处理，scroll(滚动)、 hidden(隐藏)、auto(浏览器自动处理) */
div  {
  background-color:#00FFFF;
  width:150px;
  height:150px;
  overflow: scroll;
}
/* 属性剪切了一幅图像 */
img {
  position:absolute;
  clip: rect(0px 50px 200px 0px)
}
/* 在文本中垂直排列图象, text-top/text-bottom, 
默认text-bottom, 无center */
img {vertical-align:text-top}
/* Z-index可被用于将在一个元素放置于另一元素之后，默认的 z-index 是 0 */
img {
  position:absolute;
  left:0px;
  top:0px;
  /* 图层，可将元素至于最底或者最顶层，负数是向底层，正数向最上层移动 */
  z-index:-1
}

/* 浮动 - float属性 */

```

### 对齐

```css
/* margin 水平对齐
把左和右外边距设置为 auto，规定的是均等地分配可用的外边距。结果就是居中的
如果宽度是 100%，则对齐没有效果
*/
.center {
  margin-left:auto;
  margin-right:auto;
  width:70%;
  background-color:#b0e0e6;
}

/* position 属性进行左和右对齐
对齐元素的方法之一是使用绝对定位
*/
.right {
position:absolute;
right:0px;
width:300px;
background-color:#b0e0e6;
}

/* float 属性来进行左和右对齐
对齐元素的另一种方法是使用 float 属性
*/
.right {
float:right;
width:300px;
background-color:#b0e0e6;
}
```

### 尺寸

```css
/* 图片尺寸，设高度不设宽度，宽随高度自适应；只设宽，高随宽自适应 */
img {
  height: auto; /* 自适应 */
  height: 50%; /* 父类占比，可用实际像素 100px */
  height: 10%
}
/* 设置一个元素的最大高、宽度 */
p {max-height: 10px; max-width: 300px;}
/* 设置一个百分比的最大高、宽度 */
p {max-height: 10%; max-width: 30%}
/*设置一个元素的最小高、宽度 */
p {
  min-height: 50px;
  min-width: 300px;
  /* 百分比 */
  min-height: 10%;
  min-width: 70%;
}

/* 设置行间距 */
p {
  /* 百分比，默认行间距是 100% - 120% */
  line-height: 120%;
  /* 像素，默认行间距是 20px */
  line-height: 30px;
  /* 数值，默认行间距是 大约是1 */
  line-height: 1.5;
}
```

### 分类

```css
/* 元素显示为内联元素, 第二个p标签内容，跟第一个P标签内容后面，不换行 */
<style type="text/css">
p {display: inline}
div {display: none}
</style>
<p>本例中的样式表把段落元素设置为内联元素。</p>
<p>而 div 元素不会显示出来！</p>
<div>div 元素的内容不会显示出来！</div>

/* 把元素显示为块级元素，两个span 进行分块（换行） */
span { display: block }
<span>本例中的样式表把 span 元素设置为块级元素。</span>
<span>两个 span 元素之间产生了一个换行行为。</span>

/* 使图像浮动于一个段落的右侧 */
img {
  float:right;
  border:1px dotted black; /* 显示边框 */
  margin:0px 0px 15px 20px; /* 下外边距：15px、左外边距: 20px */
}
<p>
<img src="图片地址"/>
一长串文字信息，进行多行显示的内容信息
</p>

/** 创建水平菜单 */
<style type="text/css">
ul {
float:left;
width:100%;
padding:0;
margin:0;
list-style-type:none;
}
a {
float:left;
width:7em;
text-decoration:none;
color:white;
background-color:purple;
padding:0.2em 0.6em;
border-right:1px solid white;
}
a:hover {background-color:#ff3300}
li {
display:inline;
text-align:center;
}
</style>

<ul>
<li><a href="#">Link one</a></li>
<li><a href="#">Link two</a></li>
<li><a href="#">Link three</a></li>
<li><a href="#">Link four</a></li>
</ul>

<p>
在上面的例子中，我们把 ul 元素和 a 元素浮向左浮动。li 元素显示为行内元素（元素前后没有换行）。这样就可以使列表排列成一行。ul 元素的宽度是 100%，列表中的每个超链接的宽度是 7em（当前字体尺寸的 7 倍）。我们添加了颜色和边框，以使其更漂亮。
</p>
```

### 元素不可见

```css
<html>
<head>
<style type="text/css">
h1.visible {visibility:visible}
h1.invisible {visibility:hidden}
</style>
</head>

<body>
<h1 class="visible">这是可见的标题</h1>
<h1 class="invisible">这是不可见的标题</h1>
</body>
</html>
```

改变光标，鼠标指针显示不一样

```css
<html>

<body>
<p>请把鼠标移动到单词上，可以看到鼠标指针发生变化：</p>
<span style="cursor:auto">Auto 文本样式 I</span><br />
<span style="cursor:crosshair">Crosshair 十字</span><br />
<span style="cursor:default">Default 默认</span><br />
<span style="cursor:pointer">Pointer 白色小手</span><br />
<span style="cursor:move">Move 十字箭头，表移动</span><br />
<span style="cursor:e-resize">e-resize 右→</span><br />
<span style="cursor:ne-resize">ne-resize 右斜上方箭头</span><br />
<span style="cursor:nw-resize">nw-resize 左斜上方箭头</span><br />
<span style="cursor:n-resize">n-resize 向上箭头</span><br />
<span style="cursor:se-resize">se-resize 右斜下方箭头</span><br />
<span style="cursor:sw-resize">sw-resize 左斜下方箭头</span><br />
<span style="cursor:s-resize">s-resize 向下箭头</span><br />
<span style="cursor:w-resize">w-resize 左←</span><br />
<span style="cursor:text">text 文本光标 I</span><br />
<span style="cursor:wait">wait 网络请求等待光标</span><br />
<span style="cursor:help">help 帮助光标</span>
</body>
</html>
```

清除元素侧面

```css
<html>

<head>
<style type="text/css">
img {
  float:left;
  clear:both; /** 让元素换行，清除掉右边元素空间，相当于右边是填充满的 */
  }
</style>
</head>

<body>
<img src="/i/eg_smile.gif" />
<img src="/i/eg_smile.gif" />
</body>

</html>
```

## CSS3最新标准

### 边框

```css
/* 设置圆角 */
div {
text-align:center; /* 字体居中 */
border:2px solid #a1a1a1; /* 边框 */
padding:10px 40px; /* 内边距 */
background:#dddddd; /* 背景色 */
width:350px; /* 宽度 */
border-radius:25px; /* 圆角 */
-moz-border-radius:25px; /* 老的 Firefox */
/* 阴影设置，x、y 阴影清晰效果(0最清晰，正数)，颜色 */
box-shadow: 5px 5px 10px #888888; 
}
<div>border-radius 属性允许您向元素添加圆角。</div>
```

### 背景

```css
body {
background:url(/i/bg_flower.gif); /* 设置背景图 */
background-size:63px 100px; /* 设置背景图大小 */
-moz-background-size:63px 100px; /* 老版本的 Firefox */
background-repeat:no-repeat; /* 平铺效果，no-repeat：否，repeat：是 */
padding-top:80px; /* body 内容顶部内边距 */
/* 多重背景 */
background-image:url(/i/bg_flower.gif),url(/i/bg_flower_2.gif);
}
```

### 文本效果

```css
p {
width:11em; 
border:1px solid #000000;
/* 自动换行 */
word-wrap:break-word;
/* 文本阴影效果，x、y 阴影清晰效果(0最清晰，正数)，颜色 */
text-shadow: 5px 5px 5px #FF0000;
}
```

### 2D 转换

```css
/* 移动、旋转 */
div {
  /* x、y轴的平移 */
  transform:translate(50px,100px);
  -ms-transform: translate(50px,100px);		/* IE 9 */
  -webkit-transform: translate(50px,100px);	/* Safari and Chrome */
  -o-transform: translate(50px,100px);		/* Opera */
  -moz-transform: translate(50px,100px);		/* Firefox */
}
/* 旋转, 中心点为固定点 */
div {
  /* 旋转，deg -> 度（°），中心的旋转 */
  transform:rotate(30deg);
  -ms-transform: rotate(30deg);		/* IE 9 */
	-webkit-transform: rotate(30deg);	/* Safari and Chrome */
	-o-transform: rotate(30deg);		/* Opera */
	-moz-transform: rotate(30deg);		/* Firefox */
}
/* 缩放 中心点为固定点 */
div {
  /* 宽高，缩放比例 */
  transform:scale(2,4);
  -ms-transform:scale(2,4); /* IE 9 */
  -moz-transform:scale(2,4); /* Firefox */
  -webkit-transform:scale(2,4); /* Safari and Chrome */
  -o-transform:scale(2,4); /* Opera */
}
/* 翻转 中心的为固定点*/
div {
  /* x轴旋转度数，y轴旋转度数 */
  transform:skew(30deg,20deg);
	-ms-transform:skew(30deg,20deg); /* IE 9 */
	-moz-transform:skew(30deg,20deg); /* Firefox */
	-webkit-transform:skew(30deg,20deg); /* Safari and Chrome */
	-o-transform:skew(30deg,20deg); /* Opera */
}
```

3D 转换，看CSS3 的基本使用方式

### 过渡效果

```css
/* 过渡动画使用案例 改变宽度 */
<!DOCTYPE html>
<html>
<head>
<style> 
div {
width:100px; /* 初始宽度 */
height:100px; /* 初始高度 */
background:yellow;
transition:width 1s; /* 改变宽度，动画时长 */
-moz-transition:width 1s; /* Firefox 4 */
-webkit-transition:width 1s; /* Safari and Chrome */
-o-transition:width 1s; /* Opera */
}
/* 鼠标放在改块上，进行动画改变 */
div:hover {
width:300px; /* 改变的宽度 */
}
</style>
</head>
<body>
<div>改变的div</div>
<p>请把鼠标指针放到黄色的 div 元素上，来查看过渡效果。</p>
<p><b>注释：</b>本例在 Internet Explorer 中无效。</p>
</body>
</html>

/* 案列2 改变宽高，块进行旋转 */
<!DOCTYPE html>
<html>
<head>
<style> 
div {
width:100px; /* 初始宽度 */
height:120px; /* 初始高度 */
background:yellow;
transition:width 1s, height 2s; /* 改变宽高 以及 动画时长 */
-moz-transition:width 2s, height 2s, -moz-transform 2s; /* Firefox 4 */
-webkit-transition:width 2s, height 2s, -webkit-transform 2s; /* Safari and Chrome */
-o-transition:width 2s, height 2s, -o-transform 2s; /* Opera */
}
/* 鼠标放在改块上，进行动画改变 */
div:hover {
width:200px;
height:200px;
transform:rotate(180deg);
-moz-transform:rotate(180deg); /* Firefox 4 */
-webkit-transform:rotate(180deg); /* Safari and Chrome */
-o-transform:rotate(180deg); /* Opera */
}
</style>
</head>
<body>
<div>请把鼠标指针放到黄色的 div 元素上，来查看过渡效果。</div>
<p><b>注释：</b>本例在 Internet Explorer 中无效。</p>
</body>
</html>
```

```css
/* 动画属性 
transition 属性，支持IE、Firefox、Chrome、Opera，Safari使用-webkit
*/
div {
width:100px;
height:100px;
background:yellow;
transition-property:width; /* 动画过渡的属性 */
transition-duration:1s; /* 动画过渡花费时长，默认0 */
transition-timing-function:linear; /* 动画效果时间曲线，默认‘ease’ */
transition-delay:2s; /* 动画效果何时开始，这里延迟2s */
/* Firefox 4 */
-moz-transition-property:width;
-moz-transition-duration:1s;
-moz-transition-timing-function:linear;
-moz-transition-delay:2s;
/* Safari and Chrome */
-webkit-transition-property:width;
-webkit-transition-duration:1s;
-webkit-transition-timing-function:linear;
-webkit-transition-delay:2s;
/* Opera */
-o-transition-property:width;
-o-transition-duration:1s;
-o-transition-timing-function:linear;
-o-transition-delay:2s;
}

div:hover
{
width:200px;
}
```

### 动画

```css
<!DOCTYPE html>
<html>
<head>
<style> 
div {
width:100px;
height:100px;
background:red; /** 初始颜色 */
animation:myfirst 5s; /** 动画时长 */
-moz-animation:myfirst 5s; /* Firefox */
-webkit-animation:myfirst 5s; /* Safari and Chrome */
-o-animation:myfirst 5s; /* Opera */
}

/* 由红色变为黄色，最后为红色 */
@keyframes myfirst
{
from {background:red;}
to {background:yellow;}
}
/* Firefox */
@-moz-keyframes myfirst {
from {background:red;}
to {background:yellow;}
}
/* Safari and Chrome */
@-webkit-keyframes myfirst {
from {background:red;}
to {background:yellow;}
}
/* Opera */
@-o-keyframes myfirst {
from {background:red;}
to {background:yellow;}
}

/* 动画时长按百分比切割，25%-50% 由黄变蓝色-绿色，最后回到原来色值 */
@keyframes myfirst {
0%   {background:red;}
25%  {background:yellow;}
50%  {background:blue;}
100% {background:green;}
}
</style>
</head>
<body>
<div>动画变色div</div>
<p><b>注释：</b>本例在 Internet Explorer 中无效。</p>
</body>
</html>
```

