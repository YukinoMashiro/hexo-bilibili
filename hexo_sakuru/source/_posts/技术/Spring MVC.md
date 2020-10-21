---
title: Spring MVC
author: Yukino
avatar: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2020-10-18 21:00:00
comments: true #是否开启评论
tags: #文章标签
  - Spring MVC
keywords: Spring MVC #这个暂时没找到用户
description: Spring MVC #首页文章简介
photos: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/banner/1.jpg #文章详情页的banner
---
# 1.环境配置
```Xml
<!--web.xml-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <!--
            DispatcherServlet是Spring MVC最核心的对象
            DispatcherServlet用于拦截Http请求,
            并根据请求的URL调用与之对应的Controller方法,来完成Http请求的处理
        -->
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--applicationContext.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <!--
            在Web应用启动时自动创建Spring IOC容器,
            并初始化DispatcherServlet
        -->
        <load-on-startup>0</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <!--"/" 代表拦截所有请求-->
        <url-pattern>/</url-pattern>
    </servlet-mapping>    
```
```Xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mv="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd
            http://www.springframework.org/schema/mvc
            http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <!--
    context:component-scan 标签作用
    在Spring IOC初始化过程中,自动创建并管理com.imooc.springmvc及子包中
    拥有以下注解的对象.
    @Repository
    @Service
    @Controller
    @Component
    -->
    <context:component-scan base-package="com.imooc.springmvc"></context:component-scan>
    
    <!--1.启用Spring MVC的注解开发模式-->
    <mvc:annotation-driven conversion-service="conversionService">    </mvc:annotation-driven>
    <!-- 将图片/JS/CSS等静态资源排除在外,可提高执行效率 -->
    <mvc:default-servlet-handler/>
```

# 2.URL Mapping注解
**作用**
URL Mapping将URL与Controller方法进行绑定

- RequestMapping - 通用绑定
- GetMaping - 绑定Get请求
- PostMapping - 绑定Post请求

前端Post 请求
```Html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/um/p1" method="post">
        <input name="username"> <br>
        <input name="password"> <br>
        <input name="createTime"> <br>
        <input type="submit" value="提交">
    </form>
</body>
</html>
```
```Java
@Controller
@RequestMapping("/um") //为所有类的映射都添加一个前缀
public class URLMappingController {
    @GetMapping("/g")
    @ResponseBody
    //manager_name参数映射到String managerName
    //Date createTime 是使用MyDateConverter类在applicationContext.xml配置进行日期转换的
    public String getMapping(@RequestParam("manager_name") String managerName,Date createTime){
        System.out.println("managerName:" + managerName);
        System.out.println(createTime);
        return "This is get method";
    }

    @PostMapping("/p")
    @ResponseBody
    public String postMapping(String username, Long password) {
        System.out.println(username + ":" + password);
        return "This is post method";
    }

    @PostMapping("/p1")
    @ResponseBody
    //1.可以用对象User进行接收Post参数，其中createTime字段，在User类中，需要使用DateTimeFormat
    //2.单独接收createTime 时，也可以形式参数中@DateTimeFormat，指明格式
    //3.String username也可以接收到数据
    public String postMapping(User user, String username, @DateTimeFormat(pattern = "yyyy-MM-dd") Date createTime) {
        System.out.println(user.getUsername() + ":" + user.getPassword());
        System.out.println("date:" + user.getCreateTime());
        return "<h1>这是Post响应</h1>";
    }
}
```
```Java
public class User {
    private String username;
    private Long password;
    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private Date createTime;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Long getPassword() {
        return password;
    }

    public void setPassword(Long password) {
        this.password = password;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }
}
```

