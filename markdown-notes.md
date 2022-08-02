# markdown 使用教程
不同的 markdown 只要浏览器支持，可以渲染出不同的结果。typora 本质上就是一个浏览器，用来渲染 markdown 文件。latex 公式也要看渲染器是否支持。

semantics 语义:
话里的意思，词 word / 词组 phrase / 句子 sentence / 段落 paragraph

formatting 格式:
写的东西的外观，布局 layout / 字体 font / 颜色 color

注意：markdown 兼容 html 语法

优点：不需要专门的软件打开，如 word, pages。和 html 相比更简单，更注重内容

## 标题

`# 一级标题`
`## 二级标题`
`### 三级标题`
`#### 四级标题`
`##### 五级标题`
`###### 六级标题`

## 文字
~~删除线~~ `~~delete~~` （注意一定是英文下的波浪号）<br>
*斜体* `*italic*` <br>
**加粗** `**bold**` <br>
***斜体加粗*** `***italic and bold***` <br>
表情符号 :smile: `:smile:` <br>
&nbsp;空格缩进 `&nbsp;` <br>

<p align='center'>居中</p>   

```html
<p align='center'>居中</p> 
```

<p align='right'>局右</p>

```html
<p align='right'>局右</p>
```

>引用
>>嵌套引用
>>>继续嵌套

`>quote` `>>nested quote` `>>>continue nested`

## 表格
| column1 | column2 | column3 |
| :------ | :-----: | ------: |
| row1    |  row1   |    row1 |
| row2    |  row2   |    row2 |
```markdown 
| column1 | column2 | column3 |
| :------ | :-----: | ------: |
| row1    |  row1   |    row1 |
| row2    |  row2   |    row2 |
```

## 列表
无序列表：
* item1
* item2
  
`* item` `+ item` `- item`

有序列表：
1. item1
2. item2
   1. nested item1
300. item3

`1. item` （注意随便什么数字都不影响序号正确，数组和点中间使用\反斜杠来转义）

**注脚**
Content [^2]
[^2]: Hi! This is a footnote
```
Content [^1]
[^1]: Hi! This is a footnote
```

**任务列表**
- [x] task1
- [ ] task2

`- [x] task1`

## 代码块
```javascript
const obj = {}
let arr = []
```
` ```language code here ``` ` （注意一定要换行，且闭合代码块）

## 分隔线
***

`***` `---`

## 换行
<br>

```html
<br>
```

## 跳转
[外部链接](http://support.html)
`[outer link text](link)` （注意外部链接会使用默认浏览器打开）

<http://support.html>
<hongyu.0802@gmail.com>
`<auto link>` （注意自动链接会使用默认浏览器打开，邮件会使用默认邮件打开）

## 图片
![图片描述](https://img0.baidu.com/it/u=2098311615,833819932&fm=253&fmt=auto&app=120&f=PNG?w=834&h=608)<br>
`![img text](hyper link or local path)` （注意在图片无法展示时显示图片描述）

插入流程图、时序图、饼图、数据库关系

## 隐藏内容
<!-- hidden-->
`<!-- hidden-->`

## 忽略 markdown 格式
\*emphasis* <br>
`\*emphasis*`

## latex 公式
$$
\lim_{x \to \infin}\frac{sin(t)}{x}
$$

独占一段 `$$formula$$`

$\lim_{x \to \infin}\frac{sin(t)}{x}$

行内公式 `$formula$` （注意在第一个美元符号前加空格可以保持更好的兼容性）

## vscode 内使用 markdown 所需的插件
### paste image 方便的插入图片
### code spell checker 检查错别字
忽略一些文件内的拼写检查
### markdown all in one 提供快捷键和自动补全
斜体 `cmd i` <br>
加粗 `cmd b` <br>
列表项的自动缩进和补全编号 <br>
拷贝图像 `cmd opt v`  和自动提示文件内可选图片 <br>
提供 latex 公式语法的自动补全 和 创建行内公式 `cmd m` <br>
格式化文档 `opt sft f` (主要用于格式化表格 format) <br>
选取文字，将链接粘贴过来后自文字动转为链接格式 


### markdown preview enhanced 渲染器 [^1] {#renderer}
[^1]: [markdown preview enhanced 官方文档链接](https://shd101wyy.github.io/markdown-preview-enhanced/#/zh-cn/)

==高亮== `==text==` <br>
下标上标 H~2~O~2~  `H~2~O~2~` H^2^ `H^2^` <br>
下划线 <u>underscore</u> `<u>underscore</u>` <br>


<font color='green'>绿色</font>

```html
<font color='green'>绿色</font>
```

<font size='20'>字体大小</font>

```html
<font size='20'>字体大小</font>
```


**劝告**

!!! note This is the admonition title
    This is the admonition body

**缩写注释**

*[HTML]: Hyper Text Markup Language <br>
*[W3C]: World Wide Web Consortium <br>
The HTML specification
is maintained by the W3C.

**代码块行数**
```javascript {.line-numbers}
const obj = {}
let arr = []
```
` ```language  {.line-numbers} code here ``` ` 

**新段落**
通过在文本行之间留一个空白行，可创建新段落。


[本地跳转至代码块](#renderer)

## typora 渲染器 [^3]
[^3]: [typora formatting syntax link english](https://support.typora.io/style/) <br>

下划线 `<u>text</u>` <br>
高亮 `==text==` <br>

表情符号 `:smile` <br>
内部链接 `[link text](# title)` <br>

## github 渲染器 [^4]
[^4]:[github formatting syntax link chinese](https://docs.github.com/cn/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#lists)

不支持高亮 ==高亮== <br>
不支持回车换行 <br>
latex 的 \infin 无穷符号不支持 <br>
html 的颜色和字体大小不支持

## 其他参考链接
[basic syntax guide english](https://www.markdownguide.org/basic-syntax/)<br>
[extended syntax guide english](https://www.markdownguide.org/extended-syntax/)
