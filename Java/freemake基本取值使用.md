### freemake基本取值使用

常用取值：`${var}` 对于数值类型取值使用 #{var}
对于null，不存在对象取值: `${var!}`
取包装对象的值，通过点语法( . )：`${userInfo.name}`

取值的时候进行计算-赋值

```java
// date 类型转时间格式
${date ? String('yyyy-MM-dd')}

// HTML转义内容，将HTML标签语法转string输出
${var ? html}

// 布尔值 true and false 无法直接输出，需要转换

// 定义常量cVar, 可使用${cVar}进行取值
<#assign cVar == 99/>

// var == null 可设置默认值
${var ! '默认值'}

// 赋值与运算（加、减、乘、除）
a = ${a}
${aVar + bVar + 100}

// -----自定义对象取值-----
public ModelAndView toAdd(ModelAndView mv) {
        UserInfo uInfo = new UserInfo("name", "age");
        mv.addObject("uInfo", uInfo);
        mv.setViewName("sys/userList");
        return mv;
    }
// userList.ftl 模板引擎文件取值
${uInfo.name}
// 若uInfo不存在进行整体判断处理
${uInfo.name!}
// 按照顺序判断，先判断对象是否存在-再判断属性是否存在
${(uInfo.name)!}

```

集合遍历

```java
// List对象 arrInfos 数组对象，as 表for循环，item循环结果对象
<#list arrInfos as item>
	${item}
</#list>
  
// map集合对象
Map<String, String> mp = new HashMap<String, String>();
mp.add("jKey", "jValue");
// 取值进行赋值 ${key} = key, ${mp[key]} = value
<#list mp ? keys as key>
  ${key}:${mp[key]}
</#list> 
  
  // 数组长度获取
  <#if arrInfos?size gt 0>
  	// 下标取值
    ${arrInfos[0]}
  </#if>
    
    // 数组长度判断
  <#if (arrInfos?size > 0)>
  	// 下标取值
    ${arrInfos[0]}
  </#if>

```

if语法

```java
// 定义常量
<#assign cVar == 99/>
  // 不能包含大于小于符号 >< ，gt是大于, lt是小于，
  // 大于等于：(&gt;) => (>=), 小于等于：(&lt;) => (<=)
  // if语法中同样可使用或（||）、与（&&）、非（!）
  <#if cVar == 98>
  ...
  <#elseif cVar == 99>
  ...
  <#else>
  ...
  </#if>
  
  // if + list
  // 判断list对象是否存在
  <#if uInfos ??>
  	<#list uInfos as item>
  		// 判断循环下标
  		<#if item_index == 1>
  			...
  		<#else>
  			...
  		</#if>
  	</#list>
  </#if>
```

switch语法

```java
<#assign v = 10/>

<#switch v>

<#case 10> 
...
<#break>

<#case 11> 
...
<#break>

<#defaut> 
...
<#break>

</#switch>
```

字符串String基本操作指令

```java
<#assign a = 'hello'/>
<#assign b = 'world'/>

	// 链接
 ${a+b}
	// 截取
 ${(a + b) ? substring(5,8)} == wor
   // 长度
 ${(a + b) ? length}
   // 转大写
 ${(a + b) ? upper_case}
   // 转小写
 ${(a + b) ? lower_case}
   // w出现的位置
 ${(a + b) ? index_of('w')}
   // 替换
 ${(a + b) ? reaplace('w', 'c')}
   // w最后出现的位置
 ${(a + b) ? last_index_of('w')}
   
```

freemake中给js传值

```javascript
<script>
  	// 传递字符串
    var fileStatic = '${fileStatic}';
		// 传递数组需要遍历put进数组里面
    var fileIds = [];
    <#if fileIds??>
        <#list fileIds as item>
            fileIds.push('${item}');
        </#list>
    </#if>
</script>
```

