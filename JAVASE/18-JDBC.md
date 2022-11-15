## 0. 客户端操作mysql数据库的方式：

1. 使用第三方客户端来访问数据库。如：SQLyog,Navicat,SQLWave等。
2. 使用mysql自带的命令行方式。
3. 通过java来访问mysql数据库，即JDBC。

## 1. 什么是JDBC

- JDBC即`Java DataBase Connectivity`，它是java访问数据库的规范，定义了一些接口，具体的实现由各大数据库厂商来实现。
- 每个数据库厂商根据自家的数据库的通信格式编写好自己的数据库驱动。(数据库驱动即操作数据库需要的具体的实现类)。**所以我们只需要会调用JDBC接口中的方法即可，数据库驱动由数据库厂商提供。**

## 2. 为什么要使用JDBC

- JDBC是用来执行SQL语句的java API，市面上有很多数据库，本来我们在使用java语言来操作不同数据库时要学习不同的API，这样来说对程序员很麻烦。为了简化这个操作，sun公司定义了一套 javaAPI接口(即jdbc)，由数据库厂商负责在定义的接口下实现具体的代码，程序员只要调用一套接口方法就可以使用多个数据库。针对不同的数据库只要下载相应的驱动程序然后使用固定的方法就行。

![图片](images/18-JDBC/640)在这里插入图片描述

**jdbc API：**

> 提供者：java官方
>
> 内容：供开发者调用的接口
>
> java.sql和javax.sql：DriverManager类，Connection接口，Statement接口，ResultSet接口。

**JDBC DriverManger**:

> 提供者：java官方
>
> 作用：管理不同的jdbc驱动

**JDBC 驱动：**

> 提供者：数据库厂商
>
> 作用:负责连接不同的数据库

## 3. 使用JDBC的好处

- 程序员如果要开发访问数据库的程序，只需要会调用JDBC接口中的方法即可，不用关注类是怎么实现的。
- 使用同一套代码，进行少量的修改就可以访问其他JDBC支持的数据库。

## 4.  JDBC的快速入门

#### 4.1 步骤

- 导入驱动jar包。`mysql-connector-java-5.1.37-bin.jar`。复制jar包到项目的libs目录下，右键- ->Add As Library。
- 注册驱动
- 获取数据库连接对象Connection
- 定义sql
- 获取执行sql语句的对象 Statement
- 执行sql，接受返回结果
- 处理结果
- 释放资源

#### 4.2 代码演示

**1 导入驱动jar包**

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWby7vhJXURuBSo5boUnqq3EAWKmFjJDEEQiaSoXiad2R4z4L2DXfPDzcLA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```
public static void main(String[] args) {
     Connection conn=null;
     Statement stmt=null;
     try {
            //2.注册驱动
            Class.forName("com.mysql.jdbc.Driver");
            //3.获取数据库连接对象
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/jdbctest?useUnicode=true&characterEncoding=UTF-8", "root", "root");
            //4.定义sql语句
            String sql = "update student set age=100 where id =1";
            //5.获取执行sql的对象 Statement
            stmt = conn.createStatement();
            //6.执行sql
            int  count = stmt.executeUpdate(sql);
            //7.处理结果
            System.out.println(count);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            //8.释放资源
            if(stmt!=null){
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if(conn!=null){
                 try {
                    conn.close();
                 } catch (SQLException e) {
                     e.printStackTrace();
                 }
            }

        }

    }
}
```

结果：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbVGXmiaGHicBgPj8lNNnf0MvyNT9ArJzibqkWpM6qTWSibbzDz3EQgYNHKg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 4.3 详解各个对象：

> 1. Class.forName(com.mysql.jdbc.Driver)  注册驱动

加载和注册数据库驱动，它会抛出一个`ClassNotFoundException`异常。

Class.forName()：装载一个类并且对其进行实例化的操作。

为什么这行代码可以注册驱动。通过查看Driver类的源码发现它在装载这个类时会自动注册驱动。

![图片](images/18-JDBC/O8WmZ7nA)

**注意：**从jdbc3开始(目前已经普遍使用的版本)或者mysql5之后的驱动jar包可以不用注册驱动而直接使用。Class.forName这句话可以省略。

