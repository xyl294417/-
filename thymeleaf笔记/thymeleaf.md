

## thymeleaf笔记

### 一、shymelaef是什么

 简单说， Thymeleaf 是一个跟 Velocity、FreeMarker 类似的模板引擎，它可以完全替代 JSP 。相较与其他的模板引擎，它有如下三个极吸引人的特点：

​    1.Thymeleaf 在有网络和无网络的环境下皆可运行，即它可以让美工在浏览器查看页面的静态效果，也可以让程序员在服务器查看带数据的动态页面效果。这是由于它支持 html 原型，然后在 html 标签里增加额外的属性来达到模板+数据的展示方式。浏览器解释 html 时会忽略未定义的标签属性，所以 thymeleaf 的模板可以静态地运行；当有数据返回到页面时，Thymeleaf 标签会动态地替换掉静态内容，使页面动态显示。

​    2.Thymeleaf 开箱即用的特性。它提供标准和spring标准两种方言，可以直接套用模板实现JSTL、 OGNL表达式效果，避免每天套模板、该jstl、改标签的困扰。同时开发人员也可以扩展和创建自定义的方言。

   3. Thymeleaf 提供spring标准方言和一个与 SpringMVC 完美集成的可选模块，可以快速的实现表单绑定、属性编辑器、国际化等功能。

      

### 二、应用

#### 1、简单的 Thymeleaf 应用

​	（1）只需加入thymeleaf-2.1.4.RELEASE.jar（http://www.thymeleaf.org/download.html ）包，

​		若用maven，则加入如下配置：

~~~ xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf</artifactId>
    <version>2.1.4</version>
</dependency>
~~~

​	（2）然后添加头文件（如下）

~~~html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
~~~

​	(3)就可以用th标签动态替换掉静态数据了。如下图，后台传出的message会将静态数据“Red Chair”替换掉，若访问静态页面，则显示数据“Red Chair”。

~~~html
<td th:text="${message}">Red Chair</td>
~~~

#### 2、.整合spring

​	 （1）加入thymeleaf-spring4-2.1.4.RELEASE.jar（<http://www.thymeleaf.org/download.html> ）包，

​		若用maven，则加入如下配置:

~~~xml
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring3</artifactId>
    <version>2.1.4</version>
</dependency>
~~~

​	（2）在servlet 中配置加入如下代码

~~~xml
<!-- Scans the classpath of this application for @Components to deploy as beans -->
       <context:component-scan base-package="com.test.thymeleaf.controller" />

       <!-- Configures the @Controller programming model -->
       <mvc:annotation-driven />

        <!--Resolves view names to protected .jsp resources within the /WEB-INF/views directory -->
        <!--springMVC+jsp的跳转页面配置-->
       <!--<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">-->
              <!--<property name="prefix" value="/WEB-INF/views/" />-->
              <!--<property name="suffix" value=".jsp" />-->
       <!--</bean>-->

       <!--springMVC+thymeleaf的跳转页面配置-->
       <bean id="templateResolver"
          class="org.thymeleaf.templateresolver.ServletContextTemplateResolver">
         <property name="prefix" value="/WEB-INF/views/" />
         <property name="suffix" value=".html" />
         <property name="templateMode" value="HTML5" />
       </bean>

       <bean id="templateEngine"
           class="org.thymeleaf.spring4.SpringTemplateEngine">
          <property name="templateResolver" ref="templateResolver" />
       </bean>

       <bean class="org.thymeleaf.spring4.view.ThymeleafViewResolver">
         <property name="templateEngine" ref="templateEngine" />
       </bean>
~~~

（3）将静态页面加到项目中，更改文件头，加入th标签即可。

### 三、thymeleaf 基本语法:

#### 1、简单表达

- `${...}` : 变量表达式。

  ```html
  <%--引用user对象的name属性--%>
  <input type="text" name="userName" value="James Carrot" th:value="${user.name}" />
  ```

