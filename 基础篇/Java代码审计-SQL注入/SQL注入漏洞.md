# SQL注入漏洞

1. 造成sql注入一般需要满足以下两个条件

   - 用户输入可控
   - 直接或间接拼入sql语句执行

2. 对于sql注入漏洞审计最常见的方法

   关键字查找比如：select，update等sql语句

提示：Java语言本身是一种强类型语言，因此寻找sql注入漏洞的过程中，可以首先找到所有包含sql语句的点，随后观察传参类型是否是String类型，只有当传参类型是String类型时才能进行sql注入



## 一、执行sql语句的几种方式

- 使用JDBC的Java.sql.Statement执行SQL语句
- 使用JDBC的java.sql.PreparedStatement执行SQL语句
- 使用Hibernate执行SQL语句
- 使用Mybatis执行SQL语句



## 二、常见Java SQL注入

### 1、Statement执行sql

1. SQL注入是由sql语句参数直接动态拼接完成的

   ```java
   final String DBDriver="com.mysql.cj.jdbc.Driver";
   final String DBURL="jdbc:mysql://localhost:3306/user";
   final String DBUser="root";
   final String DBPassWord="root";
   Connection con=null;
   Statement st=null;
   ResultSet res=null;
   try {
       Class.forName(DBDriver);
       con = DriverManager.getConnection(DBURL,DBUser,DBPassWord);
       st =con.createStatement();
       String id=req.getParameter("id");
       res=st.executeQuery("select * from  where \"id\"="+id);
       while (res.next()) {
           int p = res.getInt("id");
           String n = res.getString("");
           String s = res.getString("")
       }
   }catch(Exception e){
       out.println(e);
   }
   ```

   看着上边代码多其实关键就看这一段

   前端获取id的请求参数，后端没有进行任何过滤直接进行执行，就产生了sql注入

   ```java
   String id=req.getParameter("id");
   res=st.executeQuery("select * from  where \"id\"="+id);
   ```

### 2、PreparedStatement拼接错误

> PreparedStatement不会的拼接的字符串进行预处理

1. 拼接sql发生错误

   ```java
   public class statement {
       public static void main(String[] args) throws ClassNotFoundException, SQLException {
           Class.forName("com.mysql.jdbc.Driver");
           //连接数据库
           Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/user?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC","root","root");
   
           String username1 = "username%'or '1'='1'#";
           String id ="2";
           //将拼接变量变为?占位
           String sql = "select * from tb_user where id= ? ";
           sql+="and username like '%"+username1+"%'";
           System.out.println("处理前的sql语句："+sql);
   
           //将statement对象换成PreparedStatement，并且传入的值要为转换为占位符的sql语句
           //将传入的sql语句进行预处理
           PreparedStatement preparedStatement = connection.prepareStatement(sql);
           System.out.println("预处理后sql语句"+preparedStatement);
   
           //给预处理好的占位参数进行复制
           preparedStatement.setString(1,id);
   
           System.out.println("填充之后的sql语句"+preparedStatement);
   
           //执行sql语句
           ResultSet resultSet = preparedStatement.executeQuery();
   }
   ```

   关键代码在

   > username1是一个典型的注入语句
   >
   > sql+="and username like '%"+username1+"%'";这里又进行了拼接
   >
   > 最终的语句变为：select * from tb_user where id= '2' and username like '%username%'or '1'='1'#%'
   >
   > 就能实现注入
   >
   > PreparedStatement不会对拼接的字符串进行预处理

   ```java
   String username1 = "username%'or '1'='1'#";
   String id ="2";
   //将拼接变量变为?占位
   String sql = "select * from tb_user where id= ? ";
   sql+="and username like '%"+username1+"%'";
   System.out.println("处理前的sql语句："+sql);
   ```

   

### 3、order by注入

> 可以使用if来进行盲注

order by注入：https://blog.csdn.net/m0_60571990/article/details/129627365

> 在Java中PrepareStatement能够有效的避免sql注入，可是有些特殊情况下是不能使用PrepareStatement的
>
> 比如，程序中使用了order by来排序就无法使用PrepareStatement来进行预编译

1. 原理

   order by是进行排序的，order by 后面需要加字段名或字段位置，而字段名是不能带引号的，否则就会被认为是一个字符串而不是字段名，PrepareStatement进行预编译时会自动给值带上引号

   比如：`order by id;`进行预编译后就会变为`order by 'id';`这样语句就会不会产生效果

2. 当时order by时就需要使用字符串来进行拼接

   ```java
   Class.forName("com.mysql.jdbc.Driver");
   //连接数据库
   Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/user?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC","root","root");
   
   String id ="2 or 1=1";
   //将拼接变量变为?占位
   String sql = "select * from tb_user"+ " order by "+id;
   
   System.out.println("处理前的sql语句："+sql);
   
   //将statement对象换成PreparedStatement，并且传入的值要为转换为占位符的sql语句
   //将传入的sql语句进行预处理
   PreparedStatement preparedStatement = connection.prepareStatement(sql);
   System.out.println("预处理后sql语句"+preparedStatement);
   
   //给预处理好的占位参数进行复制
   ResultSet resultSet = preparedStatement.executeQuery();
   ```

   关键代码

   > 使用order by加PreparedStatement只能进行拼接，在拼接的过程中就有可能产生sql注入

   ```java
   String id ="2 or 1=1";
   //将拼接变量变为?占位
   String sql = "select * from tb_user"+ " order by "+id;
   ```