**补充**：在注册驱动时有两种方式，1.DriverManger.registerDriver(new com.mysql.jdbc.Driver)  2. Class.forName("com.mysql.jdbc.Driver")   方法一会导致驱动注册两次，过度依赖mysql的API，脱离mysql的开发包，程序无法编译。 方法二驱动只要加载一次，不需要具体的驱动，灵活性高。

> 1. DriverManager.getConnection  获取数据库连接对象

静态方法：`Connection getConnection(String url,String user,String password)` ：通过连接字符串，用户名，密码来得到数据库的连接对象。

url ：不同的数据库url是不同的，mysql的写法：`jdbc:mysql://localhost:3306/数据库名[?参数名=参数值]`

user ：登录的用户名

password：登录的密码

**url的地址格式：**

```
协议名:子协议://服务器名或者IP地址:端口号/数据库名?参数=参数值
```

url用于标识数据库的位置，程序员通过url地址告诉jdbc程序连接哪个数据库。

![图片](images/JDBC/%E5%8D%8F%E8%AE%AE)

**简写**

如果是本地服务器且端口号是3306，则简写为：`jdbc:mysql://数据库名`

**乱码的处理**

如果数据库出现乱码，可以指定参数?characterEncoding=UTF-8,让数据库以utf-8来编码。

> 1. stmt = conn.createStatement();  获取执行sql的对象 Statement

Statement createStatement()：创建一条SQL语句对象。

PreparedStatement PrepareStatement(sql)  :升级版，后面解释

> 1. stmt.executeUpdate(sql)：使用Statement对象执行SQL语句

Statement作用：代表一条语句对象，用于发送SQL语句给服务器，用于执行静态SQL并返回它所生成结果的对象。

**stmt.executeUpdate(sql)**：返回int型数据,表示对数据库影响的行数。用于发送DML语句，增删改的操作，**insert,update，delete**

**stmt.executeQuery(sql)**：返回一个查询的结果集。用于发送DQL语句，执行查询操作，**select**

> 1. 释放资源

- 需要释放的资源对象：ResultSet结果集，Statement语句，Connection连接
- 释放原则：先开的后关，后开的先关，ResultSet - ->Statement- ->Connection
- 放在finally代码块中并处理异常。
- java和数据库属于进程之间的通信，开启之后一定要关闭，不释放会出问题。

#### 4.4 概述

![图片](images/18-JDBC/0ue4KA)

## 5. JDBC的CRUD代码演示

- **8个步骤**

```
 public static void main(String[] args) {
   Connection conn=null;
   Statement stmt=null;
   ResultSet rs=null;
    try { //1.导入jar包
         //2.注册驱动
            Class.forName("com.mysql.jdbc.Driver");
         //3.获取数据库连接对象Connection
            conn= DriverManager.getConnection("jdbc:mysql://localhost:3306/jdbctest?useUnicode=true&characterEncoding=UTF-8","root","root");
        //4. 定义SQL
//            String sql="select * from student";
            String sql1="update student set age=99 where id=1";
            String sql2="insert into student values(null,'xiaoming',33,'chongqing')";
//            String sql3="DELETE FROM student WHERE id=4";
        //5.获取执行SQL的对象Statement
            stmt=conn.createStatement();
//            rs=stmt.executeQuery(sql);
        //6.执行SQL，接收返回结果
            int count1 =stmt.executeUpdate(sql1);
            int count2 =stmt.executeUpdate(sql2);
//            int count3 =stmt.executeUpdate(sql3);
       // 7.处理结果
            
//            while (rs.next()){
//                Integer id=rs.getInt("id");
//                String name=rs.getString("name");
//                Integer age=rs.getInt("age");
//                String address=rs.getString("address");
//                System.out.println("id:"+id+" 名字："+name+" age:"+age+" address:"+address);
//
//            }
            System.out.println(count1);
            System.out.println(count2);
//            System.out.println(count3);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
        //8.释放资源
            if(rs!=null){
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if(stmt!=null){
                try {
                    stmt.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
            if(conn!=null){
                try {
                    conn.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
    }
}
```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbCqT92N4Mz0L5frhJic48h7v7mKmAR30ldibsr1FibDicpvJ5kTiaqSq64pQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 6. 对JDBC CRUD的详细说明

#### 6.1 SQL语句