- `*{...}` : 选择表达式。

  ~~~html
  <div th:object="${session.user}">                                                               
       <p>姓名: <span th:text="*{name}">Saturn</span>.</p>
       <p>age: <span th:text="*{age}">Saturn</span>.</p>    
  </div>
  ~~~

  相当于:

  ~~~html
  <div>                                                               
       <p>姓名: <span th:text="${session.user.name}">Saturn</span>.</p>
       <p>age: <span th:text="${{session.user.age}">Saturn</span>.</p>    
  </div>
  ~~~

- `#{...}` :文字国际化表达式

  ~~~htmL
  <p th:utext="#{home.welcome}">Welcome to our grocery store!</p>
  ~~~

  调用国际化的welcome , 国际化资源文件如下：

  ~~~properties
  
  resource_en_US.properties：
  home.welcome=Welcome to here！
  
  resource_zh_CN.properties：
  home.welcome=欢迎您的到来！
  ~~~

  

- `@{...}` : 链接 (URL) 表达式

  @{……}支持决定路径和相对路径。其中相对路径又支持跨上下文调用url和协议的引用（//code.jquery.com/jquery-2.0.3.min.js）。

  ~~~html
  <a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>
  ~~~

  当URL为后台传出的参数时，代码如下:

  可以使用表达式来指定URL参数（`orderId=${o.id}`）如果需要多个参数，这些参数将用逗号分隔：

  ~~~html
  <img src="../../static/assets/images/qr-code.jpg" th:src="@{${path}}" alt="二维码" />
  <a th:href="@{${path}(orderId=${o.id})}">view</a>
  <a th:href="@{'/details/'+${path}(orderId=${o.id}，id = ${o.id} )}">view</a>
  ~~~

- `~{...}` : 片段表达式。







#### 2、常用th标签

##### 	2.1、 简单数据转换（数字，日期）

~~~html
	<dt>价格</dt>
  　 <dd th:text="${#numbers.formatDecimal(product.price, 1, 2)}">180</dd>
　　 <dt>进货日期</dt>
　　 <dd th:text="${#dates.format(product.availableFrom, 'yyyy-MM-dd')}">2014-12-01</dd>
~~~

##### 	2.2、字符串拼接(+)和文本替换(|...|)

~~~html
<dd th:text="${'$'+product.price}">235</dd>
~~~

##### 	2.3 转义和非转义文本（th:text    th:utext不会执行任何转义）

~~~html
<%-- 后台传来的html 值为 </h1>测试</h1>--%>
<div th:text="${html}">
　　内容为："</h1>测试</h1>"
</div> 
<div th:utext="${html}">
　　内容为：</h1>测试</h1>
</div>
~~~

##### 	2.4、如后台传过来的只是一个对象可以这样写

~~~html
<form th:action="@{/${url}}" th:object="${user}" method="post" th:method="post">
    <input type="text" th:field="*{name}"/>
    <input type="text" th:field="*{msg}"/>
    <input type="submit"/>
</form>
~~~

##### 	2.5、在页面中迭代数据

~~~html
	用 th:remove 移除除了第一个外的静态数据，用第一个tr标签进行循环迭代显示
　　　　<tbody th:remove="all-but-first">
　　　　　	//将后台传出的 productList 的集合进行迭代，用product参数接收，通过product访问属性值
            <tr th:each="product:${productList}">
                //用count进行统计，有顺序的显示
                <td th:text="${productStat.count}">1</td>
                <td th:text="${product.description}">Red Chair</td>
                <td th:text="${'$' + #numbers.formatDecimal(product.price, 1, 2)}">$123</td>
                <td th:text="${#dates.format(product.availableFrom, 'yyyy-MM-dd')}">
                    2014-12-01
                </td>
            </tr>
            <tr>
                <td>White table</td>
                <td>$200</td>
                <td>15-Jul-2013</td>
            </tr>
            <tr>
                <td>Reb table</td>
                <td>$200</td>
                <td>15-Jul-2013</td>
            </tr>
            <tr>
                <td>Blue table</td>
                <td>$200</td>
                <td>15-Jul-2013</td>
            </tr>
      </tbody>
~~~

##### 2.6、移除代码片段(th:remove)具体例子请查看2.5

