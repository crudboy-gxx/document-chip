<!--
 * @Descripttion: 
 * @Author: guox
 * @Date: 2020-07-28 18:40:12
 * @LastEditors: guox
-->
# mybatis中对象嵌套对象

模型中经常存在组合的形式，对应的sql查询就是多表查询。如何将查询结果映射到对象中呢？

参照官方文档 https://mybatis.org/mybatis-3/zh/sqlmap-xml.html#Result_Maps

1.比较通用的方式就是像下面这样，property映射到对象的某个属性上。

~~~xml
<result column="catalogue_id" property="catalogue.catalogueId" jdbcType="VARCHAR"/>
~~~



2.还可以可以使用<association>关联，这种关联的必须是副表，不能是主表。

~~~xml
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <association property="author" column="blog_author_id" javaType="Author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
  <id property="id" column="author_id"/>
  <result property="username" column="author_username"/>
  <result property="password" column="author_password"/>
  <result property="email" column="author_email"/>
  <result property="bio" column="author_bio"/>
</resultMap>
~~~

3.如果是集合，使用<collection>集合。

~~~xml
<resultMap id="blogResult" type="Blog">
  <id property="id" column="blog_id" />
  <result property="title" column="blog_title"/>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <result property="body" column="post_body"/>
  </collection>
</resultMap>
~~~

