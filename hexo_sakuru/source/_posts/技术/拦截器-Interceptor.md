---
title: 拦截器-Interceptor
author: Yukino
avatar: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2020-10-20 01:50:00
comments: true #是否开启评论
tags: #文章标签
  - Spring MVC
keywords: 拦截器-Interceptor #这个暂时没找到用户
description: 拦截器-Interceptor #首页文章简介
photos: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.staticaly.com/gh/Yukino831143/CDN@master/img/yukino/banner/1.jpg #文章详情页的banner
---
- 拦截器(Interceptor)用于对URL请求进行前置/后置过滤
- Interceptor与Filter用途相似，但实现方式不同
- Interceptor底层就是基于Spring Aop面向切面编程实现
# 1.拦截器入门
```Xml
<!--pom.xml-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
```
```Xml
<!--applicationContext.xml-->
    <mvc:interceptors>
        <mvc:interceptor>
            <bean class="com.imooc.restful.interceptor.MyInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
```
```Java
@RestController
@RequestMapping("/restful")
//@CrossOrigin(origins = {"http://localhost:8080","http://www.imooc.com"})
//@CrossOrigin(origins = "*",maxAge = 3600)
public class RestfulController {
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
        System.out.println("RestfulController.findPersons() - return list");
        return list;
    }    
}
```
```Java
public class MyInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println(request.getRequestURL() + "-准备执行");
//        response.getWriter().print("[]");
        return true;//返回false会直接拦截，不进行下一步传递
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println(request.getRequestURL() + "-目标处理成功");
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println(request.getRequestURL() + "-响应内容已产生");
    }
}
```
1. preHandle -前置执行处理
2. postHandle - 目标资源已经被Spring mvc 框架处理
3. afterCompletion - 响应文本已经产生

**打印log**

- `http://localhost:8080/restful/persons-准备执行`
- `RestfulController.findPersons() - return list`
- `http://localhost:8080/restful/persons-目标处理成功`
- `http://localhost:8080/restful/persons-响应内容已产生`

**静态资源处理**

因为按照上面的配置，当访问静态资源也会进行拦截，显然这是不合理的。
```Xml
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/restful/**"/>
            <mvc:mapping path="/webapi/**"/>
            <mvc:exclude-mapping path="/**.ico"/>
            <mvc:exclude-mapping path="/**.jpg"/>
            <mvc:exclude-mapping path="/**.gif"/>
            <mvc:exclude-mapping path="/**.js"/>
            <mvc:exclude-mapping path="/**.css"/>
            <mvc:exclude-mapping path="/resources/**"/>
            <bean class="com.imooc.restful.interceptor.MyInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
```
- 手动排除静态资源`/**.ico`
- 将静态资源放在统一目录`/resource/`
- 指定访问的链接 `/restful/**`

# 2.多拦截器问题
```Xml
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/restful/**"/>
            <mvc:mapping path="/webapi/**"/>
            <mvc:exclude-mapping path="/**.ico"/>
            <mvc:exclude-mapping path="/**.jpg"/>
            <mvc:exclude-mapping path="/**.gif"/>
            <mvc:exclude-mapping path="/**.js"/>
            <mvc:exclude-mapping path="/**.css"/>
            <mvc:exclude-mapping path="/resources/**"/>
            <bean class="com.imooc.restful.interceptor.MyInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
        <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/restful/**"/>
            <mvc:mapping path="/webapi/**"/>
            <mvc:exclude-mapping path="/**.ico"/>
            <mvc:exclude-mapping path="/**.jpg"/>
            <mvc:exclude-mapping path="/**.gif"/>
            <mvc:exclude-mapping path="/**.js"/>
            <mvc:exclude-mapping path="/**.css"/>
            <mvc:exclude-mapping path="/resources/**"/>
            <bean class="com.imooc.restful.interceptor.MyInterceptor1"/>
        </mvc:interceptor>
    </mvc:interceptors>
```
```Java
public class MyInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println(request.getRequestURL() + "-准备执行");
//        response.getWriter().print("[]");//拦截器输出到前端
        return true;//返回false会直接拦截，不进行下一步传递
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println(request.getRequestURL() + "-目标处理成功");
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println(request.getRequestURL() + "-响应内容已产生");
    }
}
```
```Java
public class MyInterceptor1 implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println(request.getRequestURL()+"-准备执行-1");
        return true;
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println(request.getRequestURL()+"-目标处理成功-1");
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println(request.getRequestURL()+"-响应内容已产生-1");
    }
}
```
**执行log**

`http://localhost:8080/restful/persons-准备执行`
`http://localhost:8080/restful/persons-准备执行-1`
`RestfulController.findPersons() - return list`
`http://localhost:8080/restful/persons-目标处理成功-1`
`http://localhost:8080/restful/persons-目标处理成功`
`http://localhost:8080/restful/persons-响应内容已产生-1`
`http://localhost:8080/restful/persons-响应内容已产生`
![image](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/20201020014438.png)

# 3.用户流量拦截器
1. 引入logback
```Xml
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>
    </dependencies>
```
2. 新建 resource/logback.xml
```
<!--logback.xml-->
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--定义向控制台输出的追加器-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>[%thread] %d %level %logger{10} - %msg%n</pattern>
        </encoder>
    </appender>
    <!--定义向log文件输出的追加器-->
    <appender name="accessHistoryLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>d:/logs/history.%d.log</fileNamePattern>
        </rollingPolicy>
        <encoder>
            <pattern>[%thread] %d %level %logger{10} - %msg%n</pattern>
        </encoder>
    </appender>
    <!--输出最低级别debug-->
    <root level="debug">
        <appender-ref ref="console"/>
    </root>
    <logger name="com.imooc.restful.interceptor.AccessHistoryInterceptor"
            level="INFO" additivity="false">
            <!--level="INFO" 默认输出等级 additivity="false" 表示不输出到console，输出到文件-->
        <appender-ref ref="accessHistoryLog"/>
    </logger>
</configuration>
```

```Xml
<!--applicationContext.xml-->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <mvc:exclude-mapping path="/resources/**"/>
            <bean class="com.imooc.restful.interceptor.AccessHistoryInterceptor"/>
        </mvc:interceptor>
    </mvc:interceptors>
```

`<pattern>[%thread] %d %level %logger{10} - %msg%n</pattern>`
- %pattern 线程名
- %d 时间
- %level 日志级别
- %logger{10} 产生类日志，限定长度10
- %msg log信息
- %n 换行

RollingFileAppender 生成按天滚动的文件。每一天一个文件。

```Java
public class AccessHistoryInterceptor implements HandlerInterceptor {
    private Logger logger = LoggerFactory.getLogger(AccessHistoryInterceptor.class);

    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        StringBuilder log = new StringBuilder();
        log.append(request.getRemoteAddr());
        log.append("|");
        log.append(request.getRequestURL());
        log.append("|");
        log.append(request.getHeader("user-agent"));
        logger.info(log.toString());
        return true;
    }
}
```
# 4.Spring MVC处理流程
![image](https://cdn.staticaly.com/gh/Yukino831143/CDN@master/blogImageHosting/20201020014523.png)
