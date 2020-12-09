## MyBatis-Plus使用

官方地址：https://baomidou.com/guide/

[MyBatis-Plus](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](http://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

### 特性

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

### 支持数据库

```
任何能使用 mybatis 进行 crud, 并且支持标准 sql 的数据库
```

### 初始化工程

创建一个空的Spring Boot工程
可以使用Spring Initializer快速初始化一个Spring Boot工程
地址：https://start.spring.io/

#### 添加依赖

引入Spring Boot Starter父工程

```java
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.0</version>
    <relativePath/>
</parent>
```

引入 `spring-boot-starter`、`spring-boot-starter-test`、`mybatis-plus-boot-starter`、`lombok`、`h2` 依赖：

```java
<dependencies>
  <!--启动Spring Boot 依赖-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
    </dependency>
  
    <!--测试用例 依赖-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
  
    <!--实体类需要使用的依赖，@Setter @Getter @Data @ToString -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
  
    <!--mybatis-plus插件 依赖-->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.4.1</version>
    </dependency>
  
    <!--数据库依赖，这里使用的是h2库，也可以换成MySQL、Oracle数据库-->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

#### 配置

在 `application.yml` 配置文件中添加 H2 数据库的相关配置：

```java
# DataSource Config
spring:
  datasource:
    driver-class-name: org.h2.Driver
    schema: classpath:db/schema-h2.sql
    data: classpath:db/data-h2.sql
    url: jdbc:h2:mem:test
    username: root
    password: test
```

在 Spring Boot 启动类中添加 `@MapperScan` 注解，扫描 Mapper 文件夹：
*扫描Mapper文件，可以通过在`application.yml`配置，进行全工程扫描相关与任何Mapper文件，以防遗漏*

```java
@SpringBootApplication
@MapperScan("com.baomidou.mybatisplus.samples.quickstart.mapper")
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(QuickStartApplication.class, args);
    }
}
```

#### 编码

```java
// 编写实体类 User.java（此处使用了 Lombok 简化代码）
@Data
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}

// 编写Mapper类 UserMapper.java
public interface UserMapper extends BaseMapper<User> {

}

// 编写测试类进行测试使用
@RunWith(SpringRunner.class)
@SpringBootTest
public class SampleTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    public void testSelect() {
        System.out.println(("----- selectAll method test ------"));
        // 直接使用mapper进行SQL查询
        List<User> userList = userMapper.selectList(null);
        Assert.assertEquals(5, userList.size());
        userList.forEach(System.out::println);
    }
}

/**
 * UserMapper 中的 selectList() 方法的参数为 MP 内置的条件封装器
 * Wrapper，所以不填写就是无任何条件, 也可以不写xml文件进行编写SQL
 */
```

### 安装

全新的 `MyBatis-Plus` 3.0 版本基于 JDK8，提供了 `lambda` 形式的调用，所以安装集成 MP3.0 要求如下：

- JDK 8+
- Maven or Gradle

JDK7 以及下的请参考 MP2.0 版本
地址：https://baomidou.gitee.io/mybatis-plus-doc/#/

#### Release

**引入 `MyBatis-Plus` 之后请不要再次引入 `MyBatis` 以及 `MyBatis-Spring`，以避免因版本差异导致的问题。**

##### Spring Boot

```java
// Maven
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.1</version>
</dependency>
  
// Gradle
compile group: 'com.baomidou', name: 'mybatis-plus-boot-starter', version: '3.4.1'
```

##### Spring MVC

```java
// Maven
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus</artifactId>
    <version>3.4.1</version>
</dependency>
  
// Gradle
compile group: 'com.baomidou', name: 'mybatis-plus', 
version: '3.4.1'
```

#### Snapshot

快照 SNAPSHOT 版本需要添加仓库，且版本号为快照版本
查看最新快照版本地址：https://oss.sonatype.org/content/repositories/snapshots/com/baomidou/mybatis-plus-boot-starter/

```java
// Maven
<repository>
 <id>snapshots</id>
 <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
</repository>
  
// Gradle
repositories {
 maven { url 
 'https://oss.sonatype.org/content/repositories/snapshots/' }
}
```

### 配置

Spring Boot 工程：

```java
@SpringBootApplication
// 配置 MapperScan 注解，扫描 Mapper 文件夹
@MapperScan("com.baomidou.mybatisplus.samples.quickstart.mapper")
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

Spring MVC 工程：