### 4、%和_模糊查询

1. 原理

   在Java预编译查询中不会对`%`和`_`进行转义处理，而`%`和`_`刚好时like查询的通配符，如果没有做好过滤，就有可能导致恶意模糊查询，暂用服务器性能，甚至可能耗尽资源，造成服务器宕机

   **只能进行模糊查询，遇到需要闭合的情况还是无法注入单引号会被转义**

2. 代码如下

   ```java
   Class.forName("com.mysql.jdbc.Driver");
   //连接数据库
   Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/user?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC","root","root");
   
   String username ="%%%";
   //将拼接变量变为?占位
   String sql = "select * from tb_user where username like ?";
   
   System.out.println("处理前的sql语句："+sql);
   
   //将statement对象换成PreparedStatement，并且传入的值要为转换为占位符的sql语句
   //将传入的sql语句进行预处理
   PreparedStatement preparedStatement = connection.prepareStatement(sql);
   preparedStatement.setString(1,username);
   System.out.println("预处理后sql语句"+preparedStatement);
   
   //给预处理好的占位参数进行复制
   
   ResultSet resultSet = preparedStatement.executeQuery();
   ```

   核心代码如下

   ```java
   String username ="%%%";
   //将拼接变量变为?占位
   String sql = "select * from tb_user where username like ?";
   ```

3. 防御

   直接将%进行过滤



## 二、MyBatis

### 1、MyBatis中#{}和${}的区别

1. #{}在底层实现了使用`?`作为占位符来生成`PreparedStatement`，也是参数化查询预编译的机制，这样即快又安全、

   ```sql
   SELECT * FROM USER WHERE id=#{id}//安全的写法
   ```

   ${}将传入的数据直接显示生成在sql语句中，类似于字符串拼接，可能会出现sql注入的风险

   ```sql
   SELECT * FROM USER WHERE id=${id}//不安全的写法
   ```

   **最大的区别就是`#{id}`会对传入的引号进行转义`#{id}`不会进行转义**

### 2、MyBatis常见SQL注入漏洞

#### 2.1、order by查询

1. 原理

   order by句子不能使用参数化查询，只能使用字符串拼接

   而#{}会进行参数化

   ```mysql
   #如果使用了 #{id}
   select * from tb_user order by  #{id}
   #在执行sql语句时就会转换为，这样order by中的值是不能带有引号的，这样order by就不会产生效果
   select from tb_user order by 'id'
   ```

   **所以只能使用${}**

2. 代码

   ```xml
       <select id="findUserById" resultType="mybatis.mode.user">
           select * from tb_user order by  ${id}
       </select>
   ```



#### 2.2、like查询

1. 原理

   在Mybatis框架中使用`like`查询只能使用`${}`来进行赋值

   如果使用`#{}`来进行赋值就会报错

   ```mysql
   #当#{id}进行赋值时，允许程序时会报错无法执行
   select * from tb_user where username like '%#{id}%'
   ```

   所以只能使用`${id}`来进行赋值

   ```mysql
   select * from tb_user where username like '%${id}%'
   ```

2. 代码如下

   > 如果username传入的值为：`AAAA'union SELECT 1,2,3,4,5,6,DATABASE()#'`就会实现sql注入

   ```xml
   <select id="findUserById" resultType="mybatis.mode.user">
   	select * from tb_user where username like '%${username}%'
   </select>
   ```



#### 2.3、in参数

1. 原理

   MyBatis框架的in语句中使用#`#{}`和`${}`,其传入的参数类似于`"'id1','id2','id3','id4'"`。每个参数都不一样

   而`#{}`会将in中的多个参数当作一个整体

   ```mysql
   select * from tb_user where username in(#{id})
   #传递参数，在这里就传递的多个参数就被当作一个参数偏离了程序设计逻辑，所以只能使用${}
   select * from tb_user where username in(''id1','id2','id3','id4'')
   ```

2. 使用${}来进行参数传递

   > 传递的参数还是为单个参数，不会偏离程序设计逻辑，这样就有可能存在sql注入

   ```mysql
   select * from tb_user where username in(${id})
   #传递参数，在这里就传递的多个参数就被当作一个参数偏离了程序设计逻辑，所以只能使用${}
   select * from tb_user where username in('id1','id2','id3','id4')
   ```

   

## 三、常规注入代码审计

1. 关键字查找

   ```mysql
   Statement
   createStatement
   PreparedStatement
   like '%${
   in(${
   select
   update
   insert
   $
   ```

   





项目实战:https://zhuanlan.zhihu.com/p/652732707

https://www.cnblogs.com/lzstar/p/15622055.html