# JDBC-Java数据库连接

JDBC(Java dataBase Connection)基本配置

```java
/**
 * jdbc 四大配置参数
 * driverClassName: com.mysql.jdbc.Driver （固定的）
 * url: jdbc:mysql://ip地址:3306/db_name数据库名称
 * username: root 数据库登录用户名
 * password: 123456 登录密码
 */
// 加载驱动类
Class.forName("com.mysql.jdbc.Driver") 
// 使用url、username、password 得到连接对象
Connection con = DriverManager.getConnection(
  "jdbc:mysql://localhost:3306/mysqlTest2", "root", "123456");

//url: jdbc:mysql://ip地址:3306/db_name数据库名称
// jdbc协议格式！jdbc:工商的名称:子协议(工商自己来规定)
// 对mysql而言，它的子协议结构: //主机:端口/数据库名称

// 对数据库做在增、删、改
// 通过Connection对象创建Statement
// Statement语句的发送器，它的功能就是向数据库发送sql语句
// 通过Connection得到Statement对象
Statement stmt = con.createStatement;
// 使用 Statement 发送SQL语句, 增、删、改
// String sqlStr = "INSERT INTO tb_stu VALUES('ITCAST_003','test')";
// String sqlStr = "UPDATE tb_stu SET ...";
String sqlStr = "DELETE FROM tb_stu WHERE ...";
int r = stmt.executeUpdate(sqlStr);
System.out.println(r);

// 查询
// 使用Statement的ResultSet rs = executeQuery(String querySQL)
ResultSet rs = stms.executeQuery("SELECT * FROM TB_EMP");
// 解析ResultSet
// 把行光标移动到第一行，可以调用next()方法完成
// 把光标移动到下一行，判断next()是否存在
while(rs.next()) {
  // 通过列编号来获取该列的值
  int empno = rs.getInt(1);
  // 通过列名称获取该列的值
  String ename = rs.getString("ename");
  
}

// 关闭资源 - 倒关
rs.close();
stmt.close();
con.close(); // 必须关闭

public void query() {
  Connection con = null;
  Statement stmt = null;
  ResultSet rs = null;
  try {
    con = getConnection();
    stmt = con.createStatement;
    String sqlStr = "Select * from TB_xxx";
    rs = stmt.executeQuery(sql);
    ...
  } catch(Exception e) {
    throw new RuntimeException(e);
  } finally {
    try {
      if(rs != null) rs.close();
      if(stmt != null) stmt.close();
      if(con != null) con.close();
    } catch(SQLException e){}
  }
}
```

```java
// 工具类 类似单利写法
Public class DaoFactoryUtils {
  // 以下这种写法类似单利，调用时查看对象是否创建，如果没有创建就进行创建，如果已创建了的，将不会重复创建
  private static Properties props = null;
  // 只在DaoFactoryUtils类加载时执行一次
  static {
    try {
      // io流加载配置文件
      InputStream in = DaoFactory.class.getClassLoader()
        .getResoutceAsStream("dao.properties");
      
      props = new Properties();
      props.load(in);
    } catch (Exception e){
      throw new RuntimeException(e);
    }
  }
  
  // 返回UserDao实体类
  public static UserDao getUserDao() {
    // 得到类名
    String daoClassName = props.getProperty("cn.sys.dao.UserDao");
    // 通过反射来创建实现类对象
    Class clazz = Class.forName(daoClassName);
    
  }
}

```

#### 事务四大特性（ACID）：

**原子性(Atomicity)：**事务中所有操作是不可再分割的原子单位。事务中所有操作要么全部执行成功，要么全部执行失败。
**一致性(Consistency)：**事务执行后，数据库状态与其他业务规则保持一致。比如转账业务，无论事务执行成功与否，参与转账的两个账号余额之和应该是不变的。
**隔离性(Isolation)：**隔离性是指在并发操作中，不同事务之间应该隔离开来，使每个并发中的事务不会互相干扰。
**持久性(Durability)：**一旦事务提交成功，事务中所有的数据操作都必须被持久化到数据库中，即使提交事务后，数据库马上崩溃，在数据库重启后，也必须能保证通过某种机制恢复数据。

