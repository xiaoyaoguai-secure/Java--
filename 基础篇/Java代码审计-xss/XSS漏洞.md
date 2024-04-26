# XSS漏洞

# 一、xss漏洞的了解

1. 什么是xss漏洞

   xss的中文名称为跨站脚本攻击，它允许用户将恶意脚本注入到web页面中，其他用户访问此页面时植入的恶意脚本就会在其他用户的客户端中执行

2. xss漏洞的原理

   后台未对用户输入进行检查或过滤，直接把用户输入返回至前端。导致javascript代码在客户端任意执行

3. xss漏洞的危害

   可以获取客户端用户的信息，比如用户登录的cookie信息

   可以进行蠕虫传播

   可以在客户端中植入木马

   主要危害：窃取cookie，键盘记录，截屏，网页挂马，命令执行



# 二、XSS常见触发位置

> xss漏洞产生必然会有相关的输入/输出，因此我们只需快速找到这些输入/输出点，即可快速地进行跟踪发现漏洞

1. Java中输入常使用

   - ${param}
   - <%= request.getParameter("name")%>
   - <% out.println("value");%>

   


# 三、JSP表达式

1. 打印语法

   ```jsp
   <% String mag = request.getParameter("name");%>
   <% out.println(mag);%>
   ```

2. 打印语法简写

   ```jsp
   <% String mag = request.getParameter("name");%>
   <%= mag%>
   ```

   

# 四、EL表达式语言

## 1、EL表达式的简介

1. EL的介绍

   EL（Expression Language） 是为了使JSP写起来更加简单。表达式语言的灵感来自于 ECMAScript 和 XPath 表达式语言，它提供了在 JSP 中简化表达式的方法，让Jsp的代码更加简化。

   El表达式提供了获取对象以及属性的简单方法。



## 2、对比JSP写法和EL表达式写法

1. JSP写法

   ```jsp
   <%= request.getParameter("name")%>
   ```

2. EL表达式写法

   EL表达式写在JSP的HTML代码块中，而不能写在<%%>之间

   ```jsp
   ${param.name}
   ```



## 3、EL表达式的基本了解

1. 语法格式

   > 用美元符号$定界，内容包括在华括弧{}中

   ```
   ${表达式}
   ```

2. 表达式兼容性设置

   - 解决EL表达式无法使用的问题

     在默认情况下，Servlet2.3/jsp 1.2是不支持EL表达式的，而Servlet2.4/jsp 2.0支持

     1. 第一种：修改web.xml文件

        ```
        <web-app version="2.4"  
        xmlns="http://java.sun.com/xml/ns/javaee"  
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
        xsi:schemaLocation="http://java.sun.com/xml/ns/javaee  
        http://java.sun.com/xml/ns/javaee/web-app_2_4.xsd"> 
        ```

     2. 第二种：设置某个jsp页面使用el表达式：需要在jsp页面加上

        ```
        <%@ page isELIgnored="false"%>  
        ```

     3. 第三种：web.xml文件中加上下列代码，每个jsp页面都可以使用

        ```
        <jsp-config>  
          <jsp-property-group>  
            <url-pattern>*.jsp</url-pattern>  
            <el-ignored>false</el-ignored>  
          </jsp-property-group>  
        </jsp-config> 
        ```



## 4、EL的隐式对象

1. 常用对象

   | 隐含对象        | 描述                              |
   | --------------- | --------------------------------- |
   | pageScope       | page作用域                        |
   | requestScope    | request作用域                     |
   | sessionScope    | session作用域                     |
   | **param**       | **request对象的参数，字符串**     |
   | **paramValues** | **Request对象的参数，字符串集合** |
   | header          | HTTP信息头，字符串                |

2. 输入相关的隐含对象

   1. 获取用户的请求参数
      1. param

         ```
         request.getParamster(String name)
         ```

      2. paramValues

         ```
         request.getParamsterValues(String name)
         ```

   2. 取得数据

      1. param

         ```
         ${param.name}
         ```

         

      2. paramValues

         ```
         ${paramValues.name}
         ```



## 5、request对象

> 基于xss漏洞来学习，大多数都是基于客户端请求

1. 常用方法

   | 类型       | 方法                          | 描述                                                         |
   | ---------- | ----------------------------- | ------------------------------------------------------------ |
   | **String** | **getParameter(String name)** | **根据参数名称获取参数值**                                   |
   | String     | getServletPath()              | 获取用户请求的JSP页面文件的名字，比如带目录符号\，列如：\hellp.jsp |
   | String     | getRequestURL()               | 获取请求URL                                                  |
   | String     | getRemoteAddr()               | 获取客户机的ip地址                                           |



# 五、数据处理类



## 1、ModelAndView类

