##  一、网页基本标签

### 1.标题标签

设置标题级数

```html
<!--
	align属性:设置对齐方式
-->
<h1 align="left">...</h1>
<h2 align="right">...</h2>
.		.
.		.
.		.
<h6 align="center">...</h6>
```

### 2.段落标签

快捷键p+Tab

作用是显示一行原格式字符串

```html
<p>...</p>
```

### 3.换行标签

写于段尾，用于换行

```html
<br>
```

### 4.水平线标签

出现一根用于分隔的水平直线

```html
<hr>
```

### 5.字体样式标签

```html
粗体：<strong>...</strong>
斜体：<em>...</em>
```

### 6.注释和特殊符号

```html
注释：<！--...-->
空格：&nbsp;
小于号：&gt;
大于号：&lt;
版权符号：&copy;
    
```



## 二、图像标签

```html
<img src="path" alt="text" title="text" width="x" height="y" border="">
src:图像的地址(必填)
alt:图像的替代文字(必填)
title:鼠标悬停提示文字
width/heigth:图像宽高
border:设置图像边框
```

## 三、链接标签

### 1.a标签(超链接)

快捷键a+Tab

```html
<a href="path" target="目标窗口位置">链接文本或图像</a>
href:链接路径（跳转目标页面）
target:链接在哪个窗口打开，常用值:_self(在当前页面打开)、_blank(新建一个标签页)

```

### 2.锚链接

可以跨链接跳转

```html
<!--设置一个标签作锚标记，除a标签外其他也可-->
<a name="top">顶部</a>
<!--跳转到设置的锚标记处-->
<a href="其他页面#top">回到顶部</a>
```

### 3.功能性链接

```html
<!--邮件链接-->
<a herf="mailto:...">文本</a>

```

## 四、块元素与行内元素

### 1.块元素

无论内容多少，该元素独占一行（该行内行已被占，自动换行）

(p、h1-h6...)

### 2.行内元素

宽度由内容决定，一行可以有多个行内元素

(span、a、strong、em...)

## 五、列表标签

### 1.有序列表

```html
<!--(ordered list)应用范围:试卷、问答...-->
<ol>						生成效果
    <li>。。。</li>    		1. 。。。
    <li>。。。</li>    		2. 。。。
    <li>。。。</li>    		3. 。。。
    <li>。。。</li>    		4. 。。。
</ol>
```



### 2.无序列表（最多）

```html
<!--(unordered list)应用范围:导航、侧边栏...
	type="none"可以去掉前面的点
-->

<ul type="none">						生成效果
    <li>。。。</li>    		● 。。。
    <li>。。。</li>    		● 。。。
    <li>。。。</li>    		● 。。。
    <li>。。。</li>    		● 。。。
</ul>		
```



### 3.自定义列表(用得少)

```html
<!--(definited list)
	dl:标签
	dt:标签名称
	dd:列表内容
-->
<dl>						生成效果
    <dt>text</dt>  			
    						text
    <dd>text1</dd>    			 text1
    <dd>text2</dd>    			 text2
    <dd>text3</dd>    			 text3    
    
</dl>			
```



## 六、表格

```html
<!--(table)
	tr:行 
	td:列
	boder:设置表格边框
	width=设置表格宽度
	height=设置表格高度
	cellspacing=设置单元格间距
	colspan:跨列
	rowspan:跨行
	align:设置对齐方式
	b是加粗标签
-->
<table boder="1px">
    <tr>
        <td colspan="4">...</td>
        <td rowspan="2">...</td>
        <td><b>...</b></td>
        <td>...</td>  
    </tr>
    <tr>
        ...
    </tr>
    
</table>
```

## 七、媒体元素

```html
<video src="path"/此处可以填写更多设置/></video>
常见的有controls、autoplay等
<audio src="path"/此处可以填写更多设置/><audio>
与以上类似
```

## 八、页面结构分析

| 元素名(*为重点) | 描述                                               |
| --------------- | -------------------------------------------------- |
| *header         | 标题头部区域的内容（用于页面或页面中的一块区域）   |
| *footer         | 标记脚部区域的内容（用于整个页面或页面的一块区域） |
| section         | Web页面中的一块独立区域                            |
| article         | 独立的文章内容                                     |
| aside           | 相关内容或应用（常用于侧边栏）                     |
| *nav            | 导航类辅助内容                                     |