#### MySQL中的事务

在默认情况下，mysql每执行一条SQL语句，都是一个单独的事务。如果需要在事务中包含多条SQL语句，那么需要开启事务和结束事务。

```mysql
-- 开启事务：start transaction;
-- 结束事务：commit(提交)或rollback(回滚);
-- 事务回滚，之前所有操作都会被撤销
START TRANSACTION;
UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;
ROLLBACK;

-- 提交事务
START TRANSACTION;
UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

#### JDBC事务

在jdbc中处理事务，都是通过Connection完成的。
**同一事务中的所有操作，都在使用同一的Connection**

Connection的三个方法与事务相关：
setAutoCommit(boolean)：设置<u>是否自动提交</u>事务，true(默认)是自动提交，每执行一条SQL语句都是单独的事务，false是手动提交，<u>就相当于开启事务</u>。
 Con.setAutoCommit(false); 表开启事务
Con.commit(); 事务提交
Con.rollback(); 事务回滚

```java
// jdbc 处理事务的代码格式
try {
  // 开启事务
  con.setAutoCommit(false);
  ...
  // try 最后提交事务
  con.commit();
} catch() {
  // 回滚事务
  con.rollback();
}

public class AccountDao {
  
// 修改指定用户余额
public void updateBalance(Connection con，String name, double balance) {
  try{
    // 给出SQL模板，创建pstmt
    String uSQL = "update account set balabce = balabce + ? where name = ?";
    PreparedStatement pstmt = con.prepareStatement(uSQL);
    // 参数赋值
    pstmt.setDouble(1, balance（值--参数）);
    pstmt.setDouble(2, name（值--参数）);
    
    // 执行
    pstmt.executeUpdate();
    
  } catch(Exception e) {
    throw new RuntimeException(e);
  }
}
}

public class Demo1 {
  // 转账方法 from -> to
  public void zhuanZhang(String from, String to, double money) {
    // 对事物的操作必须使用 Connection 对象
    // JdbcUtils 对 Connection 进行封装的一个工具类
      Connection con = JdbcUtils.getConnection();
    try {
  		// 开启事务
  		con.setAutoCommit(false);
      
      AccountDao dao = new AccountDao();
      // from 减去相应金额
      dao.updateBalance(con，from, -money);
      // to 加上相应金额
      dao.updateBalance(con，to, money);
      
  		// try 最后提交事务
  		con.commit();
      con.close();
		} catch(Exception e) {
 		 	// 回滚事务
 			 try {
          con.rollback();
       } catch(SQLException e1) {
         
       }
		}
  }
}

```

#### 并发事务问题

因为并发事务导致的问题大致有5类，其中2类是更新问题，3类是读问题：
1、脏读(dirty read)：读到另一个事务的未提交更新数据，即读取到了脏数据。
2、不可重复读(unrepeatable read)：对同一记录的两次读取不一致，因为另一事务对该记录做了修改。
3、幻读(虚读)(phantom read)：对同一张表的两次查询不一致，因为另一事务插入了一条记录。

**脏读**
事务1：张山给李思转账100元
事务2：李思查看自己账户

T1：事务1：开始事务
T2：事务1：张山给李思转账100元
T3：事务2：开始事务
T4：事务2：李思查看自己账户，看到自己账户多出100元--(脏读)
T5：事务2：提交事务
T6：事务1：回滚事务，回到转账之前的状态

#### 数据库连接池

**池参数：**
初始大小：10个（设置初始值）
最小空闲连接数：假如设置3个，当连接数少于3时需要新创建连接
增量：一次创建的最小单位（比如5个）
最大空闲连接数：空闲连接数量最大值（比如12，现有15个连接需要关闭3个）
最大连接数：20个（连接创建数量总计最大值，达到峰值再来获取连接需要等待）
最大等待时间：比如1000ms，在获取连接最大等待时间，对应最大连接数

**连接池也是使用四大连接参数来完成创建连接对象！！！**

**实现接口：**
连接池必须实现：javax.sql.DataSource 接口！
Javax.sql.DataSource 是Java 1.4 版本才有的

连接池返回的Connection对象，它的close()方法不是关闭，而是<u>把连接归还给连接池</u>。

```java
import org.apache.commons.dbcp.BasicDataSource;

