# JSP了解

1. 什么时Java Servler Pages？

   JSP全称Java Server Pages，是一种动态网页开发技术。它使用JSP标签在HTML网页中插入Java代码。标签通常以<%开头以%>结束。

   JSP是一种Java servlet，主要用于实现Java web应用程序的用户界面部分。网页开发者们通过结合HTML代码、XHTML代码、XML元素以及嵌入JSP操作和命令来编写JSP。

   JSP通过网页表单获取用户输入数据、访问数据库及其他数据源，然后动态地创建网页。

   JSP标签有多种功能，比如访问数据库、记录用户选择信息、访问JavaBeans组件等，还可以在不同的网页中传递控制信息和共享信息。

# 一、环境搭建

1. 下载jdk

2. 配置jdk地环境变量

3. 下载tomcat

4. 配置tomcat的环境变量

5. 测试：在tomcat的目录下的`\webapps\ROOT`下创建jsp文件

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" %>
   <%
   out.print("你好世界");
   %> 
   ```

   

# 二、JSP语法

> 脚本程序可以包含任意Java语句，变量，方法或表达方式

1. 语法格式

   ```jsp
   <% 代码片段 %>
   ```

   或者

   ```jsp
   <jsp:scriptlet>
   	代码片段
   </jsp:scriptlet>
   ```

2. 案例

   > 任何文本，html标签，jsp元素必须写在脚本程序的外面

   ```jsp
   <html>
       <head><title>Hell World</title></head>
       <body>
           Hell Word!<br/>
           <% 
           out.println("Your IP address is"+request.getRemoteAddr());
           %>
       </body>
   </html>
   ```

3. 当中文显示乱码时，需要在头部添加一串jsp代码

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8" pafeEncoding="UTF-8"%>
   ```

   1. 如下当添加了中文，想要正常显示就需要添加如下代码

      ```jsp
      <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
      <html>
          <head><title>Hell World</title></head>
          <body>
              Hell Word!<br/>
              <% 
              out.println("你的ip地址"+request.getRemoteAddr());
              %>
          </body>
      </html>
      ```

      

# 三、JSP声明

1. jsp声明了解

   一个声明语句可以声明一个或多个变量，犯法，供后边的Java代码使用

2. jsp声明的语法格式

   ```jsp
   <% ! declaration; [declaration; ]+ ... %>
   ```

   或者

   ```jsp
   <jsp:declaration>
   	代码片段
   </jsp:declaration>
   ```

3. 程序示例

   ```jsp
   <%! int i = 0; %> 
   <%! int a, b, c; %> 
   <%! Circle a = new Circle(2.0); %> 
   ```

   

# 四、JSP表达式

1. jsp表达式了解

   一个jsp表达式中包含的脚本语言表达式，先被转化成String，然后插入到表达式出现的地方

   表达式元素中可以包含任何符合Java语言规范的表达式，但是不有分号来结束表达式

2. JSP表达式的语法格式

   ```
   <%= 表达式%>
   ```

   或者

   ```
   <jsp:expression>
   	表达式
   </jsp:expression>
   ```

3. 案例

   > 通过new一个Java对象来获取当前的时间

   ```
   <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
   <!DOCTYPE html>
   <html>
   <head>
       <title>Hello Page</title>
   </head>
   <body>
   <h1>Hello, World!</h1>
   
   <p>今天的日期: <%= (new java.util.Date()).toLocaleString() %></p>
   </body>
   </html>
   ```

   

# 五、JSP注释

1. jsp的注释

   | 语法         | 描述                                               |
   | ------------ | -------------------------------------------------- |
   | <%--注释--%> | jsp注释，注释内容不会被发生至浏览器甚至不会被编译  |
   | <!--注释-->  | html注释，通过浏览查看网页源代码时可以看见注释内容 |
   | <\%          | 代表静态<%常量                                     |
   | %/>          | 达标静态%>常量                                     |

   

# 六、JSP指令

1. jsp指令有什么用

   jsp指令用来设置于整个jsp页面相关的属性

2. 语法格式

   ```
   <%@ directive attribute="valye" %>
   ```