- `all`：删除包含标记及其所有子标记。
- `body`：不要删除包含标记，但删除其所有子标记。
- `tag`：删除包含标记，但不删除其子项。
- `all-but-first`：删除除第一个之外的所有包含标记的子项。
- `none`： 没做什么。此值对于动态评估很有用。



##### 2.7、条件判断(th:if    th:unless   th:switch  th:case)

​	2.7.1、th:if   

~~~html
//如果price 大于 100 就显示这个span标签
<span th:if="${product.price lt 100}" class="offer">Special offer!</span>
~~~

​	2.7.2、th:unless

~~~html
<!--在页面先显示，然后再在显示的数据基础上进行修改-->
<div class="form-group col-lg-6">
    <label>姓名<span>&nbsp;</span></label>
 　　<！--除非resume对象的name属性值为null，否则就用name的值作为placeholder值-->
    <input type="text" class="form-control" th:unless="${resumes.name} eq '' or ${resumes.name} eq null"  
           data-required="true" th:placeholder="${resumes.name}" />
　　 <!--除非resume对象的name属性不为空，否则就定义一个field方便封装对象，并用placeholder提示-->
    <input type="text" th:field="${resume.name}" class="form-control" th:unless="${resumes.name} ne null" 
           data-required="true" th:placeholder="请填写您的真实姓名"  />
</div>
~~~

​	2.7.3、th:switch th:case

~~~html
<!-- 当gender存在时，选择对应的选项；若gender不存在或为null时，取得customer对象的name-->
<td th:switch="${customer.gender?.name()}">
    <img th:case="'MALE'" src="../../../images/male.png" th:src="@{/images/male.png}" alt="Male" /> 
    <img th:case="'FEMALE'" src="../../../images/female.png" th:src="@{/images/female.png}" alt="Female" /> 
    <span th:case="*">以上条件都不满足显示</span>
</td>
~~~

##### 2.8、循环迭代（th:each）

- 当前*迭代索引*，从0开始。这是`index`属性。
- 当前*迭代索引*，从1开始。这是`count`属性。
- 迭代变量中元素的总量。这是`size`属性。
- 每次迭代的变量。这是`current`属性。
- 当前迭代是偶数还是奇数。这些是`even/odd` 返回值 布尔类型。
- 当前迭代是否是第一个。这是`first` 返回值 布尔类型。
- 当前迭代是否是最后一次。这是`last` 返回值 布尔类型。

```html
<table>
  <tr>
    <th>NAME</th>
    <th>PRICE</th>
    <th>IN STOCK</th>
  </tr>
  <tr th:each="item,stat : ${prods}" th:class="${stat.odd}? 'odd'">
   	<td>[(${sta.count})]</td>
    <td th:text="${prod.name}">Onions</td>
    <td th:text="${prod.price}">2.41</td>
    <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
  </tr>
</table>
```



##### 2.9、根据后台数据选中select的选项

​	用th:switch指定传出的变量,用th:case对变量的值进行匹配。不能将 "请选择" 放在第一项  否则会出现永远选择的是这个选项。

~~~html
<div class="form-group col-lg-6">
      <label >性别<span>&nbsp;Sex:</span></label>
      <select th:field="${user.gender}" class="form-control" th:switch="${resumes.gender}"
            data-required="true">
              <option value="男" th:case="'1'" th:selected="selected" >男</option>
              <option value="女" th:case="'2'" th:selected="selected" >女</option>
              <option value="">请选择</option>
      </select>
 </div>
~~~

~~~html
<select class="form-control" name="diyuid">
	<option value="0">全部</option>
	<option th:each="r : ${genderList}" th:value="${r.name}" th:text="${r.value}" th:selected="${r.name} == ${user.gender}"></option>
</select>
~~~

还可以这么写:

~~~html
<div class='form-group col-lg-4'>
    <select class='form-control' name="skill[4].proficiency">
        <option >掌握程度</option>
        <option th:if="${skill.level eq '1'}" th:selected="selected">一般</option>
        <option th:if="${skill.level eq '2'}" th:selected="selected">熟练</option>
        <option th:if="${skill.level eq '3'}" th:selected="selected">精通</option>
    </select>
