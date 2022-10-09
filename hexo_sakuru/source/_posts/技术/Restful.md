---
title: Restful风格
author: Yukino
avatar: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2020-10-19 21:00:00
comments: true #是否开启评论
tags: #文章标签
  - Spring MVC
keywords: Restful风格 #这个暂时没找到用户
description: Restful风格 #首页文章简介
photos: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/banner/1.jpg #文章详情页的banner
---
# 0. 基础介绍 
**Restful开发规范**
- 使用URL作为用户交互入口
- 明确的语义规范(GET|POST|PUT|DELETE)
    - GET 查询
    - POST 新增
    - PUT 修改
    - DELETE 删除
    
**Refstful命名要求**

![image](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/20201020014032.png)

# 1.环境准备
```Xml
<!--web.xml-->
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>0</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

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
</web-app>
```
```Xml
<!--applicationContext.xml-->
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
    <context:component-scan base-package="com.imooc.restful"/>
    <mvc:annotation-driven>
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <!-- response.setContentType("text/html;charset=utf-8") -->
                        <value>text/html;charset=utf-8</value>
                        <value>application/json;charset=utf-8</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
    <mvc:default-servlet-handler/>
</beans>
```

# 2.Restful 注解
- @RestController - 使用在类上，替代每个@ResponseBody，代表是restful数据而不是页面跳转
- @PathVariable - 路径变量注解
- @GetMapping @PostMapping -对应请求注解

```Html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>RESTful实验室</title>
    <script src="jquery-3.3.1.min.js"></script>
    <script>
        $(function(){
            $("#btnGet").click(function () {
                $.ajax({
                    url : "/restful/request",
                    type : "get" ,
                    dataType : "json" ,
                    success : function(json){
                        $("#message").text(json.message);
                    }
                })
            });
        })

        $(function(){
            $("#btnPost").click(function () {
                $.ajax({
                    url : "/restful/request/100",//路径变量100
                    type : "post" ,
                    data : "name=lily&age=23",//数据
                    dataType : "json" ,
                    success : function(json){
                        $("#message").text(json.message+":"+json.id);
                    }
                })
            });
        })

        $(function(){
            $("#btnPut").click(function () {
                $.ajax({
                    url : "/restful/request",
                    type : "put" ,
                    data : "name=lily&age=23",
                    dataType : "json" ,
                    success : function(json){
                        $("#message").text(json.message);
                    }
                })
            });
        })

        $(function(){
            $("#btnDelete").click(function () {
                $.ajax({
                    url : "/restful/request",
                    type : "delete" ,
                    dataType : "json" ,
                    success : function(json){
                        $("#message").text(json.message);
                    }
                })
            });
        })

        $(function () {
            $("#btnPersons").click(function () {
                $.ajax({
                    url : "/restful/persons",
                    type : "get",
                    dataType : "json",
                    success : function(json){
                        console.info(json);
                        for(var i = 0 ; i < json.length;i++){
                            var p = json[i];
                            $("#divPersons").append("<h2>" + p.name + "-" + p.age + "-" + p.birthday+ "</h2>")
                        }
                    }
                })
            })
        })
    </script>
</head>
<body>
    <input type="button" id="btnGet" value="发送Get请求">
    <input type="button" id="btnPost" value="发送Post请求">
    <input type="button" id="btnPut" value="发送Put请求">
    <input type="button" id="btnDelete" value="发送Delete请求">
    <h1 id="message"></h1>
    <hr/>
    <input type="button" id="btnPersons" value="查询所有人员">
    <div id="divPersons"></div>
</body>
</html>
```
![image](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/20201020014120.png)

```Java
@RestController
//替代每个@ResponseBody，代表是restful数据而不是页面跳转
@RequestMapping("/restful")

public class RestfulController {
    @GetMapping("/request")
    //@ResponseBody
    public String doGetRequest(){
        return "{\"message\":\"返回查询结果\"}";
    }

    // POST /article/1
    // POST /restful/request/100
    // 获取路径变量
    @PostMapping("/request/{rid}")
    //@ResponseBody
    public String doPostRequest(@PathVariable("rid") Integer requestId, Person person){
        System.out.println(person.getName() + ":" + person.getAge());
        return "{\"message\":\"数据新建成功\",\"id\":" + requestId + "}";
    }

    @PutMapping("/request")
    //@ResponseBody
    public String doPutRequest(Person person){
        System.out.println(person.getName() + ":" + person.getAge());
        return "{\"message\":\"数据更新成功\"}";
    }

    @DeleteMapping("/request")
    //@ResponseBody
    public String doDeleteRequest(){
        return "{\"message\":\"数据删除成功\"}";
    }
}
```

# 3.简单请求与非简单请求
- 简单请求是指标准结构的HTTP请求,对应GET/POST请求
- 非简单请求是复杂要求的HTTP请求,指PUT/DELETE、扩展标准请求
- 两者最大区别是非简单请求发送前需要发送预检请求

**非简单请求**
![image](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/20201020014155.png)