> ModelAndView类，用来存储处理完成后的结果数据，以及显示该数据的视图，其前端JSP页面可以使用`${参数}`的方法来获取值

### 1.1、方法

| 类型         | 方法                          | 功能                                                |
| ------------ | ----------------------------- | --------------------------------------------------- |
| ModelAndView | ModelAndView()                | 默认构造函数，填充bean的属性                        |
| ModelAndView | addAllObjects(Map modelMap)   | 添加包含在所提供的地图模型中的所有条目              |
| ModelAndView | addObject(Object modelObject) | 添加对象使用的参数名称生成模型，向request请求作用域 |
|              | setViewName()                 | 设置展示数据的页面元素的名称                        |



### 1.2、用法

1. 重定向页面

   ```java
   @RequestMapping("/a")
   public ModelAndView getPageMV() {
       ModelAndView mv = new ModelAndView("redirect:/aaa.jsp");
       return mv;
   }
   ```

2. 获取参数到前端界面

   1. 前端界面：aaa.jsp

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
      <head>
          <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
          <meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7">
          <meta name="viewport" content="width=device-width, initial-scale=0.0, user-scalable=no, minimum-scale=1.0, maximum-scale=1.0">
          <meta content="yes" name="apple-mobile-web-app-capable">
          <meta content="black" name="apple-mobile-web-app-status-bar-style">
          <meta content="telephone=no" name="format-detection">
          <title>静态页</title>
      
      <body>
      <div class="divContent">
          <%@ page isELIgnored="false"%>    
          ${cs}
      </div>
      </body>
      </html>
      ```

   2. 后端功能实现

      ```java
      @Controller
      @RequestMapping("/a")
      public class http {
          //第一种写法
          @RequestMapping(value = "/aaa", method = RequestMethod.GET)
          public ModelAndView getCeishi(@RequestParam("a") String a) {
              ModelAndView modelAndView=new ModelAndView();
              //添加使用的参数名称生成模型
              modelAndView.addObject("cs",a);
      
              //传入需要添加的的静态页面，需要指定具体文件
              modelAndView.setViewName("/aaa.jsp");
              return modelAndView;
          }
          
          //第二种简写法
          @RequestMapping("/bbb")
          public ModelAndView bbb (@RequestParam String a) {
              return new ModelAndView("/aaa.jsp","cs",a);
          }
      }    
      ```

   

3. 利用ModelAndView来进行登录后的页面显示

   1. 前端界面：aaa.jsp

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
      <head>
          <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
          <meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7">
          <meta name="viewport" content="width=device-width, initial-scale=0.0, user-scalable=no, minimum-scale=1.0, maximum-scale=1.0">
          <meta content="yes" name="apple-mobile-web-app-capable">
          <meta content="black" name="apple-mobile-web-app-status-bar-style">
          <meta content="telephone=no" name="format-detection">
          <title>静态页</title>
      
      <body>
      <div class="divContent">
          <%@ page isELIgnored="false"%>
          <p>登录成功:${cs}</p>
      </div>
      </body>
      </html>
      ```

   2. 后端功能实现

      ```java
      @Controller
      @RequestMapping("/login")
      public class http {
          @RequestMapping("/{name}/user")
          public ModelAndView mo7(@PathVariable String name) {
              if (!"admin".equals((name))) {
                  return new ModelAndView("redirect:/index.jsp");
              }
              return new ModelAndView("/aaa.jsp","cs",name);
          }
      }
      ```

      

## 2、ModelMap类

> 主要用于传递控制方法处理数据到结果页面

### 2.1、方法

| 方法                                  | 描述     |
| ------------------------------------- | -------- |
| addAttribute(String key,object value) | 参数传递 |
| put(String key,object value)          | 参数传递 |



### 2.2、用法

