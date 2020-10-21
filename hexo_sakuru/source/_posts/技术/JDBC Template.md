---
title: JDBC Template
author: Yukino
avatar: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2020-10-17 14:00:00
comments: true #是否开启评论
tags: #文章标签
  - spring
keywords: JDBC Template #这个暂时没找到用户
description: JDBC Template #首页文章简介
photos: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/banner/1.jpg #文章详情页的banner
---

# 1.JDBC Template简介

Spring对数据库的操作在jdbc上面做了深层次的封装，使用spring的注入功能，可以把DataSource注册到JdbcTemplate之中。

# 2.code

**spring.xml**
```Xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop.xsd
    http://www.springframework.org/schema/tx
    http://www.springframework.org/schema/tx/spring-tx.xsd">
    
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/selection_course?useUnicode=true&amp;characterEncoding=utf-8"/>
        <property name="username" value="root"/>
        <property name="password" value="foreverlove"/>
    </bean>
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--在 XML 配置文件中启动 Spring 的自动扫描功能
    我们就不再需要在 XML 中显式使用 <bean/> 进行Bean 的配置。
    Spring 在容器初始化时将自动扫描 com.imooc.sc 指定的包及其子包下的所有 class文件，
    所有标注了 @Repository 的类都将被注册为 Spring Bean。
    -->
    <context:component-scan base-package="com.imooc.sc"/>
</beans>
```
**基本操作手法**
```Java
public class Test {
    private JdbcTemplate jdbcTemplate;
    {
        ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
        jdbcTemplate = (JdbcTemplate)context.getBean("jdbcTemplate");
    }
//create
    @org.junit.Test
    public void testExecute(){
        jdbcTemplate.execute("create table user1(id int,name varchar(20))");
    }
//insert 
    @org.junit.Test
    public void testUpdate(){
        String sql = "insert into student(name,sex) values(?,?)";
        jdbcTemplate.update(sql,new Object[]{"张飞","男"});
    }
//update
    @org.junit.Test
    public void testUpdate2(){
        String sql = "update student set sex=? where id=?";
        jdbcTemplate.update(sql,"女",1);
    }
//批量更新
    @org.junit.Test
    public void testBatchUpdate(){
        String[] sqls={
                "insert into student(name,sex) values('关羽','女')",
                "insert into student(name,sex) values('刘备','男')",
                "update student set sex='女' where id=1"
        };
        jdbcTemplate.batchUpdate(sqls);
    }
//批量更新
    public void testBatchUpdate2(){
        String sql = "insert into selection(student,course) values(?,?)";
        List<Object[]> list = new ArrayList<Object[]>();
        list.add(new Object[]{1005,1001});
        list.add(new Object[]{1005,1003});
        jdbcTemplate.batchUpdate(sql,list);
    }
//查询
    @org.junit.Test
    public void testQuerySimple1(){
        String sql = "select count(*) from student";
        int count = jdbcTemplate.queryForObject(sql,Integer.class);
        System.out.println(count);
    }
    @org.junit.Test
    public void testQuerySimple2(){
        String sql = "select name from student where sex=?";
        List<String> names = jdbcTemplate.queryForList(sql,String.class,"女");
        System.out.println(names);
    }
//查询结果为map
    @org.junit.Test
    public void testQueryMap1(){
        String sql = "select * from student where id = ?";
        Map<String,Object> stu = jdbcTemplate.queryForMap(sql,1);
        System.out.println(stu);//{id=1, name=张飞, sex=女, born=null}
    }
    @org.junit.Test
    public void testQueryMap2(){
        String sql = "select * from student";
        List<Map<String,Object>> stus = jdbcTemplate.queryForList(sql);
        System.out.println(stus);
        //[{id=1, name=张飞, sex=女, born=null}, {id=2, name=关羽, sex=女, born=null}]
    }
//查询结果返回对象
    public void testQueryEntity1(){
        String sql = "select * from student where id = ?";
        Student stu = jdbcTemplate.queryForObject(sql, new StudentRowMapper(), 1004);
        System.out.println(stu);
    }
    @org.junit.Test
    public void testQueryEntity2(){
        String sql = "select * from student";
        List<Student> stus = jdbcTemplate.query(sql,new StudentRowMapper());
        System.out.println(stus);
    }

    private class StudentRowMapper implements RowMapper<Student>{
        public Student mapRow(ResultSet resultSet, int i) throws SQLException {
            Student stu = new Student();
            stu.setId(resultSet.getInt("id"));
            stu.setName(resultSet.getString("name"));
            stu.setSex(resultSet.getString("sex"));
            stu.setBorn(resultSet.getDate("born"));
            return stu;
        }
    }
}
```