// DBCP 连接池
public class DemoTest {
  public void function1() throws SQLException {
    /**
     * 1.创建连接池对象
     * 2.配置四大参数
     * 3.配置池参数
     * 4.得到连接对象
     */
    BasicDataSource dataSource = new BasicDataSource();
    dataSource.setDriverClassName("com.mysql.jdbc.Driver");
    dataSource.setUrl("jdbc:mysql://localhost:3306/mysqlTest3");
    dataSource.setUsername("root");
    dataSource.setPassword("123456");
    
    // 最大连接
    dataSource.setMaxActive(20);
    // 最小空闲连接
    dataSource.setMinIdle(3);
    // 最大等待连接
    dataSource.setMaxWait(1000);
    // initialSize = 0; 初始连接池大小，默认值0
    // maxActive = 8; 最大连接数，默认值8，如果非正数表无限制，无限大
    // maxIdle=8;最大空闲数，默认值8
    // minIdle=0; 最小空闲数，默认0，少于设定值(5),此时为3需要再新增2
    // maxWait=-1；默认值-1，表无限等待，设置值为毫秒值
    
    // 得到连接对象 导入包为：java.sql.Connection
    Connection con = dataSource.getConnection();
    // 打印con类名 org.apache.commons.dbcp.PoolingDataSource$PoolGuardConnectionWrapper
    System.out.println(con.getClass.getName);
    
    // 把连接归还给池！
    con.close();
    
    // 连接池底层依赖mysql的Connection，对mysql中Connection的close进行增强，使得close后将连接返回给连接池
  }
  
  // C3P0连接池的基本使用方式，类是：ComboPooledDataSource
  public void function2() throws SQLException {
   
    ComboPooledDataSource dataSource = new ComboPooledDataSource();
    dataSource.setDriverClass("com.mysql.jdbc.Driver");
    dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mysqlTest3");
    dataSource.setUser("root");
    dataSource.setPassword("123456");
    
    dataSource.setAcquireIncrement(5); // 每次增量
    dataSource.setInitialPoolSize(20); // 初始连接数
    dataSource.setMinPoolSize(2); // 最小连接数
    dataSource.setMaxPoolSize(50); // 最大连接数
    
    Connection con = dataSource.getConnection();
    System.out.println(con.getClass.getName);
   
    con.close();
  }
}
```

装饰者模式

```java
// 继承
// 1、增强的内容是死的，不能动
// 2、被增强的对象也是死的
// 使用继承会使类增多！！
// ----比如-------
class 咖啡类 {}
class 有糖咖啡 extends 咖啡类 {}
class 加盐咖啡 extends 咖啡类 {}
class 加奶咖啡 extends 咖啡类 {}
class 加糖加奶咖啡 extends 咖啡类 {}

//-----------装饰者模式--------------
// 增强的内容是不能修改的
// 被增强的对象可以是任意的，任意组合
class 咖啡类 {}
class 有糖咖啡 extends 咖啡类 {}
class 加盐咖啡 extends 咖啡类 {}
class 加奶咖啡 extends 咖啡类 {}

咖啡 a = new 有糖咖啡();
咖啡 b = new 加奶咖啡(a); // 对a进行装饰，就是给a加奶
咖啡 c = new 加盐咖啡(b); // 糖+奶+盐

// 在不知道增强对象的具体类型时，可以使用！

```