- SQL语句和SQLyog中使用的完全一样，写之前可以在sqlyog中先测试下。

#### 6.2 executeUpdate 和 executeQuery的区别

- executeUpdate ：用来执行增删改sql语句，返回的是Int型值，表示对数据库的影响行数。
- executeQuery：用来执行查sql语句，返回的是一个ResultSet对象，这个对象封装了数据库查询的结果集。对结果集进行遍历，取出每一条对象。
- (ResultSet) rs.next() 游标向下移动一行，判断当前指向的记录是否还有下一条记录，如果返回true，表示还有下一条，否则返回false。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWb8a29MxwuBtAtEJ9gsovDjTZJv00evqic14VMJibAr1mibaWuHiaq9HJPEw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- **区别**：前者对数据库内容进行了改变(位置顺序等也算)，所以要执行更新update语句。后者仅仅是查询一下数据库的东西，没有对数据库造成改变，所以执行查询query语句。

#### 6.3 对executeQuery返回的结果进行处理

- 返回的是ResultSet结果集对象。
- 有两种方法来取记录。

通过列名：getInt("id"),getString("name"),getBoolean("gender"),getDate("birthday")

通过列号：getInt(1),getString(2),getBoolean(3),getDate(4).下标从1开始，这个列号和数据库中各列的位置有关。

#### 6.4 常用的数据类型转换表

![图片](images/18-JDBC/BPgJA)

## 7. 存在的问题：SQL注入

- 新建一个数据库表

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbu7hCxxhl0HzpKFNDISd1nlgA6qWdEWkgspOSZVRRYIlepgewibficJJw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- 执行一下SQL语句

```
 String sql="select * from user where name='fhsf'and password='a'or 'a'='a'";
```

虽然账号和密码都不正确，但还是可以从数据库表中查询出来数据，这就造成的风险。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWb1eAKIMZcdmcKSb4qqn4JDq4iaDBico67t40I2Aas33xBUrWicVevOWn4g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

SQL注入：：在拼接SQL时，有一些SQL的特殊关键字参与字符串的拼接，会造成安全性问题。输入用户随便，输入密码：'a' or 'a'='a' 。

**如何解决**：使用PreparedStatement对象来代替Statement

## 8. PreparedStatement 接口

#### 8.1PreparedStatement 的执行原理

- PreparedStatement是Statement接口的子接口，继承于父接口的方法。它是一个预编译的SQL语句。

  > **Statement:**
  >
  > ```
  > String sql = "select * from student where name=lisi and id=1";
  > stmt=conn.createStatement();
  > rs=stmt.executeQuery(sql);
  > 处理结果rs.next()....
  > ```
  >
  > **PreparedStatement:**
  >
  > ```
  > String sql = "select * from student where name=? and id=?";
  > ps = conn.prepareStatement(sql);
  > ps.setString(1,"lisi");
  > ps.setInt(2,1);
  > rs=ps.executeQuery();
  > 处理结果rs.next()....
  > ```

![图片](images/18-JDBC/A3KNcQ)

#### 8.2 PreparedStatement的好处

- PreparedStatement()会将SQL语句发送给数据库预编译，PreparedStatement会引用预编译后的结果，可以多次传入不同的参数给PreparedStatement对象并执行，减少SQL语句的编译次数，提高效率。
- 安全性更高，没有SQL注入的隐患。
- 提高了程序可读性。

#### 8.3 使用PreparedStatement的步骤

- 编写SQL语句，未知内容用？占位。`select * from user where name=? and password=?;`
- 获得PreparedStatement对象
- 设置实际参数，setXxx(占位符的位置，真实的值)
- 执行参数化SQL语句
- 处理结果
- 关闭资源

#### 8.4 代码演示