3. 常用的三种指令

   | 指令               | 描述                                                     |
   | ------------------ | -------------------------------------------------------- |
   | <%@ page ... %>    | 定义页面的依赖属性，比如脚本 语言，error页面，缓存需求等 |
   | <%@ include ... %> | 包含其他文件                                             |
   | <%@ taglib ... %>  | 引入标签库的定义，可以是自定义标签                       |

## 1、Page指令

> Page指令为容器提供当前页面的使用说明，一个jsp页面可以包含多个page指令

1. Page指令的语法格式

   ```
   <%@ page attribute="value" %>
   ```

   等价的xml格式

   ```
   <jsp:directive.page attribute="value">
   ```

2. 比如使用page指令来设置编码格式

   ```
   <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
   ```
   
   

3. Page指令常用的属性

   | 属性         | 描述                                  | 案例                                   |
   | ------------ | ------------------------------------- | -------------------------------------- |
   | ContentType  | 指定当前jsp页面的MIME类型和字符编码   | contentType="text/html; charset=UTF-8" |
   | language     | 指定jsp页面所用的脚本语言，默认是Java | language="java"                        |
   | pageEncoding | 决定jsp页面的字符编码方法             | pageEncoding="UTF-8"                   |


## 2、Include指令

> jsp可以通过include指令来包含其他文件，被包含的文件可以是jsp文件，html文件或文本文件，并且会被编译执行

1. include指令语法格式

   ```
   <%@ include file="文件相对url路径"%>
   ```

   等价的xml语法

   ```
   <jsp:directive.include file="文件相对url地址">
   ```

   

## 3、Taglib指令

了解，看不懂



# 七、JSP动作元素

> 与jsp指令元素不同的是，jsp动作元素在请求处理阶段起作用，jsp动作元素是用xml语法写的
>
> 利用jsp动作可以动态地插入文件，重用JavaBean组件，把用户重定向到另外地页面，为Java插件生成html代码

1. 语法格式

   ```
   <jsp:action_name attribute="value" />
   ```

2. 可用地标注动作元素

   > 动作元素基本上都是预定地函数，jsp规范定义了一系列地标注动作，它用jsp作为前缀

   | 语法            | 描述                           |
   | --------------- | ------------------------------ |
   | jsp:include     | 在页面被请求地时候引入一个文件 |
   | jsp:useBean     | 寻找或者实例化一个JavaBean     |
   | jsp:setProperty | 设置JavaBean地属性             |
   | jsp:getProperty | 输出某个JavaBean地属性         |
   | jsp:forward     | 把请求转到一个新地页面         |

## 1、常见的属性

> 所有的动作要素都有两个属性：id属性和scope属性

1. id属性

   id属性是动作元素的唯一标识，可以在jsp页面中引用，动作元素创建的id值可以通过PageContext来调

2. scope属性

   该属性用于识别动作元素的生命周期。 id属性和scope属性有直接关系，scope属性定义了相关联id对象的寿命。 scope属性有四个可能的值： (a) page, (b)request, (c)session, 和 (d) application



## 2、`<jsp:include>动作元素`

> <jsp:include >动作元素用来包含静态和动态的文件，该动作把指定文件插入正在生成的页面

1. 语法格式

   ```
   <jsp:include page="相对 URL" flush="true" />
   ```