## 九、内联框架iframe

```html
<!--src:引用页面地址
	name:框架识别名
	width:宽
	height:高
	常被用作菜单
-->
<iframe src="path" name="mainFrame"></iframe>
<ul>
    <li><a herf="path1" target="mainFrame">通过iframe窗口打开path1</a></li>
    <li><a herf="path2" target="mainFrame">通过iframe窗口打开path2</a></li>
    <li><a herf="path3" target="mainFrame">通过iframe窗口打开path3</a></li>
</ul>
```

## 十、表单

### 1.表单各操作示例

```html
<!--action:表示向何处发送表单数据
	method:规定如何发送表单数据，常用值:get、post
	get:可以在url中看到我们提交的信息，不安全，高效，有数据长度限制
	post:比较安全，可以传输大文件
细节：有name属性才会提交该信息
	 单选、多选下拉框都要添加value，以便发送给服务器

-->
<form method="post" action="result.html">

	<!--可以用value设置默认值-->
	<p> 名字:<input name="name"type="text"> </p>
	<p> 密码:<input name="pass"type="password"> </p>
  

<!--单选框标签
    input type="radio"
    value:单选框的值
    name:表示组，每组只能同时选中一个
	checked="checked"表示默认选中
    -->
     <p>
         <input type="radio" value="boy" name="sex"/>男
         <input type="radio" value="girl" name=“sex”/>女
     </p>


<!--多选框标签
	input type="checkbox"
	checked="checked"表示默认选中
    -->
     <p>爱好
         <input type="checkbox" value="sleep" name="hobby"/>睡觉
         <input type="checkbox" value="code" name="hobby"/>代码
         <input type="checkbox" value="sleep" name="hobby"/>聊天
         <input type="checkbox" value="game" name="hobby"/>游戏
     </p>


<!--按钮
	input type="button"		普通按钮
	input type="image"		图像按钮
	input type="submit"		提交按钮
	input type="reset"		重置按钮
	-->
	<p>
        <input type="button" name="butn1" value="点击变长">
        <input type="image" src="../image/1.png"
</p>

     <p>
        <input type="submit" name="Button" value="提交"/>
    </p>
    <p>
        <input type="reset" name="Reset" value="重填"/>          
    </p>

</form>
```

### 2.表单元素格式

| 属性      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| type      | 指定元素的类型:text、password、checkbox、radio、submit、reset、file、hidden、image和button、默认为text |
| name      | 指定表单元素的名称                                           |
| value     | 元素的初始值，type为radio时必须指定一个值（单选框）          |
| size      | 指定表单元素的初始宽度，当type为text或password时，表单元素的大小以字符为单位，对于其他类型，宽度以像素为单位 |
| maxlength | type为text或password时，输入的最大字符数                     |
| checked   | type为radio或checkbox时指定按钮是否被选中                    |

### 4.下拉框

```html
<!--
	select="selected"表示默认选中
-->
<p>
    <select name="列表名称">
        <option value="选项的值">...</option>
        <option value="选项的值">...</option>
        <option value="选项的值">...</option>
        <option value="选项的值">...</option>
    </select>
            
</p>
```

### 5.文本域

```html
<p>
    <textarea name="textarea" cols="列数" rows="行数">...</textarea>
</p>
```

### 6.文件域

```html
<p>
    <input type="file" name="files">
</p>
```

### 7.搜索框滑块和简单的验证

1.数字

```html
<p>
    <input type="number" name="num" max="" min="" step="">
</p>
```

2.滑块

```html
<p>
    <input type="range" name="voice" min="" max="" step=""
</p>
```

## 补充

### 表单的应用

隐藏域: hidden

只读: readonly

禁用: disabled

### 表单初级验证

非空: required

提示信息: placeholder=“ ”

正则表达式:百度查

增强鼠标可用性

```html
<p>
    <lable for="mark">...</lable>
<input type="text" id=mark>
</p>
```