```
public static void main(String[] args) {
        Connection conn=null;
        PreparedStatement ps=null;
        ResultSet rs = null;
        try {
            //2.注册驱动
            Class.forName("com.mysql.jdbc.Driver");
            //3.获取数据库连接对象
            conn= DriverManager.getConnection("jdbc:mysql://localhost:3306/jdbctest?useUnicode=true&characterEncoding=UTF-8","root","root");
             //4.定义sql语句
            String sql = "select * from student where name=? and id=?";
            //5.获取执行sql的对象 PreparedStatement
            ps = conn.prepareStatement(sql);
            //6.设置实际参数
            ps.setString(1,"lisi");
            ps.setInt(2,1);
            //7.执行参数化sql语句
            rs=ps.executeQuery();
            //8.处理结果
            while (rs.next()){
                /*Integer id=rs.getInt(1);
                String name = rs.getString(2);
                Integer age = rs.getInt(3);
                String address = rs.getString(4);*/
                Integer id=rs.getInt("id");
                String name = rs.getString("name");
                Integer age = rs.getInt("age");
                String address = rs.getString("address");
                System.out.println(id+" "+name+" "+age+" "+address);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            //关闭资源
            if(rs!=null){
                try {
                    rs.close();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            if(ps!=null){
                try {
                    ps.close();
                } catch (Exception throwables) {
                    throwables.printStackTrace();
                }
            }
            if(conn!=null){
                try {
                    conn.close();
                } catch (Exception throwables) {
                    throwables.printStackTrace();
                }
            }
        }
```

## 9. 数据库工具类jdbcUtils

#### 9.1 为什么要创建jdbcUtils工具类

- 我们在使用jdbc来CRUD，会发现注册驱动，获取连接以及关闭资源这些代码都一样但要重复几次，**所以如果一个功能经常使用，我们要把它做成一个工具类，可以在不同的地方重用，来简化书写**。
- 可以把几个字符串定义成常量：用户名，密码，url，驱动类

#### 9.2 具体实现代码

**先在src文件下new一个file，命名为jdbc.properties**

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbVTCrN21SAmgbp9RvgBf3wcVf3OoZ5DQXGZdJU71Q6vPicQzOfwPCIDg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**jdbcUtils工具类**

```
public class jdbcUtils {
    private static String url;
    private static String user;
    private static String password;
    private static String driver;
        /*
        * 文件的读取，只需要读取一次即可拿到这些值，使用静态代码块
        * */
    static{
        try {
           //获取文件路径类加载器
            InputStream in=jdbcUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
           //读取文件
            Properties pro =new Properties();
            pro.load(in);
      //获取数据，赋值
            url = pro.getProperty("url");
            user=pro.getProperty("user");
            password=pro.getProperty("password");
            // 注册驱动
            driver = pro.getProperty("driver");
            Class.forName(driver);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    /*
    获取连接
    * */
    public static Connection getConnection() throws SQLException{
        return DriverManager.getConnection(url, user, password);
    }
    /*
    * 关闭连接
    * */
    public static void close(PreparedStatement ps,Connection conn){
        if(ps!=null){
            try {
                ps.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    /*
    * 关闭连接
    * */
    public static void close(ResultSet rs,PreparedStatement ps,Connection conn){
        if( rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        close(ps,conn);
    }
}
```

**测试类**

```
public class jdbcUtilsTest04 {
    public static void main(String[] args) {
        Connection conn=null;
        PreparedStatement ps=null;
        ResultSet rs=null;
        try {
            //获取连接
            conn=jdbcUtils.getConnection();
            //定义sql
            String sql = "select * from student where name=? and id=?";
            //获取执行SQL的对象
            ps=conn.prepareStatement(sql);
            ps.setString(1,"lisi");
            ps.setInt(2,1);
            //执行查询
            rs=ps.executeQuery();
            //处理结果
            if (rs.next()){
                Integer id=rs.getInt(1);
                String name = rs.getString(2);
                Integer age = rs.getInt(3);
                String address=rs.getString(4);
                System.out.println(id+" "+name+" "+age+" "+address);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            jdbcUtils.close(rs,ps,conn);
        }
    }
}
```

**结果：**

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbbQ6znVaiaLhfibNiaVp82c0xeSjKbibykfdicXCVkY9SRSW8cTM671FTZCg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 10 jdbc控制事务

#### 10.1 为什么要使用事务？

- 在使用jdbc时，如果涉及到事务操作，比如转账，会涉及到多条SQL语句，如果出现问题导致SQL语句中只执行了部分，则会造成严重的后果，所以引入的控制事务机制。

#### 10.2 具体的方法

- 开启事务：`setAutoCommit(boolean autoCommit);`设置该方法参数为false，表示关闭自动提交，相当于开启事务。
- 提交事务：`commit()` 当所有SQL都执行完提交事务
- 回滚事务：`rollback()`在catch中回滚事务。