2. 案例

   1. 定义了两个文件date.jsp和main.jsp

   2. date.jsp

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>Title</title>
      </head>
      <body>
          今天的日期是：<%= (new java.util.Date()).toLocaleString() %>
      </body>
      </html>
      ```

   3. main.jsp

      ```jsp
      <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
      <!DOCTYPE html>
      <html>
      <head>
          <title>Hello Page</title>
      </head>
      <body>
      <h1>Hello, World!</h1>
      
      <p>
          <jsp:include page="date.jsp" flush="true" />
      </p>
      </body>
      </html>
      ```



## 3、`<jsp:useBean>`动作元素

> jsp:useBean动作用来加载一个将在jsp页面中使用的JavaBean

1. 语法格式

   > 在类载入后，即可以通过`jsp:setProperty`和`jsp:getPropery`动作来修改和检索bean的属性

   ```jsp
   <jsp:useBean id="name" class="package.class" />
   ```

2. useBean动作相关的属性列表

   | 属性     | 描述                                                 |
   | -------- | ---------------------------------------------------- |
   | class    | 指定Bean的完整包名                                   |
   | type     | 指定将引用该对象变量的类型                           |
   | beanName | 通过java.bean.Beans的instantiate()方法指定Bean的名字 |
   | id       | 给获取到的对象设置一个名称                           |



## 4、`<jsp:setProperty>动作元素`

> jsp:setProperty用来设置已经实例化的Bean对象的属性，有两种用法：可以放在元素外面，元素后面

1. 放在元素外部

   > 此时，不管jsp:useBean是找到了一个现有的Bean，还是新创建了一个Bean实例，jsp:setPropert都会执行

   ```jsp
   <jsp:useBean id="myName" ... />
   ...
   <jsp:setProperty name="myName" property="someProperty" .../>
   ```

2. 放在元素内部

   > 此时，jsp:setProperty只有在新建Bean实例时才会执行，如果是使用现有实例则不执行jsp:setProperty。

   ```jsp
   <jsp:useBean id="myName" ...>
   ...
   	<jsp:setProperty name="myName" property="someProperty" .../>
   </jsp:useBean>
   ```

3. 常用属性

   | 属性     | 描述                                                         |
   | -------- | ------------------------------------------------------------ |
   | name     | name属性是必需的。它表示要设置属性的是哪个Bean               |
   | property | property属性是必需的。它表示要设置哪个属性。有一个特殊用法：如果property的值是"*"，表示所有名字和Bean属性名字匹配的请求参数都将被传递给相应的属性set方法 |
   | value    | value 属性是可选的。该属性用来指定Bean属性的值。字符串数据会在目标类中通过标准的valueOf方法自动转换成数字、boolean、Boolean、 byte、Byte、char、 |

   

## 5、`<jsp:getProperty>动作元素`

> jsp:getProperty动作提取指定Bean属性的值，转换成字符串，然后输出

1. 语法格式

   ```jsp
   <jsp:useBean id="myName" ... />
   ...
   <jsp:getProperty name="myName" property="someProperty" .../>
   ```

2. 相关属性

   | 属性     | 描述                                 |
   | -------- | ------------------------------------ |
   | name     | 要检索的Bean属性名称，Bean必须已定义 |
   | property | 表示要提取Bean属性值                 |

3. 实例

   1. 创建一个类，实例化`message`参数

      ```java
      public class main {
          private String message = "大大怪";
          public String getMessage() {
              return (message);
          }
          public  void setMessage(String message) {
              this.message = message;
          }
      }
      ```

   2. 创建jsp文件

      > 1. 通过`jsp:setProperty`来获取到对象
      > 2. 获取到对象后，使用`jsp:setProperty`中的naem属性来获取到对象，property属性来获取对象中的属性，value给选中的属性进行赋值
      > 3. `jsp:getProperty`：提取指定Bean属性的值，转换成字符串，然后输出

      ```jsp
      <%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
      <!DOCTYPE html>
      <html>
      <head>
          <title>Hello Page</title>
      </head>
      <body>
      <h1>Hello, World!</h1>
      
      <jsp:useBean id="test" class="com.wy.test.main" />
      <jsp:setProperty name="test" property="message" value="大大怪" />
      <jsp:getProperty name="test" property="message"/>
      </body>
      </html>
      ```

      

## 6、`<jsp:forward>动作元素`

> 把请求转到另外的页面

1. 语法格式

   ```
   <jsp:forward page="相对URL地址" />
   ```

2. 相关的属性

   | 属性 | 描述                                                         |
   | ---- | ------------------------------------------------------------ |
   | page | page属性包含的是一个相对URL，page的值既可以直接给出，也可以在请求的时候动态计算，可以是一个jsp页面或者一个Java Servlet |

   

# 八、JSP隐含对象

> 它们是在jsp页面中自动可用对象，无需额外的声明或初始化

1. 九个隐含对象的简介如下

   | 对象        | 描述                                                         |
   | ----------- | ------------------------------------------------------------ |
   | request     | HttpServletRequest类的实例，代表http请求的对象，包含客户端发送到服务器的信息，如表单数据，url参数等 |
   | response    | httpServletResponse类的实例，代表http响应对象，用于向客户端发送数据和响应 |
   | out         | JsWriter类的实例，用于向客户端输出文本内容的对象，通常是用生成HTML |
   | session     | HTTP Session类的实例，代表用户会话的对象，可用于存储和检索用户特定数据，跨多个页面 |
   | application | ServletContext类的实例，代表web应用程序的上下文，可以用于存储和检索全局应用程序数据 |
   | config      | ServletConfig类的实例，包含有关当前js页面的配置细腻些，列如初始化参数 |
   | pageContext | PageContext类的实例，提供对jsp页面所有对象以及命令空间的访问 |
   | page        | 类似于Java类中的this关键字，代表当前jsp页面的实例，可以用于调用页面的方法 |
   | execption   | exception类对象，代表发送错误的jsp页面中对应的异常对象，用于处理页面中的异常情况，可用于捕获和处理页面中发送的异常 |




## 1、request对象

1. request对象

   request对象是javax.servlet.http.HttpServletRequest 类的实例。每当客户端请求一个JSP页面时，JSP引擎就会制造一个新的request对象来代表这个请求。

   request对象提供了一系列方法来获取HTTP头信息，cookies，HTTP方法等等



## 2、out对象

1. our对象

   out对象是 javax.servlet.jsp.JspWriter 类的实例，用来在response对象中写入内容

2. 常用方法

   | 方法                      | 描述                     |
   | ------------------------- | ------------------------ |
   | out.print(data Type dt)   | 输出Type类型的值         |
   | out.println(data type dt) | 输出Type类型的值然后换行 |
   | out.flush()               | 刷新输出流               |

   

## 3、session对象

1. session对象

   session对象是 javax.servlet.http.HttpSession 类的实例。和Java Servlets中的session对象有一样的行为

   session对象用来跟踪在各个客户端请求间的会话



## 4、application对象

1. application对象

   application对象直接包装了servlet的ServletContext类的对象，是javax.servlet.ServletContext 类的实例。

   这个对象在JSP页面的整个生命周期中都代表着这个JSP页面。这个对象在JSP页面初始化时被创建，随着jspDestroy()方法的调用而被移除。

   通过向application中添加属性，则所有组成您web应用的JSP文件都能访问到这些属性。



# 九、JSP客户端请求

> 当浏览器请求一个网页时，它会向网络服务器发送一系列不能被直接读取的信息，因为这些信息是作为HTTP信息头的一部分来传送的。您可以查阅HTTP协议来获得更多的信息

1. 常见的浏览器端信息头

   | 信息           | 描述                                                         |
   | -------------- | ------------------------------------------------------------ |
   | Accept         | 指定浏览器或其他客户端可以处理的MIME类型。它的值通常为 **image/png** 或 **image/jpeg** |
   | Content-Length | 仅适用于POST请求，表示 POST 数据的字节数                     |
   | Cookie         | 返回先前发送给浏览器的cookies至服务器                        |
   | Host           | 指出原始URL中的主机名和端口号                                |
   | Referer        | 标志着所引用页面的URL。比如，如果你在页面1，然后点了个链接至页面2，那么页面1的URL就会包含在浏览器请求页面2的信息头中 |
   | User-Agent     | 用来区分不同浏览器或客户端发送的请求，并对不同类型的浏览器返回不同的内容 |

   

## 1、HttpServletRequest类

> 处理请求的一个类

1. 常用方法

   | 方法                                    | 描述                                                 |
   | --------------------------------------- | ---------------------------------------------------- |
   | Cookie[] getCookies()                   | 返回客户端所有的Cookie的数组                         |
   | Enumeration getAttributeNames()         | 返回request对象的所有属性名称的集合                  |
   | **Object getAttribute(String name)**    | 返回名称为name的属性值，如果不存在则返回null         |
   | Enumeration getHeaderNames()            | 返回所有HTTP头的名称集合                             |
   | **Enumeration getParameterNames()**     | 返回请求中所有参数的集合                             |
   | **HttpSession getSession()**            | 返回request对应的session对象，如果没有，则创建一个   |
   | **ServletInputStream getInputStream()** | 返回请求的输入流                                     |
   | **String getRemoteAddr()**              | 返回客户端的IP地址                                   |
   | **String getRemoteUser()**              | 返回客户端通过登录认证的用户，若用户未认证则返回null |
   | **int getIntHeader(String name)**       | 返回指定名称的request信息头的值                      |
   | **int getServerPort()**                 | 返回服务器端口号                                     |

2. 如下

   > - getHeaderNames()方法来读取HTTP信息头,这个方法以枚举的形式返回当前HTTP请求的头信息
   > - 用hasMoreElements()方法来确定什么时候停止
   > - 用nextElement()方法来获得每个参数的名字

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8"
            pageEncoding="UTF-8"%>
   <%@ page import="java.io.*,java.util.*" %>
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8">
       <title>菜鸟教程(runoob.com)</title>
   </head>
   <body>
   <h2>HTTP 头部请求实例</h2>
   <table width="100%" border="1" align="center">
       <tr bgcolor="#949494">
           <th>Header Name</th><th>Header Value(s)</th>
       </tr>
       <%
           Enumeration headerNames = request.getHeaderNames();
           while(headerNames.hasMoreElements()) {
               String paramName = (String)headerNames.nextElement();
               out.print("<tr><td>" + paramName + "</td>\n");
               String paramValue = request.getHeader(paramName);
               out.println("<td> " + paramValue + "</td></tr>\n");
           }
       %>
   </table>
   </body>
   </html>
   ```

   

