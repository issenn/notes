# 简单介绍

> Markdown 是一种轻量级的标记语言,允许使用易读易写的纯文本格式编写文档,然后转换成有效的HTML文档	

# 基本操作

## 内容目录

```markdown
[toc]
```

[TOC]

## 标题

```markdown
一级标题 #
二级标题 ##
三级标题 ###
四级标题 ####
五级标题 #####
六级标题 ####### 
```

## 引用

```markdown
一级引用 >
N 级引用 [>..n]
```

> 一级引用
>
> > 二级引用
> >
> > > 三级引用
> > >
> > > > 四级引用
> > > >
> > > > > 五级引用
> > > > >
> > > > > > 六级引用

## 列表

### 无序列表

```markdown
一级无序列表 *
N级无序列表 [tab..n]*
一级无序列表 +
N级无序列表 [tab..n]+
一级无序列表 -
N级无序列表 [tab..n]-
```

* 无序列表 *
  * 无序列表 *

+ 无序列表 +
  + 五序列表 +

- 无序列表 -
  - 无序列表 -

### 有序列表

```markdown
一级有序列表 1.
N级有序列表 [tab..n]1.
```

1. 有序列表
   1. 有序列表
   2. 有序列表

2. 有序列表

### 任务列表

```markdown
- [ ] 未完成
- [x] 已完成
```

- [ ] 抽烟

- [x] 喝酒

- [ ] 汤头

## 表格

```markdown
|姓名|性别|生日|
|:--|:--|:--|
|李满满|男|1991-08-29|
```

| 姓名   | 性别 | 生日       |
| :----- | :--- | :--------- |
| 李满满 | 男   | 1991-08-29 |

## 链接

### 图片链接

#### 本地图片

```markdown
![zbios_efde696](TYPORA.assets/zbios_efde696-1933623.png)
```

![zbios_efde696](TYPORA.assets/zbios_efde696-1933623.png)

#### 网络图片

```markdown
![zbios_efde696](https://ss1.bdstatic.com/5eN1bjq8AAUYm2zgoY3K/r/www/cache/static/protocol/https/home/img/
qrcode/zbios_efde696.png)
```

![https://ss1.bdstatic.com/5eN1bjq8AAUYm2zgoY3K/r/www/cache/static/protocol/https/home/img/qrcode/zbios_efde696.png](../../Desktop/zbios_efde696.png)

### 超链接

```markdown
行内式链接 [百度][https://www.baidu.com/]
参考式链接 [百度]:https://www.baidu.com/
自动式链接 <http://www.baidu.com>
```

[百度][https://www.baidu.com/]

[百度]:https://www.baidu.com/

<http://www.baidu.com>

## 代码

~~~markdown
单行代码 ``
多行代码
```
```
~~~

` print "Hello Word!" `

```python
print "Hello Word!"
```

## 图表

### 自由流程图

```flow
st=>start: Start
op=>operation: Operation
cond=>condition: Condition
sub1=>subroutine: Subroutine
io=>inputoutput: Inputoutput
e=>end: End
st->op->cond
cond(yes)->io->e
cond(no)->sub1(right)->op
```

### 自由时序图

```sequence
Title: Here is a title
A->B: Normal line
B-->C: Dashed line
C->>D: Open arrow
D-->>A: Dashed open arrow
```

## 其它

### 斜体

```markdown
*斜体*
_斜体_
```

*斜体*

_斜体_

### 加粗

```markdown
**加粗**
__加粗__
```

**加粗**

__加粗__

### 高亮

```markdown
==高亮==
```

==高亮==

### 下划线

```markdown
<u>下划线</u>
```

<u>下划线</u>

### 删除线

```markdown
～删除线～
```

~~删除线~~

### 分割线

```markdown
---
```

---

### 注脚

```markdown
Markdown[^[1]]
```

Markdown[^ 1]

### 上下标

```markdown
$3^2=9$
$3^{(3-1)}=9$
$H_2SO_4$
$H_{2SO_4}$
```

$3^2=9$
$3^{(3-1)}=9$
$H_2SO_4$
$H_{2SO_4}$

### 符号输入

```markdown
\\      反斜线
\`      反引号
\*      星号
\_      底线
\{ \}   花括号
\[ \]   方括号
\( \)   括弧
\#      井字号
\+      加号
\-      减号
\.      英文句点
\!      惊叹号
```

### 特殊字符

[特殊字符编码对照表]: https://www.jb51.net/onlineread/htmlchar.htm

# 附加内容

## 快捷键

```markdown
新建标签 command + t
字体加粗 command + b
字体斜体 command + i
字体变化 command + 数字
字体下划 command + u
附加链接 command + k
选中一行 command + l
选中单词 command + d
全局搜索 command + f
增加缩进 command + ]
减少缩进 command + [
添加脚注 command + option + r
链接引用 command + option + l
有序列表 command + option + o
无序列表 command + option + u
任务列表 command + option + x
文本引用 command + option + q
格式代码 command + option + c
全局替换 command + option + f
创建表格 command + option + t
插入图片 command + control + i 
添加分割 command + shift + -
文本高亮 command + shift + h
大纲视图 command + control + 1
目录视图 command + control + 3
```



[^ 1]: 一种轻量级的标记语言,允许使用易读易写的纯文本格式编写文档,然后转换成有效的HTML文档.

