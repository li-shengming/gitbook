---
title: MarkDown画UML图
layout: post
toc: true
date: 2019-05-06
categories: 
- 工具
tags: 
- MarkDown
- UML
---
使用markdown写作的时候，画UML图是基本诉求。如果通过专业的工具来画，再导成图片引入，整套流程下来特别耗时，如果感觉不合适，再调整，时间周期会更长。

MarkDown是可以画UML图的，虽然效果没有专业工具好看，但是，掌握基本语法后，会极大提升写作效率。
<!-- more-->

## UML流程图
​
### 安装插件

```
npm install --save hexo-filter-flowchart
```

### _config文件修改
~~~
flowchart:
  # raphael:   # optional, the source url of raphael.js
  # flowchart: # optional, the source url of flowchart.js
  options: # options used for `drawSVG`
~~~

### 示例

```flow
st=>start: Start|past:>http://www.google.com[blank]
e=>end: End:>http://www.google.com
op1=>operation: My Operation|past
op2=>operation: Stuff|current
sub1=>subroutine: My Subroutine|invalid
cond=>condition: Yes
or No?|approved:>http://www.google.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|request

st->op1(right)->cond
cond(yes, right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->e
```

[流程图参考链接](https://github.com/bubkoo/hexo-filter-flowchart/blob/master/README.md)

## UML时序图

### 安装插件

```
npm install --save hexo-filter-flowchart
```

### _config文件修改
~~~
sequence:
  # webfont:     # optional, the source url of webfontloader.js
  # snap:        # optional, the source url of snap.svg.js
  # underscore:  # optional, the source url of underscore.js
  # sequence:    # optional, the source url of sequence-diagram.js
  # css: # optional, the url for css, such as hand drawn theme 
  options: 
    theme: 
    css_class: 
~~~

### 示例

```sequence
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```