# 十、JSP服务器响应

> Response响应对象主要将JSP容器处理后的结果传回到客户端。可以通过response变量设置HTTP的状态和向客户端发送数据，如Cookie、HTTP文件头信息等

1. 常见的响应头

   | 响应头       | 描述                                                         |
   | ------------ | ------------------------------------------------------------ |
   | Allow        | 指定服务器支持的request方法（GET，POST等等）                 |
   | Connection   | 命令浏览器是否要使用持久的HTTP连接。**close****值** 命令浏览器不使用持久HTTP连接，而keep-alive 意味着使用持久化连接 |
   | Content-Type | 表明文档使用的MIME类型                                       |
   | Refresh      | 指明浏览器每隔多久请求更新一次页面                           |
   | Retry-After  | 与503 (Service Unavailable)一起使用来告诉用户多久后请求将会得到响应 |
   | Set-Cookie   | 指明当前页面对应的cookie                                     |

   

# 十一、HTTP状态码

1. 常见的状态码

   | 状态码 | 消息                | 描述                                                         |
   | ------ | ------------------- | ------------------------------------------------------------ |
   | 100    | Continue            | 只有一部分被服务器接收，但只要没被服务器拒绝，客户端就会延续这个请求 |
   | 101    | Switching Protocols | 服务器交换机协议                                             |
   | 200    | ok                  | 请求被确认                                                   |
   | 201    | Created             | 请求时完整的，新的资源被创建                                 |
   | 202    | Accepted            | 请求被接收，但未处理完                                       |
   | 300    | Multiple Choices    | 一个超链接表，用户可以选择一个超链接并访问，最大支持5个超链接 |
   | 301    | Moved Permanently   | 被请求的页面已经移动到新的URL下                              |
   | 302    | Found               | 被请求的页面暂时性地移动到了新地URL下                        |
   | 303    | See Other           | 被请求地页面可以在一个不同地URL下找到                        |
   | 400    |                     | 服务器无法识别请求                                           |
   | 401    |                     | 被请求地页面需要用户名和密码                                 |
   | 403    |                     | 禁止访问所请求的页面                                         |
   | 404    |                     | 服务器无法找到所请求的页面                                   |
   | 405    |                     | 请求中所指的方法不被允许                                     |
   | 406    |                     | 服务器只能创建一个客户端无法发接收的响应                     |
   | 407    |                     | 在请求被服务前必须认证一个代理服务器                         |
   | 408    |                     | 请求时间超过了服务器所能等待的时间，连接被断开               |
   | 409    |                     | 请求有矛盾的地方                                             |
   | 410    |                     | 被请求的页面不再可用                                         |
   | 411    |                     | Content0Length没有被定义，服务器拒绝接受请求                 |
   | 412    |                     | 请求的前提条件被服务器评估为false                            |
   | 413    |                     | 因为请求的实体太大，服务器拒绝接受请求                       |
   | 414    |                     | 服务器拒绝接受请求，因为URL太长，多出现再POST请求转换为GET请求时说附带的大量查询信息 |
   | 415    |                     | 服务器拒绝接受请求，因为媒体类型不被支持                     |
   | 500    |                     | 请求不完整，服务器遇见了出乎意料的状况                       |
   | 501    |                     | 请求不完整，服务器不能提供所需要的功能                       |
   | 502    |                     | 请求不完整，服务器从上游服务器接受了一个无效的响应           |
   | 503    |                     | 请求不完整，服务器暂时重启或关闭                             |
   | 504    |                     | 网关超时                                                     |
   | 505    |                     | 服务器不支持所指定的HTTP版本                                 |

