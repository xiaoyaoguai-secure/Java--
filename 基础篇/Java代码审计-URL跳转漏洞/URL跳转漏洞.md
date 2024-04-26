# URL跳转漏洞

# 一、URL重定向

1. 什么是URL跳转

   很多的web应用因为业务需要，需要与内部的其他服务或第三方服务进行交互，这样就需要重定向的功能，由于当前网页跳转到第三方网页

2. 常见的重定向代码

   ```java
   response.sendRedirect(reques.getParameter("url"));
   ```

   跳转

   ```
   http://localhost:80/index.jsp?url=http://www.xxx.com
   ```

## 1、通过ModelAndView方式

1. 代码如下

   ```java
   @Controller
   public class URL1 {
       @GetMapping("/url1")
       public ModelAndView url1(HttpServletRequest req) {
           String url = req.getParameter("url");
           String redurl ="redirect:"+url;
           return new ModelAndView(redurl);
       }
   }
   ```

   

## 2、通过返回String方式

1. 代码如下

   ```java
   @GetMapping("/url2")
   public String redirect(@RequestParam("url") String url) {
       return "redirect:"+url;
   }
   ```

   

## 3、使用sendRedirect方式

1. 代码如下

   ```java
   @GetMapping("/url3")
   public static void sendRedirect(HttpServletRequest req,HttpServletResponse rsp) throws IOException {
       String url=req.getParameter("url");
       rsp.sendRedirect(url);
   }
   ```

   

## 4、使用RedirectAttributes方式

1. 代码如下

   > 重定向链接地址追加传递的参数

   ```java
   @GetMapping("/url4")
   public String url2(RedirectAttributes redirectAttributes) {
       redirectAttributes.addAttribute("id","2");
       return "redirect:/index.jsp";
   }
   ```

   

## 5、通过Header来进行跳转

1. 代码如下

   `SC_MOVED_TEMPORARILY`：302临时重定向

   `SC_MOVED_PERMANENTLY`：301永久重定向
   
   ```java
   @RequestMapping("/url5")
   public static void setHeader(HttpServletRequest request,HttpServletResponse response){
       String url=request.getParameter("url");
       response.setStatus(HttpServletResponse.SC_MOVED_TEMPORARILY);
       response.setHeader("Location",url);
}
   ```
   
   

# 二、URL跳转漏洞介绍

1. URL跳转漏洞简介

   服务端未对传入的跳转url变量进行检查和控制，并且url变量在攻击者可控的情况下，对url的值进行恶意构造，诱导用户跳转到恶意网站

2. 危害

   一般用于钓鱼攻击，通过跳转到恶意的网址欺骗用户输入用户名和密码来盗窃用户信息，或欺骗用户进行金钱交易，还可以造成xss漏洞

3. 常见产生漏洞的参数名

   ```
   redirect
   redirect_to
   redirect_url
   url
   jump
   jump_to
   target
   to
   link
   linkto
   domain
   ```

4. 漏洞容易产生的地方

   - 用户登录，统一身份认证处，认证完成后会跳转，所以在登陆的时候建议多观察url参数
   
   - 用户分析，收藏内容过后，会跳转
   
- 跨站点认证，授权后，会跳转
   
   - 站内点击其它网址链接时，会跳转
   
   - 在一些用户交互页面也会出现跳转，如请填写对客服评价，评价成功跳转主页，填写问卷，等等业务，注意观察url
   
   - 业务完成后跳转这可以归结为一类跳转，比如修改密码，修改完成后跳转登陆页面，绑定银行卡，绑定成功后返回银行卡充值等页面，或者说给定一个链接办理VIP，但是你需要认证身份才能访问这个业务，这个时候通常会给定一个链接，认证之后跳转到刚刚要办理VIP的页面。
   
     
   

# 三、URL跳转漏洞代码审计

## 1、常见关键字

```
ModelAndView
redirect
sendRedirect
@GetMapping("/url4")
RedirectAttributes
addAttribute
Location
```



## 2、代码审计

就和上边url重定向来进行审计，能进行跳转就存在



# 四、URL跳转漏洞修复

1. 严格把控要跳转的域名，在已知要跳转的路径时，直接写为固定值

   ```java
   @GetMapping("/url3")
   public static void sendRedirect(HttpServletRequest req,HttpServletResponse rsp) throws IOException {
       String url="http://xxx.com"
       rsp.sendRedirect(url);
   }
   ```

   