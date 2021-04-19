---
title: "Markdown学习"
categories: Markdown
tags: Markdown 基础
description: Markdown学习
toc:
  depth_from: 2
  depth_to: 6
  ordered: true
---
# Markdown笔记  

[toc]
## Markdown基础要素  

### 写在前面

1. 许多Markdown编辑器没有段落格式，例如Github内置的Markdown编辑器，需要注意结尾两空格为强制切换下一行，而我使用的VS Code则默认使用回车切换下一行，这里仅做演示使用空格换行，其余地方使用回车，如果你的Markdown换行不能预期正常显示，记得使用两空格强制换行。
eg. 这里是第一行，结尾两空格  
eg. 这里是第二行，也可以直接空一行开始新的段落
2. 许多Markdown语法需要在其后方空出一个才能生效或者高亮显示，而有些则不强制需要，这里我统一使用空一格的语法来使排版更加整齐美观。不过，有些语法使用空格则会失效，我将会在下面提到。
3. 影响排版的Markdown语法我都将以<code>" ```markdown "</code>符号封闭，仅做Markdown代码本身的展示，如果你需要预览它的实际效果，去除表示闭合代码块的符号即可。
4. 为了使排版更加美观整齐或是为了取消语法格式的连续，本Markdown文档将频繁使用空白行进行排版。

### 标题的表示

#### 1. #号表示法

```markdown
# 一级标题  
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

#### 2. 两个以上的减号或者等号

```markdown
大标题
===============
这里是内容，注意下一行为空行

小标题
---------------
```

* 建议使用 “#” 处理标题，如果使用`[toc]`生成Markdown文档目录，将捕获使用 “#” 处理的标题。
* 如果你使用减号或等号处理标题，那么符号的上一行必须要有文字充当标题，因此使用该方法可能会带来一些换行上的麻烦。
* 建议标题上下各空一行

### 引用

引用文本只需要这样：
> When life gives you lemons, squeeze lemonade into eyes.

### 字体格式

Markdown支持粗体、斜体或其两种的组合，注意该语法不支持后空一个空格。

```markdown
**粗体**  
__粗体__  
*斜体*  
_斜体_  
***粗斜体***  
___粗斜体___
~~删除线~~
```

效果如下：
**粗体**  
__粗体__  
*斜体*  
_斜体_  
***粗斜体***  
___粗斜体___
~~删除线~~

### 分割线

你可以在一行中使用三个或以上的星号、下划线或者是减号来表示分割线，这一行中不能包含其他文字  

```markdown
***
* * *
- - -
___
```

抬头纹效果如下
***
不建议使用除三个连续的`*`以外的分割线写法。

### 列表

#### 无序列表

无序列表使用 + 、- 或 * 号开始每一行

```markdown
+ Auf Wanderung
  * Sei nicht traurig, bald ist es Nacht,
  * Da sehn wir über dem bleichen Land
  * Den kühlen Mond, wie er heimlich lacht
  * Und ruhen Hand in Hand.
  - Sei nicht traurig, bald kommt die Zeit,
  - Da haben wir Ruh. Unsre Kreuzlein stehen
  - Am hellen Straßenrande zu zweit,
  - Und es regnet und schneit,
  - Und die Winde kommen und gehen.