1. 将数据显示到前端界面

   1. 前端界面

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>Title</title>
      </head>
      <body>
      <%@ page isELIgnored="false"%>
      <h1>Welcome ${name}!</h1>
      
      </body>
      </html>
      ```

   2. 后端功能实现

      ```java
      @Controller
      @RequestMapping("/a")
      class ModeImpaImp {
          @RequestMapping("/mode")
          public String welcome(ModelMap modelMap,@RequestParam("user") String user) {
              modelMap.put("name", user);
              return "/bbb.jsp"; // 对应上面的JSP文件名
          }
      }
      ```

      

## 3、Model类

> 主要负责数据表示、业务逻辑、数据持久化和数据传输
>
> 一般来说，可以用model来接收各种类型的数据，如果使用来接收一组数据List>>那么这个时候的model实际上是ModelMap。

### 3.1、方法

| 方法           | 描述                              |
| -------------- | --------------------------------- |
| addAttribute() | 将数据共享至请求作用域(request域) |

### 3.2、用法

1. 将用户输入的数据打印到前端

   1. 前端界面设计

      ```jsp
      <body>
          <%@ page isELIgnored="false"%>
          ${requestScope.usernam}
      </div>
      </body>
      ```

   2. 后端功能实现

      ```java
      @Controller
      @RequestMapping("/a")
      class Model1 {
      	@RequestMapping("/nameuser")
          public String testModel(Model model) {
              model.addAttribute("usernam","admin");
              return "/p.jsp";
          }
      }
      ```



## 4、Map类

### 4.1、方法

| 方法                         | 描述     |
| ---------------------------- | -------- |
| put(String key,object value) | 参数传递 |

### 4.2、用法

1. 将用户输入的数据打印到前端

   1. 前端设计

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <html>
      <head>
          <title>Title</title>
      </head>
      <body>
      <%@ page isELIgnored="false"%>
      ${requestScope.map}
      </body>
      </html>
      ```

   2. 后端功能实现

      ```java
      @Controller
      @RequestMapping("/a")
      class Map1{    
      	@RequestMapping(value = "/testMap")
          public String testMap(Map<String,Object> map,@RequestParam("adm") String adm){
              map.put("map",adm);
              System.out.println("map:"+map);
              System.out.println(map.getClass().getName());
              return "/ccc.jsp";
          }
      }
      ```

      

# 六、XSS漏洞

> 从上边的代码可以看到，产生xss的主要原因是因为没有对用户的输入进行过滤后直接输出

## 1、反射XSS

###  1.1、前端反射xss

1. 前端代码

   > 没有做任何拦截，用户可输入，并且会显示在前端

   ```jsp
   <%@ page language="java" contentType="text/html; charset=UTF-8"
            pageEncoding="UTF-8"%>
   <%@ page isELIgnored="false"%>
   
   <html>
   <head>
       <title>前端xss</title>
   </head>
   <body>
   <%
       String name = request.getParameter("name");
       out.println(name);
   %>
   </body>
   </html>
   ```

   payload语句构造

   ```
   ?name=<script>alert(1)</script>
   ```



### 1.2、后端反射xss

> 后端功能返回响应时未作任何过滤导致xss

1. 后端代码实现

   ```java
   @Controller
   @RequestMapping("xss")
   public class xss_1 {
       @RequestMapping("xss1")
       public void Xss1(HttpServletRequest rqs, HttpServletResponse rps)  {
           try {
               String xss1 = rqs.getParameter("xss1");
               rps.getWriter().print(xss1);
           } catch (IOException e) {
               e. printStackTrace();
           }
   
       }
   }
   ```

   payload构造

   ```
   /xss/xss1?xss1=<script>alert(1)</script>>
   ```



## 2、存储型XSS

> 原理都一样，区别在于存储型xss会把payload存储在服务器，每一次方法内容就会触发payload，反射则需要需要诱导受害者点击，才会造成伤害

1. 常见位置

   一般xss会在数据库读取数据时，将数据库中的payload语句渲染到html导致xss产生

   一般存在**留言板，文章，个性签名等地方**

2. 存储挖掘和审计思路

   通过输入点和输出点结合去查看后端代码是否有过滤
   
   https://blog.csdn.net/Mart1nn/article/details/81221349