2. http状态码程序示例

   ```jsp
   <html>
   <head>
   <title>Setting HTTP Status Code</title>
   </head>
   <body>
   <%
      // 设置错误代码，并说明原因
      response.sendError(407, "Need authentication!!!" );
   %>
   </body>
   </html>
   ```

3. 常用方法

   | 方法                                                | 描述                                                         |
   | --------------------------------------------------- | ------------------------------------------------------------ |
   | **public void setStatus ( int statusCode )**        | 此方法可以设置任意的状态码。如果您的响应包含一个特殊的状态码和一个文档，请确保在用PrintWriter返回任何内容前调用setStatus方法 |
   | **public void sendRedirect(String url)**            | 此方法产生302响应，同时产生一个 *Location* 头告诉URL 一个新的文档 |
   | **public void sendError(int code, String message)** | 此方法将一个状态码(通常为 404)和一个短消息，自动插入HTML文档中并发回给客户端 |

   

# 十二、JSP表单处理

> 我们再浏览网页的时候，经常需要向服务器提交信息，并让后台程序处理，浏览器中使用GET和POST方法向服务器提交数据

## 1、JSP读取表单数据

- **getParameter()**：来获取表单参数的值
- **getParameterValues()**：接受数组变量，比如名字相同，但是值有多个
- **geParameterNames()**：该方法可以取得所有变量的名称
- **getInputStream():**调用此方法来读取来自客户端的二进制数据流



