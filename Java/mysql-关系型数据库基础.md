mysql-关系型数据库(基础)

启动：net start mysql
关闭：net stop mysql

终端连接mysql：mysql -uroot -p123456

mysql默认端口：3306，一般不需要修改

SQL：结构化查询语言



DCL：数据库控制语言--数据库用户管理权限

```mysql
-- 一个项目创建一个用户！一个项目对应数据库只有一个！
-- 非root用户只能对自己的数据库又权限，对其他数据库无权限操作

-- 创建用户
-- 创建指定IP用户
mysql> $ CREATE USER 用户名@IP地址 IDENTIFIED BY '密码';
eg: CREATE USER user1@localhost IDENTIFIED BY '123456';
-- 创建不指定IP用户，任意能访问到IP都能登录 mysql
CREATE USER 用户名@'%' IDENTIFIED BY '密码';
eg: CREATE USER user2@'%' IDENTIFIED BY '123456';

-- 给用户授权 mydb_test(案例库)
> GRANT 权限1，权限2，权限3，... ON 数据库.* TO 用户名@IP地址
eg: GRANT INSERT,UPDATE,SELECT ON mydb_test.* TO user1@localhost;
-- 给用户分派指定数据库上的所有权限
GRANT ALL ON mydb_test.* TO user1@localhost;

-- 撤销授权
REVOKE 权限1，...，权限n ON 数据库.* TO 用户名@IP地址
eg: REVOKE INSERT,UPDATE,DROP ON mydb_test.* TO user1@localhost;
-- 以上 user1 的 INSERT,UPDATE,DROP 权限

-- 查看用户操作权限
SHOW GRANTS FOR 用户名@IP地址
eg: SHOW GRANTS FOR user1@localhost;

-- 删除用户
DROP USER 用户名@IP地址

```

mysql> $show database: 显示所有数据库名称

```mysql
-- 显示数据库所有表
SHOW  TABLES;

create table tb_tableName (
  id int,
  title varchar(100),
  info varchar(255)
);

-- 修改标准：ALTER TABLE 表名
ALTER TABLE tb_tableName;
-- 增加列
ALTER TABLE tb_tableName ADD (
  cityId int,
  cityName varchar(50)
);

-- 修改列类型（修改列如果存在数据，新类型可能会影响到已存数据）
-- ALTER TABLE tb_tableName MODIFY 列名 需要修改类型;
ALTER TABLE tb_tableName MODIFY cityId varchar(32);

-- 修改列名 ALTER TABLE tb_tableName CHANGE 原列名 新列名 列类型
ALTER TABLE tb_tableName CHANGE cityName cName varchar(50);

-- 删除列 ALTER TABLE tb_tableName DROP 删除列列名
ALTER TABLE tb_tableName DROP cityName；

-- 修改表名
ALTER TABLE tb_tableName RENAME TO TB_MSGINFO;

```

增删改查DML:

```mysql
-- 查看表结构
desc tb_name;
-- 查询创建表语法
show create tables tb_name;

-- 查询语句
SELECT * FROM tb_name ( WHERE ...);

-- 插入语句
INSERT INTO tb_name (列名1， 列表名2，...) VALUES (列值1，列值2，...);
INSERT INTO tb_name (id, title, info) 
VALUES 
(1, 'id=1', 'info1'),
(2, 'id=2', 'info2');

-- 修改语句
UPDATE tb_name SET 列名1=列值1，列名2=列值2,... WHERE ...
UPDATE tb_name SET title='列值1'，info='列值2' WHERE id = 1;

-- 删除语句 (没事别瞎用)
DELETE FROM tb_name WHERE ... 

-- 以下两个语句效果一致
select * from tb_name where age >= 18 and age <= 30;
select * from tb_name where age between 18 and 30;
```

基础查询语句DQL：

```mysql
-- 列 可以直接进行加减乘除
select sal * 1.5 from tb_emp;
-- 如果是NULL 用其他值替换，别名 as 可以省略
select IFNULL(sal, 0) + 1000 as sale from tb_emp;
-- CONCAT 连接值
select CONCAT('$',sal,"&") as sale from tb_emp;
-- "$值(sal)&"

-- 模糊查询 valuer% 右模糊，%valuer 左模糊，%valuer% 左右模糊，
select * from where title like '%提%';

-- 排序 order by 列
select * from ORDER BY 列 ASC(升序);
select * from ORDER BY 列 DESC(降序);

-- count(*)统计，sum(sal)求和，max(sal)最大，min(sal)最小，avg(sal)平均

-- 分组 group by , 分组后加条件判断 -> group by 字段 having 条件
-- sql 执行顺序
select
from
where
group by
having
order by


-- 内连接
select e.name, e.title, d.dName 
from emp e INNER JOIN dept d 
ON e.deId = d.deId;

-- 外连接（有主从之分，left 左表为主表, 查询主表所有数据与之从表匹配 -- 即查询出来数据多少行 根据主表条件得到，无论是否满足关联条件，如果从表不满足条件使用NULL补位）
select e.name, e.title, d.dName 
from emp e LEFT OUTER JOIN dept d 
ON e.deId = d.deId;

-- 右外连接
select e.name, e.title, d.dName 
from emp e RIGHT OUTER JOIN dept d 
ON e.deId = d.deId;

-- 合并结果集（全外连接，并集）
select e.name, e.title, d.dName 
from emp e LEFT OUTER JOIN dept d 
ON e.deId = d.deId
UNION
select e.name, e.title, d.dName 
from emp e RIGHT OUTER JOIN dept d 
ON e.deId = d.deId;

-- 全外连接 mysql不支持以下语法
select e.name, e.title, d.dName 
from emp e FULL OUTER JOIN dept d 
ON e.deId = d.deId;

-- 子查询
select *from emp where sal = (select max(sal) from emp);

-- 二次查询，对查询结果再次查询
SELECT e.id, e.name, e.title
FROM (SELECT * FROM emp WHERE dep = 30) e
WHERE (条件)

```

