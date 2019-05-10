---
title: MarkDown画UML图
layout: page
toc: true
date: 2019-05-10
categories: 
- 工具
tags: 
- MarkDown
- UML
---
使用markdown写作的时候，画UML图是基本诉求。如果通过专业的工具来画，再导成图片引入，整套流程下来特别耗时，如果感觉不合适，再调整，时间周期会更长。

MarkDown是可以画UML图的，虽然效果没有专业工具好看，但是，掌握基本语法后，会极大提升写作效率。
<!-- more-->

## plantUml配置

### 安装插件

```
npm install hexo-tag-plantuml --save
```

### 顺序语法介绍
- 关键字start 和stop表示图示的开始和结束
- 活动标签(activity label)以冒号开始，以分号结束【:  ;】

#### 顺序示例代码
~~~
{% plantuml %}
start
:配置Java环境; 
:下载pantuml.jar;
:编写描述文件; 
:执行; 
stop
{% endplantuml %}
~~~
#### 顺序示例效果图
{% plantuml %}
start
:配置Java环境; 
:下载pantuml.jar;
:编写描述文件; 
:执行; 
stop
{% endplantuml %}
### if-else语法介绍
- 关键字if,then和else设置分支测试，关键字elseif可以设置多个分支

#### if-else示例代码
~~~
{% plantuml %}
start
if (判断条件) then (yes)
:执行yes操作;
else (no)
:执行no操作;
endif
stop
{% endplantuml %}
~~~
#### if-else示例效果图
{% plantuml %}
start
if (判断条件) then (yes)
:执行yes操作;
else (no)
:执行no操作;
endif
stop
{% endplantuml %}

### repeat语法介绍
- 可以使用关键字repeat和repeatwhile进行重复循环

#### repeat示例代码
~~~
{% plantuml %}
start
repeat
:int i=1;
:执行i++;
repeat while (i<=100)
stop
{% endplantuml %}
~~~
#### repeat示例效果图
{% plantuml %}
start
repeat
:int i=1;
:执行i++;
repeat while (i<=100)
stop
{% endplantuml %}

### while语法介绍
- 可以使用关键字while和end while进行while循环;

#### while示例代码
~~~
{% plantuml %}
start
:i=1;
while(i<100)
:i++;
endwhile
stop
{% endplantuml %}
~~~
#### while示例效果图
{% plantuml %}
start
:i=1;
while(i<100)
:i++;
endwhile
stop
{% endplantuml %}

### fork语法介绍
- 关键字fork，fork again和end fork表示并行处理

#### fork示例代码
~~~
{% plantuml %}
start
if(条件判断) then (yes)
fork
:并行操作1;
fork again
:并行操作2;
end fork
else(no)
:执行1;
:执行2;
endif
stop
{% endplantuml %}
~~~
#### fork示例效果图
{% plantuml %}
start
if(条件判断) then (yes)
fork
:并行操作1;
fork again
:并行操作2;
end fork
else(no)
:执行1;
:执行2;
endif
stop
{% endplantuml %}

- 文本格式注释
    - title 标题，#color 颜色
    - 使用->标记，你可以给箭头添加文字或者修改箭头颜色

#### ->示例代码
~~~
{% plantuml %}
start
if(条件判断) then (yes)
-[#blue]->
:执行操作1;
-[#green]->
:执行操作2;
else(no)
-[#black]->
:执行1;
endif
stop
{% endplantuml %}
~~~

#### ->示例效果图
{% plantuml %}
start
if(条件判断) then (yes)
-[#blue]->
:执行操作1;
-[#green]->
:执行操作2;
else(no)
-[#black]->
:执行1;
endif
stop
{% endplantuml %}

### 时序图语法介绍
- 用->来绘制参与者之间传递的消息，用 "-->" 绘制一个虚线箭头表示异步消息
    - 用:消息描述
    - 用关键字actor表示参与者  
    - 用database标识数据库
    - 用关键字participant声明参与者
    - 用#RGB值或者颜色名修改 actor 或参与者的背景颜色
    - 用self->self给自己发消息
    - 用[#RGB]修改箭头颜色-[#red]>或-[#red]->
    - 用关键字autonumber用于自动对消息编号
    - 用skinparam命令改变颜色和字体

#### 时序图示例代码
~~~
{% plantuml %}
actor User
participant "流程服务" as A
participant "组合服务" as B
participant "基础服务" as C
User -> A:查询列表
activate A
A -> B: 查询列表
activate B
B -> C: 查询列表
activate C
C --> B: 返回
deactivate C
B-->A:返回
deactivate B
A --> User:返回
deactivate A
{% endplantuml %}
~~~

#### 时序图示例图

{% plantuml %}
actor User
participant "流程服务" as A
participant "组合服务" as B
participant "基础服务" as C
User -> A:查询列表
activate A
A -> B: 查询列表
activate B
B -> C: 查询列表
activate C
C --> B: 返回
deactivate C
B-->A:返回
deactivate B
A --> User:返回
deactivate A
{% endplantuml %}

### 组件图描述
- 组件用[]括号起来

#### 组件图代码

~~~
{% plantuml %}
package "包组合" {
    HTTP - [包一组件]
    [包二组件]
}

node "节点组合" {
    FTP - [节点一组件]
    [包一组件] --> FTP
}

cloud "云服务" {
    [云里一]
}

database "数据库服务" {
    folder "文件夹组合" {
        [文件夹一]
    }
    frame "帧组合" {
        [帧二]
    }
}

[包二组件] --> [云里一]
[云里一] --> [文件夹一]
[文件夹一] --> [帧二]
{% endplantuml %}
~~~

#### 组件图示例

{% plantuml %}
package "包组合" {
    HTTP - [包一组件]
    [包二组件]
}

node "节点组合" {
    FTP - [节点一组件]
    [包一组件] --> FTP
}

cloud "云服务" {
    [云里一]
}

database "数据库服务" {
    folder "文件夹组合" {
        [文件夹一]
    }
    frame "帧组合" {
        [帧二]
    }
}

[包二组件] --> [云里一]
[云里一] --> [文件夹一]
[文件夹一] --> [帧二]
{% endplantuml %}
