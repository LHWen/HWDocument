## MyBatis入门使用

文档参考官网，与实际使用开发思路编写
官网地址：https://mybatis.org/mybatis-3/zh/index.html

### XML映射

##### 映射mapper文件

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="关联的mapper文件路径">
  // id 是mapper文件路径中查询SQL的方法名名称， resultType 为返回结果
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
// #{id},获取传递的参数值
// ${xxxx},写入值，即不为查询参数
```

##### insert、update、delete语句示例

```Java
<insert id="insertAuthor">
  INSERT INTO Author (id,username,password,email,bio)
  values (#{id},#{username},#{password},#{email},#{bio})
</insert>

<update id="updateAuthor">
  UPDATE Author set
    username = #{username},
    password = #{password},
    email = #{email},
    bio = #{bio}
  where id = #{id}
</update>

<delete id="deleteAuthor">
  DELETE from Author where id = #{id}
</delete>
```

##### sql

```java
1. 
<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>
  
  <select id="selectUsers" resultType="map">
    select
    <include refid="userColumns"><property name="alias" value="t1"/>		</include>,
    <include refid="userColumns"><property name="alias" value="t2"/>		</include>
  	from some_table t1
   	cross join some_table t2
	</select>
      
2.      
  <sql id="queryUserInfo">ID as id, USER_NAME AS userName, ${info} AS userInfo </sql>
   
   <select id="selectUserInfo" resultType="map">
      SELECT
      <include refid="queryUserInfo"><property name="info" value="备注信息"/></include>
      FORM TB_USER_INFO
      WHERE ISBISABLE = 0
   </select>
     
3. sql 结合使用
   <sql id="sometable">${prefix}Table</sql>
	 <sql id="someinclude"> from <include refid="${include_target}"/>
	 </sql>
  // select field1, field2, field3 from SomeTable
	<select id="select" resultType="map">
   select field1, field2, field3
	  <include refid="someinclude">
  	  <property name="prefix" value="Some"/> // SomeTable
    	<property name="include_target" value="sometable"/>
      // from SomeTable
	  </include>
	</select>

      
```

##### 参数

```Java
// #{id} 参数传递方式
// ${columnName} 字符串替换，
<select id="selectUsers" resultType="User">
  select id, username, password
  from users
  where id = #{id} 
	ORDER BY ${columnName}
</select>
```

##### 结果映射

```java
// 返回实体类 路径假设为，com.someapi.entity
package com.someapi.entity;
public class UserInfo {
	private int id;
  private String username;
  private String desc;
}
```

```java
// 1. 直接使用实体类路径关联映射返回
<select id="selectUsers" resultType="com.someapi.entity.UserInfo">
  SELECT id, username, desc FROM TB_USER_INFO WHERE id = #{id}
</select>

// 2. 使用类别名映射
<!-- mybatis-config.xml 中 -->
<typeAlias type="com.someapi.entity.UserInfo" alias="User"/>

<!-- SQL 映射 XML 中 -->
<select id="selectUsers" resultType="User">
  SELECT id, username, desc FROM TB_USER_INFO WHERE id = #{id}
</select>

// 3. 列名与属性名匹配不上，使用别名
<select id="selectUsers" resultType="com.someapi.entity.UserInfo">
  SELECT 
  USER_ID AS id, 
	USER_NAME AS username, 
	INFO AS desc 
  FROM TB_USER_INFO WHERE id = #{id}
</select> 
 
// 4. 显式使用外部的 resultMap
<resultMap id="userResultMap" type="com.someapi.entity.UserInfo">
  <id property="id" column="USER_ID" />
  <result property="username" column="USER_NAME"/>
  <result property="desc" column="INFO"/>
</resultMap>

// 这里返回结果不再使用 resultType 使用 resultMap
<select id="selectUsers" resultMap="userResultMap">
  SELECT USER_ID, USER_NAME, INFO FROM TB_USER_INFO WHERE id = #{id}
</select> 
  
  
```

### 动态SQL

动态SQL包含如下几项：

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

##### if

使用动态SQL最常见情景是根据条件包含where子句的一部分

```java
<select id="selectUsers" resultType="com.someapi.entity.UserInfo">
  SELECT id, username, desc FROM TB_USER_INFO WHERE isDisable = 0
  <if test="username != null and username != ''">
    AND username like #{username}
  </if>
</select>

<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

##### choose、when、otherwise 多条件判断

```java
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

##### trim、where、set

```java
// ---------------- where ---------------
<select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG WHERE
  <if test="state != null">
    state = #{state}
  </if>
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
 // 如果没有一个条件满足 sql：SELECT * FROM BLOG WHERE 导致查询失败
 // 使用where解决该问题, 会除去开头为 “AND” 或 “OR”
 <select id="findActiveBlogLike" resultType="Blog">
  SELECT * FROM BLOG 
  <where>
    <if test="state != null"> AND state = #{state} </if>
    <if test="title != null"> AND title like #{title} </if>
    <if test="author != null and author.name != null">
      AND author_name like #{author.name}
    </if>
  </where>
</select>
      
// ---------------- set ---------------
// set 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>

// trim 使用为where & set 等价自定义，具体使用看官方文档
```

##### foreach

```java
// 构建 IN 条件语句使用情况
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT * FROM POST P
  WHERE ID in
  <foreach item="item" index="index" collection="list" 
  open="(" separator="," close=")">
     #{item}
  </foreach>
</select>

// 构建新增语句使用情况
<insert id="insertBatch">
   <if test="saveList != null and saveList.size() > 0">
       INSERT INTO TB_SPECIAL_INCENTIVE (
       BELONG_MONTH,
       USERNAME,
       BONUS
       )
       VALUES
       <foreach collection="saveList" item="item" open="" separator="," close="">
        	(
     		 	#{item.belongMonth},
        	#{item.username},
        	#{item.bouns}
        	)
        </foreach>
    </if>
 </insert>
```

