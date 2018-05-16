---
title: markdown-tutorial
date: 2016-10-21 12:44:02
updated: 2016-10-21 12:44:02
tags:
categories:
keywords:
---

Markdown 是一种电子邮件风格的标记语言，他是文字编辑者的福音，有了它，你就可以在编辑文本的时候和鼠标说再见了（有种 Vim 的即视感~_~）。
以下是 Markdown 的部分优点：

- 专注于编辑文字而逃离排版的深渊
- 易懂、易用、易读
- 简洁却十分强大，支持 HTML 和 CSS ，同时格式间的转换非常方便

详情可参见：

> [Markdown 是什么？](http://web.archive.org/web/20160330102057/http://www.zhihu.com/question/19963642)
> [Markdown](http://web.archive.org/web/20160330102057/http://en.wikipedia.org/wiki/Markdown)

下面是 Markdown 的一些示例：

```
    # Title1
    ## Title2
    
    This is a **paragragh**.
    The _second_ *line*.
    \# 21B  415

    > a magic show

    [Eason Yang](http://www.eason-yang.com)
    
    ***

```

# Title1

## Title2

This is a **paragragh**.
The *second* *line*!
\# 21B 415

> a magic show

[Eason Yang](http://web.archive.org/web/20160330102057/http://www.eason-yang.com/)

------

接下来我们来详细介绍 Markdown 的用法

## 标题

#### Atx 方法

#### Setext 方法

```
Title1
=========
Title2
---------------

```

# Title1

## Title2

## 段落格式

1. 首行缩进
   很遗憾，Markdown 并未直接提供对首行缩进的支持，实际上在英文世界也基本没有首行缩进这个概念，但是借助 CSS 或者使用两个全角的空格再或者直接暴力使用 都可以解决问题，更详细的解释可以参见 [markdown 如何实现首行缩进？](http://web.archive.org/web/20160330102057/http://www.v2ex.com/t/136227)

2. 段落
   段落前后有一行空格，即可形成一个段落。空行指的是没有实际内容的行，回车、空格、制表符实现的行都是空行。 Markdown 不会把每个换行符转为 `<br />` ,想要实现 `<br />` 的换行效果，需要在插入处输入两个空格然后回车。

3. 列表
   使用*（+及-也可以） + 空格 + 内容的方式即可实现 HTML 中的无序列表使用*数字 `* +` 句点 `.` 加上内容的方式即可实现 HTML 中的有序列表 `<ol></ol>`。
   此外通过控制不同的缩进， Markdown 会按要求将内容加上，从而在显示时实现不同的缩进效果，示例如下：

   ```
   * li1
   * li2
   * li3

   1. li1
   2. li2
   3. li3

   - Markdown is something 
   magic for us.
     > 注意这里的缩进
   - This is an example
     of list.
   > 若无缩进则会与原列表脱离 

   ```

   - li1
   - li2
   - li3

   1. li1
   2. li2
   3. li3

   - Markdown is something

     ​

     magic for us.

     > 注意这里的缩进

   - This is an example
     of list.

   > 若无缩进则会与原列表脱离

4. 代码块
   对应HTML的 `<pre></pre>` 和 `<code></code>` ，我们使用`来实现代码块（即键盘上 Tab 上方的那个键）。

- 对某一部分实现代码块：

  ```
  This is the function `printf()`

  ```

  This is the function

   

  ```
  printf()
  ```

- 多行代码：

  ​

  \``` A python example

  ​

  def test() :

  ​

  print 1

  ​

  \```

  ```python
      def test() :
          print 1
  ```

1. 强调与斜体
   \* + 内容 + * 实现斜体
   _ + 内容 + _实现斜体
   ** + 内容 + ** 实现粗体
   __ + 内容 + __ 实现粗体

   ```
   This is the *content*.
   This is the _content_.
   This is the **content**.
   This is the __content__.

   ```

   This is the *content*.
   This is the *content*.
   This is the **content**.
   This is the **content**.

2. 分割线
   在一行中输入三个以上*、-或_中的一个来实现，同时允许中间加入空格

   ```
   ------
   ******
   ______
   * * *
   _   _ _
   -  -  -
   ```

   ------

   ------

   ------

   ------

   ------

   ------

3. 引用
   使用>实现引用，并可用多个>控制引用的缩进

   ```
   > A test
   >> test 2

   ```

   > A test
   >
   > > test 2

## 链接及图片

1. 链接

- 方式一：

  ```
  [text](http://www.eason-yang.com "可选的title")

  ```

- 方式二：

  ```
  [text][A]
  [A]:http://www.eason-yang.com "可选的title"

  ```

- 自动链接

  ```
  [id]: <http://www.eason-yang.com/>  "可选的title"

  ```

1. 图片
   与链接的实现方法十分相似，仅需加一个!即可

- 方式一：

  ```
  ![text](http://www.eason-yang.com "可选的title")

  ```

- 方式二：

  ```
  ![text][A]
  [A]:http://www.eason-yang.com/pic "可选的title"

  ```

## 使用HTML和CSS

Markdown 兼容 HTML 和 CSS ，因此我们可以曲线救国实现很多 Markdown 默认实现不了的效果
如，改变字体大小和颜色

```
<span style="font-size: 5em;color: red">Markdown</span>

```

Markdown

## 转义字符

这是一个老话题了，这个问题可一描述为**怎样才能输入>、&等这样的特殊字符**。解决方法是在需要转换的字符前加上 \ 即可

```
> test

\> test

```

> test

\> test

## Markdown 编辑器（不分先后）

- 首先 Typecho 的编辑器是支持的，给 Typecho 点个赞
- [Typora](http://www.typora.io/)
- [简书](http://web.archive.org/web/20160330102057/http://www.jianshu.com/)*(p.s. online editor)*
- [MarkdownPad](http://web.archive.org/web/20160330102057/http://markdownpad.com/)*(p.s. for windows)*
- [MarkPad](http://web.archive.org/web/20160330102057/http://code52.org/DownmarkerWPF/)*(p.s. for windows)*
- [ReText](http://web.archive.org/web/20160330102057/http://sourceforge.net/p/retext/home/ReText/)*(p.s. for linux)*
- [Mou](http://web.archive.org/web/20160330102057/http://25.io/mou/)*(p.s. for Mac OS X)*
- Evernote、wordpress 等甚至 sublime text 、Atom 都可以通过各种方法实现对 Markdown 的支持

本文由 [Eason Yang](https://eason-yang.com) 创作，采用*[署名 4.0 国际（CC BY 4.0）创作共享协议](http://creativecommons.org/licenses/by/4.0/deed.zh)*进行许可，[详细声明 ](https://eason-yang.com/about/)。