#  [ Spring Boot(四)：Thymeleaf 使用详解
](https://www.cnblogs.com/ityouknow/p/5833560.html)

在上篇文章[Spring Boot (二)：Web
综合开发](http://www.ityouknow.com/springboot/2016/02/03/spring-boot-
web.html)中简单介绍了一下 Thymeleaf，这篇文章将更加全面详细的介绍 Thymeleaf 的使用。Thymeleaf 是新一代的模板引擎，在
Spring4.0 中推荐使用 Thymeleaf 来做前端模版引擎。

## Thymeleaf 介绍

简单说，Thymeleaf 是一个跟 Velocity、FreeMarker 类似的模板引擎，它可以完全替代 JSP
。相较与其他的模板引擎，它有如下三个极吸引人的特点：

  * 1.Thymeleaf 在有网络和无网络的环境下皆可运行，即它可以让美工在浏览器查看页面的静态效果，也可以让程序员在服务器查看带数据的动态页面效果。这是由于它支持 html 原型，然后在 html 标签里增加额外的属性来达到模板+数据的展示方式。浏览器解释 html 时会忽略未定义的标签属性，所以 Thymeleaf 的模板可以静态地运行；当有数据返回到页面时，Thymeleaf 标签会动态地替换掉静态内容，使页面动态显示。 
  * 2.Thymeleaf 开箱即用的特性。它提供标准和 Spring 标准两种方言，可以直接套用模板实现 JSTL、 OGNL表达式效果，避免每天套模板、改 Jstl、改标签的困扰。同时开发人员也可以扩展和创建自定义的方言。 
  * 3.Thymeleaf 提供 Spring 标准方言和一个与 SpringMVC 完美集成的可选模块，可以快速的实现表单绑定、属性编辑器、国际化等功能。

## 标准表达式语法

它们分为四类：

  * 1.变量表达式
  * 2.选择或星号表达式
  * 3.文字国际化表达式
  * 4.URL 表达式

### 变量表达式

变量表达式即 OGNL 表达式或 Spring EL 表达式(在 Spring 术语中也叫 model attributes)。如下所示：  
`${session.user.name}`

它们将以HTML标签的一个属性来表示：

    
    
    <span th:text="${book.author.name}">
    <li th:each="book : ${books}">
    

### 选择(星号)表达式

选择表达式很像变量表达式，不过它们用一个预先选择的对象来代替上下文变量容器(map)来执行，如下：  
`*{customer.name}`

被指定的 object 由 th:object 属性定义：

    
    
    <div th:object="${book}">
      ...
      <span th:text="*{title}">...</span>
      ...
    </div>
    

### 文字国际化表达式

文字国际化表达式允许我们从一个外部文件获取区域文字信息(.properties)，用 Key 索引 Value，还可以提供一组参数(可选).

    
    
    #{main.title}
    #{message.entrycreated(${entryId})}
    

可以在模板文件中找到这样的表达式代码：

    
    
    <table>
      ...
      <th th:text="#{header.address.city}">...</th>
      <th th:text="#{header.address.country}">...</th>
      ...
    </table>
    

### URL 表达式

URL 表达式指的是把一个有用的上下文或回话信息添加到 URL，这个过程经常被叫做 URL 重写。  
`@{/order/list}`

URL还可以设置参数：  
`@{/order/details(id=${orderId})}`

相对路径：  
`@{../documents/report}`

让我们看这些表达式：

    
    
    <form th:action="@{/createOrder}">
    <a href="main.html" th:href="@{/main}">
    

### 变量表达式和星号表达有什么区别吗？

如果不考虑上下文的情况下，两者没有区别；星号语法评估在选定对象上表达，而不是整个上下文  
什么是选定对象？就是父标签的值，如下：

    
    
    <div th:object="${session.user}">
      <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
      <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
      <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
    </div>
    

这是完全等价于：

    
    
    <div th:object="${session.user}">
      <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
      <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
      <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p>
    </div>
    

当然，美元符号和星号语法可以混合使用：

    
    
      <div th:object="${session.user}">
    	  <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
      	  <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
          <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
      </div>
    

### 表达式支持的语法

#### 字面（Literals）

  * 文本文字（Text literals）: `'one text', 'Another one!',…`
  * 数字文本（Number literals）: `0, 34, 3.0, 12.3,…`
  * 布尔文本（Boolean literals）:`true, false`
  * 空（Null literal）:`null`
  * 文字标记（Literal tokens）:`one, sometext, main,…`

#### 文本操作（Text operations）

  * 字符串连接(String concatenation):`+`
  * 文本替换（Literal substitutions）:`|The name is ${name}|`

#### 算术运算（Arithmetic operations）

  * 二元运算符（Binary operators）:`+, -, *, /, %`
  * 减号（单目运算符）Minus sign (unary operator):`-`

#### 布尔操作（Boolean operations）

  * 二元运算符（Binary operators）:`and, or`
  * 布尔否定（一元运算符）Boolean negation (unary operator):`!, not`

#### 比较和等价(Comparisons and equality)

  * 比较（Comparators）:`>, <, >=, <= (gt, lt, ge, le)`
  * 等值运算符（Equality operators）:`==, != (eq, ne)`

#### 条件运算符（Conditional operators）

  * If-then:`(if) ? (then)`
  * If-then-else:`(if) ? (then) : (else)`
  * Default: (value) ?:`(defaultvalue)`

所有这些特征可以被组合并嵌套：

    
    
    'User is of type ' + (${user.isAdmin()} ? 'Administrator' : (${user.type} ?: 'Unknown'))
    

## 常用th标签都有那些？

关键字 | 功能介绍 | 案例  
---|---|---  
th:id | 替换id | `<input th:id="'xxx' + ${collect.id}"/>`  
th:text | 文本替换 | `<p th:text="${collect.description}">description</p>`  
th:utext | 支持html的文本替换 | `<p th:utext="${htmlcontent}">conten</p>`  
th:object | 替换对象 | `<div th:object="${session.user}"> `  
th:value | 属性赋值 | `<input th:value="${user.name}" /> `  
th:with | 变量赋值运算 | `<div th:with="isEven=${prodStat.count}%2==0"></div> `  
th:style | 设置样式 | `th:style="'display:' + @{(${sitrue} ? 'none' : 'inline-
block')} + ''" `  
th:onclick | 点击事件 | `th:onclick="'getCollect()'" `  
th:each | 属性赋值 | `tr th:each="user,userStat:${users}"> `  
th:if | 判断条件 | `<a th:if="${userId == collect.userId}" >`  
th:unless | 和th:if判断相反 | `<a th:href="@{/login}" th:unless=${session.user !=
null}>Login</a> `  
th:href | 链接地址 | `<a th:href="@{/login}" th:unless=${session.user !=
null}>Login</a> /> `  
th:switch | 多路选择 配合th:case 使用 | `<div th:switch="${user.role}"> `  
th:case | th:switch的一个分支 | `<p th:case="'admin'">User is an administrator</p>`  
th:fragment | 布局标签，定义一个代码片段，方便其它地方引用 | `<div th:fragment="alert">`  
th:include | 布局标签，替换内容到引入的文件 | `<head th:include="layout :: htmlhead"
th:with="title='xx'"></head> /> `  
th:replace | 布局标签，替换整个标签到引入的文件 | `<div th:replace="fragments/header ::
title"></div> `  
th:selected | selected选择框 选中 | `th:selected="(${xxx.id} == ${configObj.dd})"`  
th:src | 图片类地址引入 | `<img class="img-responsive" alt="App Logo"
th:src="@{/img/logo.png}" /> `  
th:inline | 定义js脚本可以使用变量 | `<script type="text/javascript"
th:inline="javascript">`  
th:action | 表单提交的地址 | `<form action="subscribe.html"
th:action="@{/subscribe}">`  
th:remove | 删除某个属性 | `<tr th:remove="all">
1.all:删除包含标签和所有的孩子。2.body:不包含标记删除,但删除其所有的孩子。3.tag:包含标记的删除,但不删除它的孩子。4.all-but-
first:删除所有包含标签的孩子,除了第一个。5.none:什么也不做。这个值是有用的动态评估。`  
th:attr | 设置标签属性，多个属性可以用逗号分隔 |
比如`th:attr="src=@{/image/aa.jpg},title=#{logo}"`，此标签不太优雅，一般用的比较少。  
  
还有非常多的标签，这里只列出最常用的几个,由于一个标签内可以包含多个th:x属性，其生效的优先级顺序为:  
`include,each,if/unless/switch/case,with,attr/attrprepend/attrappend,value/href,src
,etc,text/utext,fragment,remove。 `

## 几种常用的使用方法

### 1、赋值、字符串拼接

    
    
    <p  th:text="${collect.description}">description</p>
    <span th:text="'Welcome to our application, ' + ${user.name} + '!'">
    

字符串拼接还有另外一种简洁的写法

    
    
    <span th:text="|Welcome to our application, ${user.name}!|">
    

### 2、条件判断 If/Unless

Thymeleaf中使用th:if和th:unless属性进行条件判断，下面的例子中，`<a>`标签只有在`th:if`中条件成立时才显示：

    
    
    <a th:if="${myself=='yes'}" > </i> </a>
    <a th:unless=${session.user != null} th:href="@{/login}" >Login</a>
    

`th:unless` 于 `th:if` 恰好相反，只有表达式中的条件不成立，才会显示其内容。

也可以使用 `(if) ? (then) : (else)`这种语法来判断显示的内容

### 3、for 循环

    
    
    <tr  th:each="collect,iterStat : ${collects}">
       <th scope="row" th:text="${collect.id}">1</th>
       <td >
          <img th:src="${collect.webLogo}"/>
       </td>
       <td th:text="${collect.url}">Mark</td>
       <td th:text="${collect.title}">Otto</td>
       <td th:text="${collect.description}">@mdo</td>
       <td th:text="${terStat.index}">index</td>
    </tr>
    

iterStat称作状态变量，属性有：

  * index:当前迭代对象的 index（从0开始计算）
  * count: 当前迭代对象的 index(从1开始计算)
  * size:被迭代对象的大小
  * current:当前迭代变量
  * even/odd:布尔值，当前循环是否是偶数/奇数（从0开始计算）
  * first:布尔值，当前循环是否是第一个
  * last:布尔值，当前循环是否是最后一个

### 4、URL

URL 在 Web 应用模板中占据着十分重要的地位，需要特别注意的是 Thymeleaf 对于 URL 的处理是通过语法 `@{...}` 来处理的。  
如果需要 Thymeleaf 对 URL 进行渲染，那么务必使用 `th:href`，`th:src` 等属性，下面是一个例子

    
    
    <!-- Will produce 'http://localhost:8080/standard/unread' (plus rewriting) -->
     <a  th:href="@{/standard/{type}(type=${type})}">view</a>
    
    <!-- Will produce '/gtvg/order/3/details' (plus rewriting) -->
    <a href="details.html" th:href="@{/order/{orderId}/details(orderId=${o.id})}">view</a>
    

设置背景

    
    
    <div th:style="'background:url(' + @{/<path-to-image>} + ');'"></div>
    

根据属性值改变背景

    
    
     <div class="media-object resource-card-image"  th:style="'background:url(' + @{(${collect.webLogo}=='' ? 'img/favicon.png' : ${collect.webLogo})} + ')'" ></div>
    

几点说明：

  * 上例中 URL 最后的`(orderId=${o.id}) `表示将括号内的内容作为 URL 参数处理，该语法避免使用字符串拼接，大大提高了可读性
  * `@{...}`表达式中可以通过`{orderId}`访问 Context 中的 orderId 变量
  * `@{/order}`是 Context 相关的相对路径，在渲染时会自动添加上当前 Web 应用的 Context 名字，假设 context 名字为 app，那么结果应该是 `/app/order`

### 5、内联 js

内联文本：[[...]]
内联文本的表示方式，使用时，必须先用`th:inline="text/javascript/none"`激活，`th:inline`可以在父级标签内使用，甚至作为
body 的标签。内联文本尽管比`th:text`的代码少，不利于原型显示。

    
    
    <script th:inline="javascript">
    /*<![CDATA[*/
    ...
    var username = /*[[${sesion.user.name}]]*/ 'Sebastian';
    var size = /*[[${size}]]*/ 0;
    ...
    /*]]>*/
    </script>
    

js 附加代码：

    
    
    /*[+
    var msg = 'This is a working application';
    +]*/
    

js 移除代码：

    
    
    /*[- */
    var msg = 'This is a non-working template';
    /* -]*/
    

### 6、内嵌变量

为了模板更加易用，Thymeleaf 还提供了一系列 Utility 对象（内置于 Context 中），可以通过 # 直接访问：

  * dates ： _java.util.Date的功能方法类。_
  * calendars : _类似#dates，面向java.util.Calendar_
  * numbers : _格式化数字的功能方法类_
  * strings : _字符串对象的功能类，contains,startWiths,prepending/appending等等。_
  * objects: _对objects的功能类操作。_
  * bools: _对布尔值求值的功能方法。_
  * arrays： _对数组的功能类方法。_
  * lists: _对lists功能类方法_
  * sets
  * maps  
...

下面用一段代码来举例一些常用的方法：

#### dates

    
    
    /*
     * Format date with the specified pattern
     * Also works with arrays, lists or sets
     */
    ${#dates.format(date, 'dd/MMM/yyyy HH:mm')}
    ${#dates.arrayFormat(datesArray, 'dd/MMM/yyyy HH:mm')}
    ${#dates.listFormat(datesList, 'dd/MMM/yyyy HH:mm')}
    ${#dates.setFormat(datesSet, 'dd/MMM/yyyy HH:mm')}
    
    /*
     * Create a date (java.util.Date) object for the current date and time
     */
    ${#dates.createNow()}
    
    /*
     * Create a date (java.util.Date) object for the current date (time set to 00:00)
     */
    ${#dates.createToday()}
    

#### strings

    
    
    /*
     * Check whether a String is empty (or null). Performs a trim() operation before check
     * Also works with arrays, lists or sets
     */
    ${#strings.isEmpty(name)}
    ${#strings.arrayIsEmpty(nameArr)}
    ${#strings.listIsEmpty(nameList)}
    ${#strings.setIsEmpty(nameSet)}
    
    /*
     * Check whether a String starts or ends with a fragment
     * Also works with arrays, lists or sets
     */
    ${#strings.startsWith(name,'Don')}                  // also array*, list* and set*
    ${#strings.endsWith(name,endingFragment)}           // also array*, list* and set*
    
    /*
     * Compute length
     * Also works with arrays, lists or sets
     */
    ${#strings.length(str)}
    
    /*
     * Null-safe comparison and concatenation
     */
    ${#strings.equals(str)}
    ${#strings.equalsIgnoreCase(str)}
    ${#strings.concat(str)}
    ${#strings.concatReplaceNulls(str)}
    
    /*
     * Random
     */
    ${#strings.randomAlphanumeric(count)}
    
    

## 使用 Thymeleaf 布局

Spring Boot 2.0 将布局单独提取了出来，需要单独引入依赖：thymeleaf-layout-dialect。

    
    
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <dependency>
        <groupId>nz.net.ultraq.thymeleaf</groupId>
        <artifactId>thymeleaf-layout-dialect</artifactId>
    </dependency>
    

定义代码片段

    
    
    <footer th:fragment="copy">
    &copy; 2019
    </footer>
    

在页面任何地方引入：

    
    
    <body>
        <div th:insert="layout/copyright :: copyright"></div>
        <div th:replace="layout/copyright :: copyright"></div>
    </body>
    

th:insert 和 th:replace 区别，insert 只是加载，replace 是替换。Thymeleaf 3.0 推荐使用 th:insert
替换 2.0 的 th:replace。

返回的 HTML 如下：

    
    
    <body>
       <div> &copy; 2019 </div>
      <footer>&copy; 2019 </footer>
    </body>
    

下面是一个常用的后台页面布局，将整个页面分为头部，尾部、菜单栏、隐藏栏，点击菜单只改变 content 区域的页面

    
    
    <body class="layout-fixed">
      <div th:fragment="navbar"  class="wrapper"  role="navigation">
    	<div th:replace="fragments/header :: header">Header</div>
    	<div th:replace="fragments/left :: left">left</div>
    	<div th:replace="fragments/sidebar :: sidebar">sidebar</div>
    	<div layout:fragment="content" id="content" ></div>
    	<div th:replace="fragments/footer :: footer">footer</div>
      </div>
    </body>
    

任何页面想使用这样的布局值只需要替换中见的 content 模块即可

    
    
    <html xmlns:th="http://www.thymeleaf.org" layout:decorator="layout">
     <body>
        <section layout:fragment="content">
      ...
    

也可以在引用模版的时候传参

    
    
    <head th:include="layout :: htmlhead" th:with="title='Hello'"></head>
    

layout 是文件地址，如果有文件夹可以这样写`fileName/layout:htmlhead`，htmlhead 是指定义的代码片段
如`th:fragment="copy"`

**文章示例项目**

**[示例代码-github](https://github.com/ityouknow/spring-boot-
examples/tree/master/spring-boot-thymeleaf)**

**[示例代码-码云](https://gitee.com/ityouknow/spring-boot-
examples/tree/master/spring-boot-thymeleaf)**

> 文章内容已经升级到 Spring Boot 2.x

## 参考

[thymeleaf官方指南](http://www.thymeleaf.org/doc/tutorials/2.1/thymeleafspring.html#integrating-
thymeleaf-with-spring)  
[新一代Java模板引擎Thymeleaf](http://www.tianmaying.com/tutorial/using-thymeleaf)  
[Thymeleaf基本知识](http://www.webinno.cn/blog/article/view/131)  
[thymeleaf总结文章](http://v8en.com/news/list/47/0)  
[Thymeleaf 模板的使用](http://www.cnblogs.com/lazio10000/p/5603955.html)  
[thymeleaf
学习笔记](http://www.blogjava.net/bjwulin/archive/2013/02/07/395234.html)

## 资料推荐

最近又赶上跳槽的高峰期（招聘旺季），好多读者都问我要有没有最新面试题，找华为朋友整理一份内部资料《第6版：互联网大厂面试题》并分类 4 份 PDF，累计
926 页！

整个资料包，包括 Spring、Spring
Boot/Cloud、Dubbo、JVM、集合、多线程、JPA、MyBatis、MySQL、大数据、Nginx、Git、Docker、GitHub、Servlet、JavaWeb、IDEA、Redis、算法、面试题等几乎覆盖了
Java 基础和阿里巴巴等大厂面试题等、等技术栈！

![](https://img2020.cnblogs.com/blog/331425/202101/331425-20210113163923413-362460571.png)

据说已经有小伙伴通过这套资料，成功的入职了蚂蚁金服、字节跳动等大厂。

![](https://img2020.cnblogs.com/blog/331425/202101/331425-20210113163947485-10139155.png)

而且，这些资料不是扫描版的，里面的文字都可以直接复制，非常便于我们学习：

![](https://img2020.cnblogs.com/blog/331425/202101/331425-20210113163954838-1960031763.png)

如果你想获得完整PDF可以通过以下方式获得

面试大全怎么获取：

  1. 关注下方公众号
  2. 在下方公众号后台回复 【555】 即可。  
![](https://img2020.cnblogs.com/blog/331425/202101/331425-20210113163912945-917121074.png)  
👆长按上方二维码 2 秒  
回复「555」即可获取资料

  
作者：[纯洁的微笑](http://www.cnblogs.com/ityouknow/)  
出处：[www.ityouknow.com](http://www.ityouknow.com/)  
资源：微信搜 **【纯洁的微笑】** 关注我，回复 **【程序员】【面试】【架构师】** 有我准备的一线程序必备计算机书籍、大厂面试资料和免费电子书。
[**一共1024G的资料，希望可以帮助大家提升技术和能力。**](http://www.ityouknow.com/share/2020/04/16/resource-
sharing.html)  
  
本文如对您有帮助，还请多帮 【推荐】 下此文。  
[点我了解：Tooool-程序员一站式导航网站](http://tooool.org/)  

分类: [springboot](https://www.cnblogs.com/ityouknow/category/914493.html)

标签: [springboot
thymeleaf](https://www.cnblogs.com/ityouknow/tag/springboot%20thymeleaf/)

[好文要顶](javascript:void\(0\);) [关注我](javascript:void\(0\);)
[收藏该文](javascript:void\(0\);)
[![](https://common.cnblogs.com/images/icon_weibo_24.png)](javascript:void\(0\);
"分享至新浪微博")
[![](https://common.cnblogs.com/images/wechat.png)](javascript:void\(0\);
"分享至微信")

[![](https://pic.cnblogs.com/face/331425/20130328094900.png)](https://home.cnblogs.com/u/ityouknow/)

[纯洁的微笑](https://home.cnblogs.com/u/ityouknow/)  
[关注 - 0](https://home.cnblogs.com/u/ityouknow/followees/)  
[粉丝 - 6665](https://home.cnblogs.com/u/ityouknow/followers/)

推荐博客

[+加关注](javascript:void\(0\);)

65

1

[« ](https://www.cnblogs.com/ityouknow/p/5772248.html) 上一篇：
[程序员该用哪种姿势来理财](https://www.cnblogs.com/ityouknow/p/5772248.html "发布于
2016-08-15 11:18")  
[» ](https://www.cnblogs.com/ityouknow/p/5891443.html) 下一篇： [Spring
Boot(五)：Spring Boot Jpa 的使用](https://www.cnblogs.com/ityouknow/p/5891443.html
"发布于 2016-09-21 09:36")

posted @ 2016-09-02 15:09  [纯洁的微笑](https://www.cnblogs.com/ityouknow/)
阅读(234181)  评论(7)  [编辑](https://i.cnblogs.com/EditPosts.aspx?postid=5833560)
[收藏](javascript:void\(0\))  [举报](javascript:void\(0\))