```

列表使用空格或者是tab缩进来分级，而不是使用不同的符号。这里建议统一使用tab键进行缩进。
同级的不同符号如果没有上级符号时，解释器将会在两种符号之间自动空出一行
效果如下：

* Auf Wanderung
  * Sei nicht traurig, bald ist es Nacht,
  * Da sehn wir über dem bleichen Land
  * Den kühlen Mond, wie er heimlich lacht
  * Und ruhen Hand in Hand.
  * Sei nicht traurig, bald kommt die Zeit,
  * Da haben wir Ruh. Unsre Kreuzlein stehen
  * Am hellen Straßenrande zu zweit,
  * Und es regnet und schneit,
  * Und die Winde kommen und gehen.

不建议在一个列表内使用多种符号。

#### 有序符号

有序列表只需用序号. 内容即可，例如：

```markdown
1. From fairest creatures we desire increase,
2. That thereby beauty's rose might never die,
3. But as the riper should by time decease
```

这里建议尽量在 "数字."之后加一个空格，否则某些解释器可能会不识别，成为普通的一行文字而不是列表。
注意，列表结束后需要空出一行，否则会与下方的内容连续。尽管标题通常不会被编辑器默认连续，但我还是建议统一手动空出一行。

### 图片与网址的链接

* 图片支持本地链接支持相对路径和绝对路径，但并不灵活，文件的移动容易造成图片丢失，个人认为使用本地链接会破坏使用Markdown的原则，所以不建议使用,链接格式如下:

```markdown
![Cute Dog](https://t.cn/A6PtI2tZ)
Format:![Title](link)
```

![Cute Dog](https://t.cn/A6PtI2tZ)

* 网址的链接更加简单:

```markdown
[GitHub](http://github.com)
```

链接成功: [GitHub](http://github.com)

* 还有一种特殊的图片链接方法，使用图片的base64值允许你在脱机状态下也可以浏览图片，又不会使你的文档拖泥带水。这里也可以直接使用base64，但是base64字符串太长，放在文字中间排版会看起来很糟糕，所以这里我们使用内部链接，将需要链接的base64字符串放在Markdown文档结尾，这里就不做展示了:

```markdown
![Mini Challenge][Mini64]
````

![Mini Challenge][Mini64]
这里仅选择大小为1Kb的图片，它的base64值仍然很长。

* 邮箱的链接将打开计算机上默认的邮件软件，它的链接方式十分简单:

```markdown
<tonyzep966@outlook.com>
```

<tonyzep966@outlook.com>

这是一种自动链接的方式，Markdown会将它自动解释为超链接，也同样适用于网址:

```markdown
<http://github.com>
```

<http://github.com>

### 代码插入与高亮

你可以使用`符号开始或结束，包含并插入行内的一个函数代码片段，这样解释器就不会解释此处的Markdown语法并强调显示该处的文字，在符号包含范围中允许特殊的缩进。

带有语法高亮的代码块使用<code>```</code>开始与结束（**注意不是单引号**），可以在起始处表明需要高亮的语言，例如：

<pre>
```kotlin{.line-numbers}
fun picBase64(path: String): String {
    val file = File(path)
    return Base64.getEncoder().encodeToString(file.readBytes())
}
```
</pre>
效果如下:

```kotlin{.line-numbers}
fun picBase64(path: String): String {
    val file = File(path)
    return Base64.getEncoder().encodeToString(file.readBytes())
}
```

这里的`{.line-numbers}`是Markdown Preview Enhanced(简称MPE)插件的扩展特性。
你也可以使用`<code># 没有用的标题</code>`来在行内插入，或者是`<pre></pre>`来进行整段插入，但要进行代码高亮就相对麻烦了许多。

### 任务列表

任务列表如下:  

- [x] <del>Create Team</del>
- [ ] **New Project**

任务列表语法对空格的要求比较严格，其行内支持多种文字格式。

### 表格

表格的写法较为麻烦，但也十分直观，有三种对齐方式表示，其中 '-'的个数不限:  
| Left | Right | Middle |
| :--- | ----: | :----: |
| 1    |     2 |   3    |
| 4    |     5 |   6    |

下面是两种MPE插件支持的写法: 
左右合并单元格的写法：
| a   |    b |
| --- | ---: |
| >   |    1 |
| 1   |      |

上下合并单元格的写法：
|   a   |   b   |
| :---: | :---: |
|   1   |   2   |
|   ^   |   4   |
### 符号与图标

#### Emoji和Font-Awesome的支持

* Emoji例子:
:smile: :fa-car: :boy: :girl:
* Font-Awesome是一款HTML+CSS框架，它允许你使用更多的图标，但很多Markdown编辑器在脱机状态下并不可以使用。要使用Font-Awesome，你必须在你的Markdown文档中加入下面的代码，放在文档中任何地方都可以，但我建议放在Markdown文档的末尾，便于修改与删除之余也不会影响美观。

```html{.line-numbers}
<head> 
    <script defer src="https://use.fontawesome.com/releases/v5.12.0/js/all.js"></script> 
    <script defer src="https://use.fontawesome.com/releases/v5.12.0/js/v4-shims.js"></script> 
</head> 
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.12./css/all.css">
```

测试一下:
<i class = "fa fa-weibo"></i> [**966曹長**](https://weibo.com/zep966)
我的编辑器内置了Font-Awesome V4，可以脱机预览，如果你使用上面的链接则是V5的版本，需要注意的是，V4使用的fa前缀在V5中已更改为fas和fab。

#### 上标、下标和脚注等

* 上标: `1^st^` 1^st^
* 下标: `H~2~O` H~2~O
* 脚注:
> だが南に向いた斜面には葡萄[^1]の房[^2]が青々と支柱にみのって、
その祝福された内部に情熱と密[^3]かな慰めとを秘めている。

[^1]:ぶどう
[^2]:ふさ
[^3]:ひそ

脚注的详细注解将会自动移动到预览页面的末端。

* 缩略，严格注意格式，否则它将不会被解释器识别，而且不是所有编辑器都支持此种语法:

```markdown
*[DSL]: Domain-Specific Language
```

*[DSL]: Domain-Specific Language
> An external DSL is a language that's parsed independently of the host general purpose language: good examples include regular expressions and CSS.

如果你的编辑器不支持这项语法，可以使用HTML的`<abbr>`标签解决问题，例如:

```markdown
> The <abbr title="Hyper Text Markup Language">HTML</abbr> specification is maintained by the <abbr title="World Wide Web Consortium">W3C</abbr>.
```

它的效果如下:
> The <abbr title="Hyper Text Markup Language">HTML</abbr> specification is maintained by the <abbr title="World Wide Web Consortium">W3C</abbr>.

* 标记:

```markdown
==我说的都是重点==
```

==我说的都是重点==

* 使用反斜杠`\`在你的文档中加入特殊符号，例如\#。

### HTML带来的一些帮助

Markdown是可以转换为HTML的，所以我们可以使用HTML标签来弥补Markdown语法上的微小缺点。

#### 强制换行

你可以使用`<br>`或者`<br/>`来进行强制换行，这可以用在多行文本或者是表格单元格内换行中。某些编辑器可能会要求你在br和 / 之间加一个空格，这曾经在HTML中也有同样的问题。

#### 字体大小与颜色

如果你想改变字体的大小或颜色，可以使用`<font> </font>`标签，但没有特殊需要通常不建议使用，以保持Markdown文档的简洁整齐型。

## Markdown进阶要素

### 数学公式

$\alpha+$
$\alpha^2$



[Mini64]:data:image/png;base64,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