## 2、使用表单的GET方法示例

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
         pageEncoding="UTF-8"%>
<%@ page import="java.io.*,java.util.*" %>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>大大怪</title>
</head>
<body>
<h1>使用 GET 方法读取数据</h1>
<ul>
    <p>
        name值<br/>
        <%= request.getParameter("name")%>

    </p>
    <p>
        url<br/>
        <%= request.getParameter("url")%>
    </p>
</ul>
</body>
</html>
```



# 十三、JSP标注标签库

[JSP 标准标签库（JSTL） | 菜鸟教程 (runoob.com)](https://www.runoob.com/jsp/jsp-jstl.html)

[ 标签 | 菜鸟教程 (runoob.com)](https://www.runoob.com/jsp/jstl-core-out-tag.html)

1. 概念

   JSTL：Java Server pages Tag Library，JSP标注标签库

2. 使用步骤

   1. 下载地址

      jstl相关jar包下载地址：http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/

   2. 导入jstl相关jar包

   3. 在JSP页面中引入标签库：taglib指令： `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`

3. 常用标签

   | 标签            | 描述                                                         |
   | --------------- | ------------------------------------------------------------ |
   | `<c:out>`       | 用于在jsp中显示数据，就像<%= ...>                            |
   | `<c:set>`       | 用于保存数据                                                 |
   | `<c:remove>`    | 用于删除数据                                                 |
   | `<c:if>`        | 与if语句一样                                                 |
   | `<c:choose>`    | 本身只当作`<c:when>`和`<c:otherwise>`的父标签                |
   | `<c:when>`      | `<c:choose>`的子标签，判断条件是否成立                       |
   | `<c:otherwise>` | `<c:choose>`的子标签，接在`<c:when>`标签后，当`<c:when>`标签判断为false时会被执行 |
   | `<c:import>`    | 检索一个绝对或相对的url，然后将其暴露给页面                  |
   | `<c:forEach>`   | 基础迭代标签，接受多种集合类型                               |
   | `<c:param>`     | 用来给包含或重定向的页面传递参数                             |
   | `<c:redirect>`  | 重定向至一个新的URL                                          |
   | `<c:url>`       | 使用可选的查询参数来创造一个URL                              |



1. `<c:out>标签`

   > 用来显示一个表达式的结果，与<%= %>作用相似

   ```
   <c:out value="${user.getUSername()}">
   ```

