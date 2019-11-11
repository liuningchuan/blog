---
title: "玩转IntelliJ IDEA"
tags:
- Java
categories:
- 工具
catalog: true
cover: "/images/header_img/header-9.jpg"
date: 2019-08-04 22:14:24
---

IntelliJ IDEA是Java编程最好的IDE，没有之一，提供了很多强大的功能，而且颜值也很高，唯一美中不足的就是价格不菲，不过好在用教育邮箱可以免费使用，每年授权一次。

IDEA很多快捷键可以极大程度上提供我们的编码效率，下面记录了一些常用的快捷键。对于快捷键我个人的理解是，对于一些常用的我们有必要记下来，很方便，不常用的就没必要熟记，真要用时可以从相应的菜单中找到，毕竟真正的精力还是要放到编码本身中。

### 无处不在的跳转

- 项目之间跳转：**option+command+`** （**Ctrl+Alt+]**）
- 最近的文件(Recent Files)：`command+E`(**Ctrl+E**)
- 最近改变的文件：`shift+command+E`
- 跳转到上次修改的地方：`shift+command+backspace`(**Ctrl+Shift+Backspace**)
- 上次浏览的地方：`option+command+L`(**Ctrl+Alt+L**)，下一次浏览的地方`option+command+R`(**Ctrl+Alt+L**)

### 精准搜索

- 搜索类(Go to class)：`command+O`(**Ctrl+N**)，可以选择包含jar包中所有的类

- 搜索文件(Go to file)：`shift+command+O`(**Ctrl+Shift+N**)

- 根据符号搜索(Go to symbol)：`option+command+O`(**Ctrl+Shift+Alt+N**)

- **double shift**可以快速地根据类、文件、符号搜索

- 根据字符串搜索(Find in path)：`shift+command+F`(**Ctrl+Shift+F**)，比较常用，可以选择是否匹配大小写、字符串是否是个单词、通过正则表达式搜索、指定在哪类文件搜索，还可以选择搜索范围

- 在当前文件中搜索：`command+F`(**Ctrl+F**)

### Alt+Enter

alt+enter虽然只是一个简单的快捷键，但是却拥有极其强大的功能。

- 自动创建函数
- list replace，即当写了一个传统for循环后，alt+enter会帮你自动转换成for-in循环
- 字符串format或者build
- 单词拼写
- 倒入依赖

### 重构

- 当需要对某个变量统一修改时，将光标移到变量名称上，`shift+F6`就可以快速修改
- `command+F6`修改函数签名(Change Signature)，可以对函数名称、返回值、参数等进行修改

### 视图查看

- `command+F12`(**Ctrl+F12**)查看类结构，包括方法、参数、返回值等
- `option+shift+command+U`(**Ctrl+Alt+Shift+U**)查看依赖拓扑图，如果是类文件会展示类继承、接口实现、包括注解之间的关系；如果是pom.xml文件，则会显示工程的依赖树。也可以通过右击**Show Diagrams**查看依赖拓扑图
- `control+H`(**Ctrl+H**)显示类的继承结构(Type Hierarchy)，Type Hierarchy是IDEA的一个侧边菜单，`command+8`(**Alt+8)**也可查看继承结构
- `F1`(**Ctrl+Q**)查看注释文档
- **Alt+left/right**在已打开的tab之间切换，**Alt+up/down**在上一个/下一个方法之间切换
- **F2**高亮错误或警告快速定位

### 其他

- `option+command+L`(Ctrl+Alt+L)格式化代码
- **Alt+Insert**生成代码(如get,set方法,Constructor,toString()等)
- `command+R`(**Ctrl+R**)替换文本
- `command+/`(**Ctrl+/**) 注释代码（// 或者/*...*/ ）