```java
// 配置 MapperScan 
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" 
     value="com.baomidou.mybatisplus.samples.quickstart.mapper"/>
</bean>
  
// 调整 SqlSessionFactory 为 MyBatis-Plus 的 SqlSessionFactory
<bean id="sqlSessionFactory" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

### 条件构造器

#### allEq 全部=

全部 eq 或者个别 isNull

```java
allEq(Map<R, V> params)
allEq(Map<R, V> params, boolean null2IsNull)
allEq(boolean condition, Map<R, V> params, boolean null2IsNull)
  
// 个别参数说明:
// params : key为数据库字段名,value为字段值
// null2IsNull : 为true则在map的value为null时调用 isNull 方法,为false
// 时则忽略value为null的
 
//例1: allEq({id:1,name:"老王",age:null})
--->id = 1 and name = '老王' and age is null
//例2: allEq({id:1,name:"老王",age:null}, false)
--->id = 1 and name = '老王'
  
allEq(BiPredicate<R, V> filter, Map<R, V> params)
allEq(BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull)
allEq(boolean condition, BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull) 
  
// 个别参数说明:
// filter : 过滤函数,是否允许字段传入比对条件中
// params 与 null2IsNull : 同上

// 例1: allEq((k,v) -> k.indexOf("a") >= 0, {id:1,name:"老王",age:null})
--->name = '老王' and age is null
// 例2: allEq((k,v) -> k.indexOf("a") >= 0, {id:1,name:"老王",age:null}, false)
--->name = '老王'
```

#### eq 等于=

```java
// 等于 =
eq(R column, Object val)
eq(boolean condition, R column, Object val)
// 例: eq("name", "老王")--->name = '老王'
```

#### ne 不等于<>

```java
// 不等于 <> 、!=
ne(R column, Object val)
ne(boolean condition, R column, Object val)
// 例: ne("name", "老王")--->name <> '老王'
```

#### gt 大于>

```java
gt(R column, Object val)
gt(boolean condition, R column, Object val)
// gt("age", 18)--->age > 18
```

#### ge 大于等于>=

```java
ge(R column, Object val)
ge(boolean condition, R column, Object val)
// ge("age", 18)--->age >= 18
```

#### lt 小于<

```java
lt(R column, Object val)
lt(boolean condition, R column, Object val)
// lt("age", 18)--->age < 18
```

#### le 小于等于<=

```java
le(R column, Object val)
le(boolean condition, R column, Object val)
// le("age", 18)--->age <= 18
```

#### between 两值之间

```java
between(R column, Object val1, Object val2)
between(boolean condition, R column, Object val1, Object val2)
// between("age", 18, 30) --> age between 18 and 30
```

#### notBetween 不在两值之间

```java
notBetween(R column, Object val1, Object val2)
notBetween(boolean condition, R column, Object val1, Object val2)
// notBetween("age", 18, 30) --> age not between 18 and 30
```

#### like '%值%'

```java
like(R column, Object val)
like(boolean condition, R column, Object val)
// like("name", "王") --> name like '%王%'
```

#### notLike

```java
notLike(R column, Object val)
notLike(boolean condition, R column, Object val)
// notLike("name", "王") --> name not like '%王%'
```

#### likeLeft '%值'

```java
likeLeft(R column, Object val)
likeLeft(boolean condition, R column, Object val)
// likeLeft("name", "王") --> name like '%王'
```

#### likeRight '值%'

```java
likeRight(R column, Object val)
likeRight(boolean condition, R column, Object val)
// likeRight("name", "王") --> name like '王%'
```

#### isNull

```java
isNull(R column)
isNull(boolean condition, R column)
// isNull("name") --> name is null
```

#### isNotNull

```java
isNotNull(R column)
isNotNull(boolean condition, R column)
// isNotNull("name") --> name is not null
```

#### in

```java
in(R column, Collection<?> value)
in(boolean condition, R column, Collection<?> value)
// in("age",{1,2,3}) --> age in (1,2,3)

in(R column, Object... values)
in(boolean condition, R column, Object... values)
// in("age", 1, 2, 3) --> age in (1,2,3)
```

#### notIn

```java
notIn(R column, Collection<?> value)
notIn(boolean condition, R column, Collection<?> value)
// notIn("age",{1,2,3}) --> age not in (1,2,3)