#### 10.3 开发步骤

- 获取连接
- 开启事务
- 获取到PreparedStatement
- 使用PreparedStatement执行两次更新操作
- 正常情况下提交事务
- 出现异常回滚事务
- 最后关闭资源

#### 10.4 代码演示(使用jdbcUtils类)

```
public class shiwuTest05 {
    public static void main(String[] args) {
        Connection conn=null;
        PreparedStatement ps1=null;
        PreparedStatement ps2=null;
        ResultSet rs=null;
        try {
            //获取连接
            conn=jdbcUtils.getConnection();
            //开启事务
            conn.setAutoCommit(false);
            //定义SQL zhangsan-100 lisi+100
            String sql1 = "update money set balance=balance-? where name=?";
            String sql2 = "update money set balance=balance + ? where name=?";
            //获取到PreparedStatement
            ps1 = conn.prepareStatement(sql1);
            ps2 = conn.prepareStatement(sql2);
            //使用PreparesStatement执行操作
            ps1.setInt(1,100);
            ps1.setString(2,"zhangsan");
            ps2.setInt(1,100);
            ps2.setString(2,"lisi");
            ps1.executeUpdate();
            //出现异常
            System.out.println(100/0);
            ps2.executeUpdate();
            //提交事务,没有异常则提交，有异常则会被回滚事务rollback处理
            conn.commit();
            System.out.println("转账成功");
        } catch (Exception throwables) {
            throwables.printStackTrace();
            try {
                //事务的回滚，撤销之前的SQL操作
                conn.rollback();
            } catch (SQLException e) {
                e.printStackTrace();
            }
            System.out.println("转账失败");
        }finally {
            //关闭资源
            jdbcUtils.close(ps1,conn);
            jdbcUtils.close(ps2,null);
        }
    }
}
```

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbicHVRcvaLtcQRLMrRJ5icjnhw76gdgA7zheeHkQZOIlT8vXshRUfjAXQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

- **结果**：虽然SQL语句已经执行了一条，但数据库中数据并没有改变

## 11. 数据库连接池简述

#### 11.1 什么是数据库连接池

- 数据库连接池就是一个容器，里面存放数据库连接对象Connection。
- 当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，**从容器中获取连接对象**，用户访问完之后，会**将连接对象归还给容器**。

#### 11.2 为什么要使用数据库连接池

- 数据库连接的建立和关闭是非常耗资源的
- 频繁的打开关闭会造成系统性能的低下。
- jdbc没有保持连接的能力，一旦超过一定时间没有使用(大约几百毫秒)，连接就会被自动释放，每次新建连接都需要140毫秒左右的时间，所以耗费时间比较多。若使用连接池技术，随取随用，则每次取用只要10-20毫秒，这在高并发随机访问数据库的时候对效率的提升有很大帮助。

#### 11.3 实现

- 一般我们不去实现它，有数据库厂商来实现
- c3p0：数据库连接池技术，较老
- Druid：数据库连接池实现技术，阿里巴巴提供，性能比较好

#### 11.4 主要的接口方法

- 获取连接：getConnection()
- **归还连接：Connection.close()。并不是关闭连接，而是归还连接对象到连接池**。

## 12. c3p0的基本应用

#### 12.1 步骤

1. 导入jar包，三个，`c3p0-0.9.5.2.jar`,`mchange-commons-java-0.2.12.ja`,`mysql-connector-java-5.1.37-bin.jar`。新建一个libs目录，将jar包粘贴进去，并且右键add as library，引入项目中。
2. 定义配置文件：c3p0.properties或者c3p0-config.xml。将xml文件复制到src目录下。名字必须是c3p0-config.xml

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbPA4es71LBlnIH0rJbiaTVasz1RocjDbrrbthHFmqLDoHg0atI4q4jNA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

链接：https://pan.baidu.com/s/1A0YbJDVAj37cvkOXeRaiVQ 提取码：ypwl

