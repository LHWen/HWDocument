

# JavaScript

### JS 简介

#### 改变HTML内容

```javascript
/* getElementById() 通过id进行元素查找 */
document.getElementById("testDemo").innerHTML = 'Hello JavaScript';
```

#### 改变元素样式

```javascript
/* 通过元素id 进行元素样式修改 */
document.getElementById("testDemo").style.fontSize = '20px';

<button onclick="document.getElementById('myImage').src='/i/eg_bulbon.gif'">开灯</button>

<button onclick="document.getElementById('myImage').src='/i/eg_bulboff.gif'">关灯</button>
```

#### 隐藏HTML元素

```JavaScript
document.getElementById("demo").style.display = 'none';
```

#### 显示HTML元素

```JavaScript
document.getElementById("demo").style.display = 'block';
```

### JS 输出

JavaScript能够以不同方式“显示”数据：

- 使用 window.alert() 写入警告框
- 使用 document.write() 写入 HTML 输出
- 使用 innerHTML 写入 HTML 元素
- 使用 console.log() 写入浏览器控制台

```javascript
// 使用innerHTML
document.getElementById("demo").innerHTML = "修改块内容信息";

// 使用 document.write()，直接输出在HTML中
// 提示：document.write() 方法仅用于测试 !!!!
document.write(5 + 6); // 结果直接输出在HTML中
<!DOCTYPE html>
<html>
<body>
<h2>我的第一张网页</h2>
<p>我的第一个段落。</p>
 // 点击按钮会直接讲上面的 h2/p 标签内容被 document.write(5 + 6)结果替换
<button type="button" onclick="document.write(5 + 6)">试一试</button>
</body>
</html>

// 使用 window.alert(), 使用警告框来显示数据
window.alert(5 + 6); // 弹框提示

// 使用 console.log()， 控制台打印数据
console.log(5 + 6);
```

### JS 语句

```JavaScript
// 先定义属性后赋值
var x, y, z;
x = 22; y = 11; z = x + y;
// 定义属性的时 同时初始化
var x = 11, y = 22, z;
z = x + y;
```

JavaScript 关键词

| 关键词        | 描述                                              |
| :------------ | :------------------------------------------------ |
| break         | 终止 switch 或循环。                              |
| continue      | 跳出循环并在顶端开始。                            |
| debugger      | 停止执行 JavaScript，并调用调试函数（如果可用）。 |
| do ... while  | 执行语句块，并在条件为真时重复代码块。            |
| for           | 标记需被执行的语句块，只要条件为真。              |
| function      | 声明函数。                                        |
| if ... else   | 标记需被执行的语句块，根据某个条件。              |
| return        | 退出函数。                                        |
| switch        | 标记需被执行的语句块，根据不同的情况。            |
| try ... catch | 对语句块实现错误处理。                            |
| var           | 声明变量。                                        |

#### JavaScript 比较运算符

| 运算符 | 描述           |
| :----- | :------------- |
| ==     | 等于           |
| ===    | 等值等型       |
| !=     | 不相等         |
| !==    | 不等值或不等型 |
| >      | 大于           |
| <      | 小于           |
| >=     | 大于或等于     |
| <=     | 小于或等于     |
| ?      | 三元运算符     |

#### JavaScript 算数运算符

算术运算符对数值（文字或变量）执行算术运算。

| 运算符 | 描述                                                      |
| ------ | :-------------------------------------------------------- |
| +      | 加法                                                      |
| -      | 减法                                                      |
| *      | 乘法                                                      |
| **     | 幂（[ES2016](https://www.w3school.com.cn/js/js_es6.asp)） |
| /      | 除法                                                      |
| %      | 系数，返回除法余数                                        |
| ++     | 递增                                                      |
| --     | 递减                                                      |

幂运算

```JavaScript
var x = 5;
var z = x ** 2;          // 结果是 25

// 使用函数进行运算
var x = 5;
var z = Math.pow(x, 2); // 25
```

#### JavaScript 赋值运算符

赋值运算符向 JavaScript 变量赋值。

| 运算符 | 例子     | 等同于      |
| :----- | :------- | :---------- |
| =      | x = y    | x = y       |
| +=     | x += y   | x = x + y   |
| -=     | x -= y   | x = x - y   |
| *=     | x *= y   | x = x * y   |
| /=     | x /= y   | x = x / y   |
| %=     | x %= y   | x = x % y   |
| <<=    | x <<= y  | x = x << y  |
| >>=    | x >>= y  | x = x >> y  |
| >>>=   | x >>>= y | x = x >>> y |
| &=     | x &= y   | x = x & y   |
| ^=     | x ^= y   | x = x ^ y   |
| \|=    | x \|= y  | x = x \| y  |
| **=    | x **= y  | x = x **    |

### JS 数据类型

#### JavaScript 数据类型

JavaScript 变量能够保存多种*数据类型*：数值、字符串值、数组、对象等等：

```JavaScript
var length = 7;                             // 数字
var lastName = "Gates";                      // 字符串
var cars = ["Porsche", "Volvo", "BMW"];         // 数组
var x = {firstName:"Bill", lastName:"Gates"};    // 对象 

// 获取数组对象值
var carsData = cars[0];

// 获取集合对象值
var xMap1 = x.firstName;

```

### JS 函数

函数定义

```javascript
function myFunction(p1, p2, p3) {
  return p1 + p2 + p3;
}
// 格式
function testFunc(参数1, 参数2, 参数3, ...) {
  // 逻辑处理代码 
}
// es 6 语法，参数多个，调用时可以不全传完参数过去
```

### 常见的 HTML 事件

下面是一些常见的 HTML 事件：

| 事件        | 描述                         |
| :---------- | :--------------------------- |
| onchange    | HTML 元素已被改变            |
| onclick     | 用户点击了 HTML 元素         |
| onmouseover | 用户把鼠标移动到 HTML 元素上 |
| onmouseout  | 用户把鼠标移开 HTML 元素     |
| onkeydown   | 用户按下键盘按键             |
| onload      | 浏览器已经完成页面加载       |

### 字符串处理

#### 特殊字符

| 代码 | 结果 | 描述   |
| :--- | :--- | :----- |
| \'   | '    | 单引号 |
| \"   | "    | 双引号 |
| \\   | \    | 反斜杠 |

```javascript
// 中国是瓷器的故乡，因此 china 与 "China（中国）"同名
var y = "中国是瓷器的故乡，因此 china 与 \"China（中国）\"同名";
// It's good to see you again
var x = 'It\'s good to see you again';
```

转义字符（\）也可用于在字符串中插入其他特殊字符。
其他六个 JavaScript 中有效的转义序列：

| 代码 | 结果       |
| :--- | :--------- |
| \b   | 退格键     |
| \f   | 换页       |
| \n   | 新行       |
| \r   | 回车       |
| \t   | 水平制表符 |
| \v   | 垂直制表符 |