notIn(R column, Object... values)
notIn(boolean condition, R column, Object... values)
// notIn("age", 1, 2, 3) --> age not in (1,2,3)
```

#### inSql

```java
// 字段 IN ( sql语句 )
inSql(R column, String inValue)
inSql(boolean condition, R column, String inValue)
// inSql("age", "1,2,3,4,5,6") --> age in (1,2,3,4,5,6)
// inSql("id", "select id from table where id < 3")
--->id in (select id from table where id < 3)
```

#### notInSql

```java
// 字段 NOT IN ( sql语句 )
notInSql(R column, String inValue)
notInSql(boolean condition, R column, String inValue)
// notInSql("age", "1,2,3,4,5,6")--->age not in (1,2,3,4,5,6)
// notInSql("id", "select id from table where id < 3")
--->id not in (select id from table where id < 3)
```

#### groupBy

```java
// 分组：GROUP BY 字段
groupBy(R... columns)
groupBy(boolean condition, R... columns)
// groupBy("id", "name") --> group by id,name
```

#### orderBy

```java
// 排序，isAsc 默认 true 顺序，赋值false 倒序
orderBy(boolean condition, boolean isAsc, R... columns)
// orderBy(true, true, "id", "name") --> order by id ASC,name ASC
```

#### orderByAsc 或 

```java
// 排序，顺序 小->大
orderByAsc(R... columns)
orderByAsc(boolean condition, R... columns)
// orderByAsc("id", "name") --> order by id ASC,name ASC
```

#### orderByDesc

```java
// 排序，顺序 大->小
orderByDesc(R... columns)
orderByDesc(boolean condition, R... columns)
// orderByDesc("id", "name") --> order by id DESC,name DESC
```

#### having

```java
// HAVING ( sql语句 )
having(String sqlHaving, Object... params)
having(boolean condition, String sqlHaving, Object... params)
// having("sum(age) > 10") --> having sum(age) > 10
// having("sum(age) > {0}", 11) --> having sum(age) > 11
```

####  func

```java
// func 方法(主要方便在出现if...else下调用不同方法能不断链)
func(Consumer<Children> consumer)
func(boolean condition, Consumer<Children> consumer)
// func(i -> if(true) {i.eq("id", 1)} else {i.ne("id", 1)})
```

#### or

```java
or()
or(boolean condition)
// 注意：
// 主动调用or表示紧接着下一个方法不是用and连接!
// (不调用or则默认为使用and连接)
// eq("id",1).or().eq("name","老王") --> id = 1 or name = '老王'
  
// OR 嵌套
or(Consumer<Param> consumer)
or(boolean condition, Consumer<Param> consumer)
// or(i -> i.eq("name", "李白").ne("status", "活着"))
  --> or (name = '李白' and status <> '活着')
```

#### and

```java
// AND 嵌套
and(Consumer<Param> consumer)
and(boolean condition, Consumer<Param> consumer)
// and(i -> i.eq("name", "李白").ne("status", "活着"))
--> and (name = '李白' and status <> '活着')
```

#### nested

```java
// 正常嵌套 不带 AND 或者 OR
nested(Consumer<Param> consumer)
nested(boolean condition, Consumer<Param> consumer)
// nested(i -> i.eq("name", "李白").ne("status", "活着"))
--> (name = '李白' and status <> '活着')
```

#### apply 拼接sql

```java
// 该方法可用于数据库函数 动态入参的params对应前面applySql内部的{index}部分.这样是不会有sql注入风险的,反之会有!
apply(String applySql, Object... params)
apply(boolean condition, String applySql, Object... params)
// 例: apply("id = 1")--->id = 1
// 例: apply("date_format(dateColumn,'%Y-%m-%d') = '2008-08-08'")
  --->date_format(dateColumn,'%Y-%m-%d') = '2008-08-08'")
// 例: apply("date_format(dateColumn,'%Y-%m-%d') = {0}", "2008-08-08")
  --->date_format(dateColumn,'%Y-%m-%d') = '2008-08-08'")
```

#### last

```java
// 无视优化规则直接拼接到 sql 的最后
// 只能调用一次,多次调用以最后一次为准 有sql注入的风险,请谨慎使用
last(String lastSql)
last(boolean condition, String lastSql)
// last("limit 1")
```

#### exists

```java
// 拼接 EXISTS ( sql语句 )
exists(String existsSql)
exists(boolean condition, String existsSql)
// exists("select id from table where age = 1")
--> exists (select id from table where age = 1)
```

#### notExists

```java
// 拼接 NOT EXISTS ( sql语句 )
notExists(String notExistsSql)
notExists(boolean condition, String notExistsSql)
// notExists("select id from table where age = 1")
--> not exists (select id from table where age = 1)
```