# 3.接收表单复杂数据
```Html
<div class="container">
    <h2>学员调查问卷</h2>
    <form action="./apply" method="post">
        <h3>您的姓名</h3>
        <input name="name" class="text"  style="width: 150px">
        <h3>您正在学习的技术方向</h3>
        <select name="course" style="width: 150px">
            <option value="java">Java</option>
            <option value="h5">HTML5</option>
            <option value="python">Python</option>
            <option value="php">PHP</option>
        </select>
        <div>
            <h3>您的学习目的：</h3>
            <input type="checkbox" name="purpose" value="1">就业找工作
            <input type="checkbox" name="purpose" value="2">工作要求
            <input type="checkbox" name="purpose" value="3">兴趣爱好
            <input type="checkbox" name="purpose" value="4">其他
        </div>
        <div style="text-align: center;padding-top:10px" >
            <input type="submit" value="提交" style="width:100px">
        </div>
    </form>

</div>
```
![image](https://cdn.jsdelivr.net/gh/Yukino831143/CDN/blogImageHosting/20201020013601.png)

```Java
@Controller
public class FormContoller {
    @PostMapping("/apply")
    @ResponseBody
    //1.@RequestParam 指定接收到的参数为n,默认值为"ANON",可避免无法匹配到参数时，出现的异常
    //2.复选框使用数组Integer[] purpose，接收
    public String apply(@RequestParam(value = "n", defaultValue = "ANON") String name, String course, Integer[] purpose) {
        System.out.println("name:" + name);
        System.out.println("course:" + course);
        for(Integer p : purpose){
            System.out.println("purpose:" + p);
        }
        return "SUCCES";
    }

//    @PostMapping("/apply")
    @ResponseBody
    //@RequestParam表示将接收到的数据转换成List，一定要加
    public String apply(String name, String course, @RequestParam List<Integer> purpose){
        System.out.println("name:" + name);
        System.out.println("course:" + course);
        for(Integer p : purpose){
            System.out.println("purpose:" + p);
        }
        return "SUCCES";
    }

//    @PostMapping("/apply")
    @ResponseBody
    //以对象方式接收参数
    public String  apply(Form form) {
        System.out.println(form.getName());
        System.out.println(form.getCourse());
        for(Integer p : form.getPurpose()) {
            System.out.println(p);
        }
        return "SUCCESS";
    }
//    @PostMapping("/apply")
    @ResponseBody
    //注意Map类型最好不要用因为Map类型对于传过来的purpose参数，对于复杂类型，如复选框，只会取第一个值。漏掉数据
    public String apply(@RequestParam Map map){
        System.out.println(map);
        return "SUCCESS";
    }
}
```
```Java
public class Form {
    private String name;
    private String course;
    private List<Integer> purpose;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCourse() {
        return course;
    }

    public void setCourse(String course) {
        this.course = course;
    }

    public List<Integer> getPurpose() {
        return purpose;
    }

    public void setPurpose(List<Integer> purpose) {
        this.purpose = purpose;
    }
}
```

# 4.关联对象赋值
```Html
    <h2>学员调查问卷</h2>
    <form action="./apply" method="post">
        <h3>您的姓名</h3>
        <input name="name" class="text"  style="width: 150px">
        <h3>您正在学习的技术方向</h3>
        <select name="course" style="width: 150px">
            <option value="java">Java</option>
            <option value="h5">HTML5</option>
            <option value="python">Python</option>
            <option value="php">PHP</option>
        </select>
        <div>
            <h3>您的学习目的：</h3>
            <input type="checkbox" name="purpose" value="1">就业找工作
            <input type="checkbox" name="purpose" value="2">工作要求
            <input type="checkbox" name="purpose" value="3">兴趣爱好
            <input type="checkbox" name="purpose" value="4">其他
        </div>
        <h3>收货人</h3>
        <input name="delivery.name" class="text" style="width: 150px">
        <h3>联系电话</h3>
        <input name="delivery.mobile" class="text" style="width: 150px">
        <h3>收货地址</h3>
        <input name="delivery.address" class="text" style="width: 150px">

        <div style="text-align: center;padding-top:10px" >
            <input type="submit" value="提交" style="width:100px">
        </div>
    </form>
```
- delivery.name
- delivery.mobile
- delivery.address

![image](https://cdn.jsdelivr.net/gh/Yukino831143/CDN/blogImageHosting/20201020013648.png)

```Java
public class Delivery {
    private String name;
    private String address;
    private String mobile;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public String getMobile() {
        return mobile;
    }

    public void setMobile(String mobile) {
        this.mobile = mobile;
    }
}
```
```Java
public class Form {
    private String name;
    private String course;
    private List<Integer> purpose;
    private Delivery delivery = new Delivery();//关联对象

    public Delivery getDelivery() {
        return delivery;
    }

    public void setDelivery(Delivery delivery) {
        this.delivery = delivery;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCourse() {
        return course;
    }

    public void setCourse(String course) {
        this.course = course;
    }

    public List<Integer> getPurpose() {
        return purpose;
    }

    public void setPurpose(List<Integer> purpose) {
        this.purpose = purpose;
    }
}
```
```Java
    @PostMapping("/apply")
    @ResponseBody
    //注意Map类型最好不要用因为Map类型对于传过来的purpose参数，只会取第一个
    public String applyDelivery(Form form){
        System.out.println(form.getDelivery().getName());
        return "SUCCESS";
    }
```
# 5.日期转换
- @DateTimeFormat
指定格式，将String 类型转换称日期类型
```Java
    @PostMapping("/p1")
    @ResponseBody
    //1.可以用对象User进行接收Post参数，其中createTime字段，在User类中，需要使用DateTimeFormat
    //2.单独接收createTime 时，也可以形式参数中@DateTimeFormat，指明格式
    public String postMapping(User user, String username, @DateTimeFormat(pattern = "yyyy-MM-dd") Date createTime) {
        System.out.println(user.getUsername() + ":" + user.getPassword());
        System.out.println("date:" + user.getCreateTime());
        return "<h1>这是Post响应</h1>";
    }
```
- 对象成员 注解
```Java
public class User {
    private String username;
    private Long password;
    //@DateTimeFormat(pattern = "yyyy-MM-dd")
    private Date createTime;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Long getPassword() {
        return password;
    }

    public void setPassword(Long password) {
        this.password = password;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }
}
```
```Java
    public String postMapping(User user, String username, @DateTimeFormat(pattern = "yyyy-MM-dd") Date createTime) {
        System.out.println(user.getUsername() + ":" + user.getPassword());
        System.out.println("date:" + user.getCreateTime());
        return "<h1>这是Post响应</h1>";
    }
```
User 对象的成员变量自动转换
- 自定义日期转换类

```Java
//日期转换
public class MyDateConverter implements Converter<String, Date> {
    public Date convert(String s) {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
        try {
            Date d = sdf.parse(s);
            return d;
        } catch (ParseException e) {
            return null;
        }
    }
}
```
```Xml
<!--applicationContext.xml-->
    <mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>
    <bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <!--指定日期转换类-->
                <bean class="com.imooc.springmvc.converter.MyDateConverter"/>
            </set>
        </property>
    </bean>
```
```Java
    @GetMapping("/g")
    @ResponseBody
    //manager_name参数映射到String managerName
    //Date createTime 是使用MyDateConverter类在applicationContext.xml配置进行日期转换的
    public String getMapping(@RequestParam("manager_name") String managerName,Date createTime){
        System.out.println("managerName:" + managerName);
        System.out.println(createTime);
        return "This is get method";
    }
```
Date createTime 没有加注解，也可以转换

注意：注解与自定义日期转换类同时存在时，以日期转换类优先

# 6.中文乱码问题
- Get乱码问题 - server.xml增加URIEncoding属性
- Post请求乱码 - Web.xml 配置 CharacterEncodingFilter
- Response响应乱码 - spring配置StringHttpMessageConverter

## 6.1 get
Path:"Tomcat v8.5 Server at localhost-config\server.xml"
```Xml
<Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443" URIEncoding="UTF-8"/>
```
在tomcat8 以下的版本需要增加URIEncoding，以上版本默认是UTF-8

## 6.2 post
通过过滤器来实现post编码
```Xml
<!--web.xml-->
    <filter>
        <filter-name>characterFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```
## 6.3 Response
```Xml
<!--applicationContext.xml-->
    <!--3.message-converters 配置Response响应字符集-->
    <mvc:annotation-driven conversion-service="conversionService">
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <!-- response.setContentType("text/html;charset=utf-8") -->
                        <value>text/plain;charset=utf-8</value>
                        <value>text/html;charset=utf-8</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

# 7.响应输出
## 7.1 ModelAndView