</div>
~~~







#### 3、spring表达式语言

~~~html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
    <head>
        <title>Thymeleaf tutorial: exercise 10</title>
        <link rel="stylesheet" href="../../../css/main-static.css" th:href="@{/css/main.css}" />
        <meta charset="utf-8" />
    </head>
    <body>
        <h1>Thymeleaf tutorial - Solution for exercise 10: Spring Expression language</h1>
  
        <h2>Arithmetic expressions</h2>
        <p class="label">Four multiplied by minus six multiplied by minus two module seven:</p>
        <p class="answer" th:text="${4 * -6 * -2 % 7}">123</p>
 
        <h2>Object navigation</h2>
        <p class="label">Description field of paymentMethod field of the third element of customerList bean:</p>
        <p class="answer" th:text="${customerList[2].paymentMethod.description}">Credit card</p>
 
        <h2>Object instantiation</h2>
        <p class="label">Current time milliseconds:</p>
        <p class="answer" th:text="${new java.util.Date().getTime()}">22-Jun-2013</p>
        
        <h2>T operator</h2>
        <p class="label">Random number:</p>
        <p class="answer" th:text="${T(java.lang.Math).random()}">123456</p>
    </body>
</html>
~~~



#### 4、内联

##### 	4.1、内联表达式

​	`[[...]]` 相当于`th:text`

```html
<p>Hello, [[${user.name}]]!</p>
```

​	`[(...)]`相当于 `th:utext`

```html
<p>The message is "[(${msg})]"</p>
```



##### 	4.2、js内联

#### 5、优先级

![](https://i.imgur.com/oynwWHJ.png)

### 四、

#### 1、运算符 变量

文本：

- 文本文字：`'one text'`，`'Another one!'`，...
- 数字：`0`，`34`，`3.0`，`12.3`，...
- 布尔文字：`true`，`false`
- 空： `null`
- 文字标记：`one`，`sometext`，`main`，...

文本操作：

- 字符串连接： `+`
- 文字替换： `|The name is ${name}|`

算术运算：

- 二元运算符：`+`，`-`，`*`，`/`，`%`
- 减号（一元运算符）： `-`

布尔运算：

- 二元运算符：`and`，`or`
- 布尔否定（一元运算符）： `!`，`not`

比较运算符：

- 比较：`>`，`<`，`>=`，`<=`（`gt`，`lt`，`ge`，`le`）
- 相等比较表达式：`==`，`!=`（`eq`，`ne`）

条件运算符：

- IF-THEN： `(if) ? (then)`
- IF-THEN-ELSE： `(if) ? (then) : (else)`
- 默认： `(value) ?: (defaultvalue)`
- 例子：`User is of type ' + (${user.isAdmin()} ? 'Administrator' : (${user.type} ?: 'Unknown'))`

特殊符号：

- 无操作： `_`

#### 2、表达式基本对象

 2.1、在上下文变量评估OGNL表达式时，一些对象表达式可获得更高的灵活性。这些对象将由#号开始引用

- #ctx: 上下文对象.

- #vars: 上下文变量.

- #locale: 上下文语言环境.

- #httpServletRequest: (仅在web上文)HttpServletRequest 对象.

- #httpSession: (仅在web上文)  HttpSession 对象.

2.2、thymeleaf工具对象

- #dates:java.util.Date对象的实用方法。 

- #calendars:和dates类似, 但是 java.util.Calendar 对象.

- #numbers: 格式化数字对象的实用方法。

- #strings: 字符创对象的实用方法： contains, startsWith, prepending/appending等.

- #objects: 对objects操作的实用方法。

- #bools: 对布尔值求值的实用方法。

- #arrays: 数组的实用方法。

- #lists: list的实用方法。

- #sets: set的实用方法。

- #maps: map的实用方法。

- #aggregates: 对数组或集合创建聚合的实用方法。

- #messages: 在表达式中获取外部信息的实用方法。

- #ids: 处理可能重复的id属性的实用方法 (比如：迭代的结果)。