1. 创建核心对象 数据库连接池对象 ComboPooledDataSource。`DataSource ds = new ComboPooledDataSource();`。可以通过new ComboPooledDataSource("xxx")来使用指定的配置读取数据库连接对象。
2. 获取连接：getConnection。`Connection conn = ds.getConnection();`
3. 定义sql，获取执行sql语句的对象 Statement，执行sql，接受返回结果，处理结果
4. 关闭资源。注意这里Connection调用close并不是和PreparedStatement,ResultSet一样关闭，而是将连接对象归还到连接池。

#### 12.2 小案例

- **c3p0-config.xml**

```
<c3p0-config>
  <!-- 使用默认的配置读取连接池对象 -->
  <default-config>
   <!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbctest</property>
    <property name="user">root</property>
    <property name="password">root</property>
    
    <!-- 连接池参数 -->
<!--    初始化申请的连接数量-->
    <property name="initialPoolSize">5</property>
<!--    最大的连接数量-->
    <property name="maxPoolSize">10</property>
<!--    超时时间，单位毫秒-->
    <property name="checkoutTimeout">3000</property>
  </default-config>
    
 <!-- 使用指定的配置读取连接池对象 -->
  <named-config name="otherc3p0"> 
    <!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/jdbctest</property>
    <property name="user">root</property>
    <property name="password">root</property>
    
    <!-- 连接池参数 -->
    <property name="initialPoolSize">5</property>
    <property name="maxPoolSize">8</property>
    <property name="checkoutTimeout">1000</property>
  </named-config>
```

- **c3p0Utils**

```
public class c3p0JdbcUtils {
    
    public static ComboPooledDataSource ds=new ComboPooledDataSource();
    
    public  static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }
    public  static void close( ResultSet rs, PreparedStatement ps,Connection conn){
        if (rs!=null){
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(ps!=null){
            try {
                ps.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(conn!=null){
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }



}
```

- **c3p0Test**

```
public class c3p0Test06 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement ps=null;
        ResultSet rs=null;

        try {
             conn=c3p0JdbcUtils.getConnection();
            String sql = "select * from student where name=?";
            ps=conn.prepareStatement(sql);
            ps.setString(1,"wangwu");
            rs=ps.executeQuery();
            if(rs.next()){
                Integer id=rs.getInt(1);
                String name = rs.getString(2);
                Integer age = rs.getInt(3);
                String address=rs.getString(4);
                System.out.println(id+" "+name+" "+age+" "+address);
            }
        } catch (Exception throwables) {
            throwables.printStackTrace();
        }finally {
           c3p0JdbcUtils.close(rs,ps,conn);
        }

    }
}
```

## 13. Druid的基本应用

#### 13.1 步骤

1. 导入jar包 druid-1.0.9.jar和mysql-connector-java-5.1.37-bin.jar。新建一个libs目录，然后右键add as library，引入项目中.
2. 定义配置文件：xxx.properties。可以叫任何名字，可以放在任意目录下。案例放在了src下。
3. 获取数据库连接池对象：通过工厂来获取。DruidDataSourceFactory
4. 获取连接。getConnection

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbD3a1FibiatU3t9vccRZWCMC3go9QHEAYiaX8ID0LDJevKknlHU2sYV0Fw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

链接：https://pan.baidu.com/s/1yAJK4isbT6zFuBLr5ZSIuw 提取码：oj5h

#### 13.2 案例

- **druid.properties**

```
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/jdbctest
username=root
password=root
# 初始化连接数量
initialSize=5
# 最大连接数
maxActive=10
# 最大等待时间
maxWait=3000
```

- **DruidUtils**

```
public class DruidUtils {
    //定义成员变量 DataSource
    private static DataSource ds;
    static {
        try {
            //加载配置文件
            Properties properties = new Properties();
                                    properties.load(DruidUtils.class.getClassLoader().getResourceAsStream("druid.properties"));
           //获取DataSource
            ds=DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
   // 获取连接池方法
  public static DataSource getDataSource(){
        return ds;
    }
    //获取连接
    public static  Connection getConnection() throws SQLException {
        return ds.getConnection();
    }
    //释放连接
    public static void close(ResultSet rs, PreparedStatement ps,Connection conn ){
        if(rs!=null){
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
            close(ps,conn);
    }
    public static  void close(PreparedStatement ps,Connection conn){
        if(ps!=null){
            try {
                ps.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(conn!=null){
            try {
                conn.close();//归还到数据库连接池
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```