```Html
<script>
        $(function(){
            $("#btnPost").click(function () {
                $.ajax({
                    url : "/restful/request/100",
                    type : "post" ,
                    data : "name=lily&age=23",
                    dataType : "json" ,
                    success : function(json){
                        $("#message").text(json.message+":"+json.id);
                    }
                })
            });
        })

        $(function(){
            $("#btnPut").click(function () {
                $.ajax({
                    url : "/restful/request",
                    type : "put" ,
                    data : "name=lily&age=23",
                    dataType : "json" ,
                    success : function(json){
                        $("#message").text(json.message);
                    }
                })
            });
        })
</script>
```
```Java
    @PostMapping("/request/{rid}")
    //@ResponseBody
    public String doPostRequest(@PathVariable("rid") Integer requestId, Person person){
        System.out.println(person.getName() + ":" + person.getAge());
        return "{\"message\":\"数据新建成功\",\"id\":" + requestId + "}";
    }

    @PutMapping("/request")
    //@ResponseBody
    public String doPutRequest(Person person){
        System.out.println(person.getName() + ":" + person.getAge());
        return "{\"message\":\"数据更新成功\"}";
    }
```
由于Post请求是简单请求,可以得到执行结果
- lily:23

但Put请求是非简单请求，执行结果

- null:null

为了使非简单请求，能够获取到数据。需要在web.xml增加配置
```Xml
<!--web.xml-->
    <filter>
        <filter-name>formContentFilter</filter-name>
        <filter-class>org.springframework.web.filter.FormContentFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>formContentFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

# 4.JSON序列化

```Java
public class Person {
    private String name;
    private Integer age;
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss" , timezone = "GMT+8")
    private Date birthday;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
}
```
**@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss" , timezone = "GMT+8")**

对日期进行格式化，JackSon的默认时区为格林尼治时间(GMT),北京时间为东八区。需要进行转换时区。
```Java
    @GetMapping("/person")
    public Person findByPersonId(Integer id){
        Person p = new Person();
        if(id==1){
            p.setName("lily");
            p.setAge(23);
        }else if(id==2){
            p.setName("smith");
            p.setAge(22);
        }
        return p;
    }

    @GetMapping("/persons")
    public List<Person> findPersons(){
        List list = new ArrayList();
        Person p1 = new Person();
        p1.setName("lily");
        p1.setAge(23);
        p1.setBirthday(new Date());

        Person p2 = new Person();
        p2.setName("smith");
        p2.setAge(22);
        p2.setBirthday(new Date());
        list.add(p1);
        list.add(p2);
        return list;
    }
```
```Html
    <script>
        $(function () {
            $("#btnPersons").click(function () {
                $.ajax({
                    url : "/restful/persons",
                    type : "get",
                    dataType : "json",
                    success : function(json){
                        console.info(json);
                        for(var i = 0 ; i < json.length;i++){
                            var p = json[i];
                            $("#divPersons").append("<h2>" + p.name + "-" + p.age + "-" + p.birthday+ "</h2>")
                        }
                    }
                })
            })
        })
    </script>
<body>
    <h1 id="message"></h1>
    <hr/>
    <input type="button" id="btnPersons" value="查询所有人员">
    <div id="divPersons"></div>
</body>
```

# 5.跨域请求
## 5.1 跨域基础知识
**浏览器同源策略**

- 同源策略阻止一个域加载的脚本去获取另一个域上的资源
- 只要协议、域名、端口有一个不同，都被当作是不同的域
- 浏览器Console看到 **Access-Control-Allow-Origin**就代表跨域了

**同源策略示例**
![image](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/20201020014231.png)

**HTML中允许跨域的标签**
- `<img>` - 显示远程图片
- `<script>` - 加载远程JS
- `<link>` - 加载远程CSS

**个人经历**

年少时玩爬虫的时候，目标是直接爬取B站的数据，直接显示在自己的界面。B站有一个服务器，是restful风格提供数据。通过前端直接去B站服务器的Json数据。结果发现一直是无法成功获取。这个问题就是同源策略。后面还是只有通过后端语音拿到数据再传到前端瞬间实现。

**CORS跨域资源访问**
- CORS是一种机制,使用额外的HTTP头通知浏览器可以访问其他域，其实是浏览器发现是跨域，所以进行拦截了。
- URL响应头包含 Access-Control-*指明请求允许跨域，浏览器发现带有这个response头，就放行了。
- 跨域访问，在Request Headers中，有字段`Sec-Fetch-Mode: cors`
## 5.2 Spring MVC解决跨域访问
- @CrossOrigin－ Controller跨域注解
- <mvc:cors> - Spring MVC全局跨域配置

1. **@CrossOrigin**
```Java
@RestController//替代每个@ResponseBody，代表是restful数据而不是数据跳转
@RequestMapping("/restful")
//@CrossOrigin(origins = {"http://localhost:8080","http://www.imooc.com"})
//@CrossOrigin(origins = "*",maxAge = 3600)
public class RestfulController {
    
}
```
在Controller类上添加注解

`@CrossOrigin(origins = {"http://localhost:8080","http://www.imooc.com"})`
`@CrossOrigin(origins = "*",maxAge = 3600)`
maxAge的意思是浏览器的非简单请求在实际请求之前，会先发送预检请求。
maxAge 即保存预简请求的时间，在这期间。非简单请求就不用发送预检请求了，缓解服务器的压力。

2. <mvc:cors>
```Xml
    <mvc:cors>
        <mvc:mapping path="/restful/**"
                     allowed-origins="http://localhost:8080,http://www.imooc.com"
                     max-age="3600"/>
    </mvc:cors>
```
其中 `path="/restful/**` 表示访问的目标域名为restful/ 下的所有域名

**总结**

<mvc:cors> 的优势方便配置全局跨域访问。