3. 代码分析

   1. 前端代码

      > 接收后端传递的参数

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      <%@ page isELIgnored="false"%>
      <html>
      <head>
          <title>xss_存储</title>
      </head>
      <body>
      <h1>${user}</h1>
      <h1>${comment}</h1>
      </body>
      </html>
      ```

   2. 后端代码

      > 先进行数据的插入，随后在进行数据的获取，就一个简单的留言功能

      ```java
      @Controller
      public class xss {
          @RequestMapping("/xss_com")
          public Object xss_INSERT(@RequestParam("user") String user,@RequestParam("comment") String comment) throws ClassNotFoundException, SQLException {
              Class.forName("com.mysql.jdbc.Driver");
              Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/xss?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC","root","root");
              Statement st = conn.createStatement();
              st.executeUpdate("INSERT INTO comments (user, comment) values ('" + user + "', '" + comment +"')");
              conn.close();
              return "/index.jsp";
          }
      
          @RequestMapping("/xss_select")
          public String xss_select(Model model) throws ClassNotFoundException, SQLException {
              Class.forName("com.mysql.jdbc.Driver");
              Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/xss?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC","root","root");
              Statement st = conn.createStatement();
              String sql = "SELECT * FROM comments";
              System.out.println("sql = " + sql);
              ResultSet rs = st.executeQuery(sql);
              while (rs.next()) {
                  model.addAttribute("user",rs.getString("user"));
                  System.out.println(rs.getString("user"));
                  model.addAttribute("comment",rs.getString("comment"));
                  System.out.println(rs.getString("comment"));
              }
              return "/xss1.jsp";
          }
      }
      ```




# 七、xss漏洞修复

## 1、过滤器

> 设置全局过滤器

## 2、HTML实体编码

> 把一些预定的字符转换为HTML实体编码

1. 如下代码

   > 把特殊的字符转换HTML实体编码

   ```java
   public static String htmlSpecialChars(String content) {
     if (content == null) {
       return null;
     }
   
     char[]        charArray = content.toCharArray();
     StringBuilder sb        = new StringBuilder();
   
     for (char c : charArray) {
       switch (c) {
         case '&':
           sb.append("&amp;");
           break;
         case '"':
           sb.append("&quot;");
           break;
         case '\'':
           sb.append("&#039;");
           break;
         case '<':
           sb.append("&lt;");
           break;
         case '>':
           sb.append("&gt;");
           break;
         default:
           sb.append(c);
           break;
       }
     }
   
     return sb.toString();
   }
   ```

   



# 总结

## 一、反射审计思路

1. 找到传参点
2. 查看后端参数传递时会不会打印在前端，在看传递时是否会拦截过滤

## 二、存储审计思路

1. 黑加白，对照者功能点来进行审计
2. 直接基于关键字看源码审计



## 三、个人审计思路

功能和代码双结合审计，简称白加黑

1. 当不知到程序的具体功能时，先对该程序做一个简单的渗透探测
2. 在基于关键或者功能点查找后端代码
3. 找到关键点出现的地方后
4. 查看该功能对应的前端路径，试着去访问一下
5. 在回到后端进行代码审计，前后端结合审计



## 四、总结

### 1、输入点

1. 通过前后端结合找到输入点，查看输入点是否会把前端输入的数据传入到后端数据库中，并且要找到在哪里开始输入内容

### 2、输出点

1. 也是一样的前后端结合，查看后端数据库查询，是否会把插入的内容在前端页面显示，在哪里显示



# 关键字

1. 关键字

   ```
   <%=
   ${
   <c:out
   <c:if
   <c:forEach
   ModelAndView
   ModelMap
   Model
   Map
   request.getParameter
   request.setAttribute
   response.getWriter().print()
   response.getWriter().writer()
   ```






https://gitee.com/root8088/jeesns1.3?_from=gitee_search







https://cloud.tencent.com/developer/article/1938463

https://www.freebuf.com/vuls/384354.html

https://blog.csdn.net/shelter1234567/article/details/134001322



[Java审计之XSS篇 - 先知社区 (aliyun.com)](https://xz.aliyun.com/t/8294?time__1311=n4%2BxuDgDBDyBDQDOiD%2FzW4wexc0WCKD97DiqhD&alichlgref=https%3A%2F%2Fxz.aliyun.com%2Ft%2F8294#toc-0)

[Java 审计之xss审计要点 - nice_0e3 - 博客园 (cnblogs.com)](https://www.cnblogs.com/nice0e3/p/13709440.html)



[Java审计之XSS篇 - 先知社区 (aliyun.com)](https://xz.aliyun.com/t/8294?time__1311=n4%2BxuDgDBDyBDQDOiD%2FzW4wear40K4m5Kqx&alichlgref=https%3A%2F%2Fxz.aliyun.com%2Ft%2F8294#toc-8)

https://cloud.tencent.com/developer/article/1938463



https://xz.aliyun.com/t/8294?time__1311=n4%2BxuDgDBDyBDQDOiD%2FzW4wex9Dfro%2BxhYWD&alichlgref=https%3A%2F%2Fwww.baidu.com%2Flink%3Furl%3Dloq2kuYPzG-f-1oSVueUKm8GtRwEr0BYKV0ubW1XVCjBDyyF9f3vItawxrAuNTNw%26wd%3D%26eqid%3Db01af6810009f5a10000000266151539









https://www.cnblogs.com/N0r4h/p/15859970.html



https://www.freebuf.com/vuls/384354.html







https://blog.csdn.net/shelter1234567/article/details/134001322



https://blog.csdn.net/Gherbirthday0916/article/details/135031210



https://ost.51cto.com/posts/491



靶场环境

[GitHub - cn-panda/JavaCodeAudit: Getting started with java code auditing 代码审计入门的小项目](https://github.com/cn-panda/JavaCodeAudit)

[Java代码审计-XSS审计 - FreeBuf网络安全行业门户](https://www.freebuf.com/articles/web/367805.html)

[Java代码审计 —XSS跨站脚本 - FreeBuf网络安全行业门户](https://www.freebuf.com/articles/web/319333.html)

[【Java 代码审计入门-03】XSS 漏洞原理与实际案例介绍-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/2204381?areaId=106001)