- **DruidTest**

```
public class DruidTest07 {
    public static void main(String[] args) {
        Connection conn=null;
        PreparedStatement ps=null;
        try {
            conn=DruidUtils.getConnection();
            String sql = "insert into user values(?,?)";
            ps = conn.prepareStatement(sql);
            ps.setString(1,"liuer");
            ps.setInt(2,278891);
            int count=ps.executeUpdate();

        } catch (Exception throwables) {
            throwables.printStackTrace();
        }finally {
            DruidUtils.close(ps,conn);
        }
    }
}
```

## 14. jdbcTemplate

#### 14.1 简单了解

- Spring框架对jdbc的简单封装。提供了一个jdbcTemplate对象来简化Java的开发。
- 在之前的数据库连接池学习中，虽然优化了数据库连接对象，但其他的操作，比如定义sql，获取执行sql语句的对象 Statement，执行sql，接受返回结果，处理结果，关闭资源依然很麻烦，要写大量代码。
- 引入了jdbcTemplate技术后，它会自动帮我们完成sql语句的执行，接收返回结果并处理结果，同时关闭资源。我们要做的就只有写业务SQL语句了，很方便。
- 需要注意的是，**jdbcTemplate是在数据库连接池的技术上进一步封装了其他复杂重复的代码**。它需要依赖于数据源DataSource,即数据库连接池中已经处理好的mysql的连接池，用户的设置，连接对象的获取。

#### 14.2 步骤

1. 导入jar包，jdbcTemplate有5个包，但还依赖数据库连接池相关包和mysql连接的包。Druid和c3p0都可以。

   链接：https://pan.baidu.com/s/1PvTj4edMdTtNuPCI-gEsEw 提取码：utc0

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/XHYkicw0VusA5PE0Z8iaxs4mO1JxoaEHWbUMqzqjOibosKp4VLQcy7kUzicUfaM9GPXW8DYgaKWIjp3ibLzFSMHibr0g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

1. 创建jdbcTemplate对象。依赖于数据源DataSource

   `JdbcTemplate template =new JdbcTemplate(datasource);`

2. 调用JdbcTemplate的方法来完成crud操作

> - update()：执行DML语句。增删改语句
>
> - queryForMap():查询结果将结果集封装为map集合，将列名作为key，将值作为value 将这条记录封装为一个map集合。注意：这个方法查询的结果集长度只能是1
>
> - queryForList():查询结果将结果集封装为list集合。注意：将每一条记录封装为一个Map集合，再将Map集合装载到List集合中
>
> - query():查询结果，将结果封装为JavaBean对象。
>
>   query的参数：RowMapper * 一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean的自动封装 * new BeanPropertyRowMapper<类型>(类型.class)
>
> - queryForObject：查询结果，将结果封装为对象
>
>   一般用于聚合函数的查询

#### 14.3 代码演示

```
public class JdbcTemplateTest08 {
    public static void main(String[] args) {

        JdbcTemplate template = new JdbcTemplate(DruidUtils.getDataSource());
        //CRUD操作 更新
        /*String sql="update money set balance=500000 where name=?";
        int count = template.update(sql, "lisi");*/
        
       /* String sql1 = "insert into money values(?,?)";
        int count1 = template.update(sql1, "wangwu", 100000);*/

        /*String sql2 = "delete from money where name= ?";
        int count2 = template.update(sql2, "lisi");*/

        
        
        //查询id=2的记录，结果封装成map集合
        String sql3 = "select * from student where id=?";
        Map<String,Object> map= template.queryForMap(sql3, 2);
        System.out.println(map);
        /*运行结果：
        * {id=2, name=wangwu, age=19, address=shanghai}
        * */
        

        //查询所有记录 ，将其封装成List
        String sql4 = "select * from student";
        List<Map<String, Object>> list = template.queryForList(sql4);
        for (Map<String, Object> stringObjectMap : list) {
            System.out.println(stringObjectMap);
        }
        /*结果：
        * {id=1, name=lisi, age=99, address=beijing}
        {id=2, name=wangwu, age=19, address=shanghai}
        {id=3, name=zhangsan, age=43, address=shenzhen}
        {id=5, name=xiaoming, age=33, address=chongqing}
        * */
    }
}
```