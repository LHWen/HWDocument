# HTML学习之路

### 基础标签

#### 标题标签

```html
<p>
  HTML 标题，是通过 h1 - h6 等标签进行定义的
</p>
<h1>This is a heading, h1</h1>
<!-- <hr> 标题水平线，hr 元素可用于分隔内容 -->
<hr>
<h2>This is a heading, h2</h2>
```

#### 段落标签

```html
<p>HTML 段落，是通过 p 标签进行定义的</p>
<p>This is a paragraph.</p>
<!-- 如果您希望在不产生一个新段落的情况下进行换行（新行），请使用 <br /> 标签；如果使用<p></p>进行换行，表示是另起一个段落 -->
<p>This is another paragraph.<br />使用 br 标签进行换行</p>
```

#### 链接标签

```html
<a href="http://www.w3school.com.cn">This is a link, go to w3c</a>
```

#### 图像标签

```html
<p>获取图片路径，使用相对路径，两个点开头：../xxx/xxx.png</p>
<img src="../images/kobe.jpg" width="208px" height="284px">
```

### 不赞成使用标签和属性

在 HTML 4 中，有若干的标签和属性是被废弃的。被废弃（Deprecated）的意思是在未来版本的 HTML 和 XHTML 中将不支持这些标签和属性。

**这里传达的信息很明确：请避免使用这些被废弃的标签和属性！**

#### 应该避免使用下面这些标签和属性：

| 标签                 | 描述               |
| -------------------- | ------------------ |
| <center>             | 定义居中的内容     |
| <font> 和 <basefont> | 定义 HTML 字体     |
| <s> 和 <strike>      | 定义删除线文本     |
| <u>                  | 定义下划线文本     |
| **属性**             | **描述**           |
| align                | 定义文本的对齐方式 |
| bgcolor              | 定义背景颜色       |
| color                | 定义文本颜色       |

background-color 属性为元素定义了背景颜色

```html
<html>

<body style="background-color:yellow">
<h2 style="background-color:red">This is a heading</h2>
<p style="background-color:green">This is a paragraph.</p>
</body>

</html>
```

**style 属性淘汰了“旧的” bgcolor 属性**

font-family、color 以及 font-size 属性分别定义元素中文本的字体系列、颜色和字体尺寸

```html
<html>

<body>
<h1 style="font-family:verdana">A heading</h1>
<p style="font-family:arial;color:red;font-size:20px;">A paragraph.</p>
</body>

</html>
```

**style 属性淘汰了旧的 <font> 标签**

text-align 属性规定了元素中文本的水平对齐方式

```html
<html>

<body>
<h1 style="text-align:center">This is a heading</h1>
<p>The heading above is aligned to the center of this page.</p>
</body>

</html>
```

**style 属性淘汰了旧的 "align" 属性**

### 文本格式

pre标签属于格式标签，不会忽略空格

```html
<pre>
for i = 1 to 10
     print i
next i
</pre>
输出：
for i = 1 to 10
     print i
next i
```

地址标签

```html
<address>
Written by <a href="mailto:webmaster@example.com">Donald Duck</a>.<br> 
Visit us at:<br>
Example.com<br>
Box 564, Disneyland<br>
USA
</address>
```

### HTML 中有用的字符实体

**注释：实体名称对大小写敏感！**

```
显示结果		 描述					实体名称						实体编号
 						空格					&nbsp;							&#160;
	<					小于号		 		 &lt;								 &#60;
	>					大于号		 		 &gt;								 &#62;
	&					和号		  		&amp;								&#38;
	"					引号					&quot;							&#34;
	'					撇号 					&apos; (IE不支持)		&#39;
	￠					分（cent）		&cent;							 &#162;
	£					镑（pound）	 &pound;							&#163;
	¥					元（yen）		 &yen;								&#165;
	€					欧元（euro）	&euro;							 &#8364;
	§					小节					&sect;							&#167;
	©					版权					&copy;							&#169;
	®					注册商标			&reg;								&#174;
	™					商标				  &trade;							&#8482;
	×					乘号					&times;							&#215;
	÷					除号					&divide;						&#247;
```

