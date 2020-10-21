---
title: Spring 事务管理
author: Yukino
avatar: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/avatar/a26.ico #头像地址
authorLink: /# #头像跳转链接
authorAbout: NULL
authorDesc: NULL
categories: 技术 #public/catagories文章目录
date: 2020-10-18 15:00:00
comments: true #是否开启评论
tags: #文章标签
  - spring
keywords: Spring 事务管理 #这个暂时没找到用户
description: Spring 事务管理 #首页文章简介
photos: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/article_cover/ #首页的文章的封面图
banner: https://cdn.jsdelivr.net/gh/Yukino831143/CDN/img/yukino/banner/1.jpg #文章详情页的banner
---
**什么是事务？**

事务一般指的是数据库事务(Database Transaction),是指一个程序执行单元，要么完全执行，要么完全不执行

**事务并发问题**
- 脏读

![image](https://cdn.jsdelivr.net/gh/Yukino831143/CDN/blogImageHosting/20201018152637.png)

解决方法：只能读取永久数据，不能读取内存中的数据 

- 不可重复读

![image](https://cdn.jsdelivr.net/gh/Yukino831143/CDN/blogImageHosting/20201018152727.png)

解决方法：锁行 
- 幻读

![image](https://cdn.jsdelivr.net/gh/Yukino831143/CDN/blogImageHosting/20201018154446.png)

解决方法：锁表

**Mysql事务隔离级别**

![image](https://cdn.jsdelivr.net/gh/Yukino831143/CDN/blogImageHosting/20201018152949.png)

**JDBC事务隔离级别**

![image](https://cdn.jsdelivr.net/gh/Yukino831143/CDN/blogImageHosting/20201018153050.png)

**事务传播行为**

![image](https://cdn.jsdelivr.net/gh/Yukino831143/CDN/blogImageHosting/20201018153209.png)
# 0. 公共代码
```XML
<!--spring-dao.xml-->
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
        <property name="url" value="jdbc:mysql://localhost:3306/os?useUnicode=true&amp;characterEncoding=utf-8"/>
        <property name="username" value="root"/>
        <property name="password" value="foreverlove"/>
    </bean>
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--在 XML 配置文件中启动 Spring 的自动扫描功能
    我们就不再需要在 XML 中显式使用 <bean/> 进行Bean 的配置。
    Spring 在容器初始化时将自动扫描 com.imooc.os.dao 指定的包及其子包下的所有 class文件，
    所有标注了 @Repository 的类都将被注册为 Spring Bean。
    -->
    <context:component-scan base-package="com.imooc.os.dao"/>
</beans>
```

```Java
@Repository
public class OrderDaoImpl implements OrderDao {

    @Autowired
    private JdbcTemplate jdbcTemplate;
    public void insert(Order order) {
        String sql = "insert into orders values(?,?,?,?,?,?,?,?,?,?,?)";
        jdbcTemplate.update(sql,order.getId(),order.getProductsId(),order.getNumber(),order.getPrice(),order.getCreateTime(),order.getSendTime(),order.getConfirmTime(),order.getConsignee(),order.getConsigneePhone(),order.getConsigneeAddress(),order.getStatus());
    }

    public void update(Order order) {
        String sql = "update orders set create_time=?,send_time=?,confirm_time=?,consignee=?,consignee_phone=?,consignee_address=?,status=? where id=?";
        jdbcTemplate.update(sql,order.getProductsId(),order.getNumber(),order.getPrice(),order.getCreateTime(),order.getSendTime(),order.getConfirmTime(),order.getConsignee(),order.getConsigneePhone(),order.getConsigneeAddress(),order.getStatus(),order.getId());
    }

    public void delete(String id) {
        String sql = "delete from orders where id=?";
        jdbcTemplate.update(sql,id);
    }

    public Order select(String id) {
        String sql = "select form orders where id=?";
        return jdbcTemplate.queryForObject(sql,new OrderRowMapper(),id);
    }

    public List<Order> select() {
        String sql = "select form orders";
        return jdbcTemplate.query(sql,new OrderRowMapper());
    }

    private class OrderRowMapper implements RowMapper<Order>{
        public Order mapRow(ResultSet resultSet, int i) throws SQLException {
            Order order=new Order();
            order.setId(resultSet.getString("id"));
            order.setProductsId(resultSet.getString("product_id"));
            order.setNumber(resultSet.getInt("number"));
            order.setPrice(resultSet.getDouble("price"));
            order.setCreateTime(resultSet.getTimestamp("create_time"));
            order.setSendTime(resultSet.getTimestamp("send_time"));
            order.setConfirmTime(resultSet.getTimestamp("confirm_time"));
            order.setConsignee(resultSet.getString("consignee"));
            order.setConsigneePhone(resultSet.getString("consignee_phone"));
            order.setConsigneeAddress(resultSet.getString("consignee_address"));
            order.setStatus(resultSet.getString("status"));
            return order;
        }
    }
}
```
```Java
@Repository
public class ProductDaoImpl implements ProductDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;
    public void insert(Product product) {
        String sql = "insert into products values(?,?,?,?,?)";
        jdbcTemplate.update(sql,product.getId(),product.getTitle(),product.getPrice(),product.getStock(),product.getStatus());
    }

    public void update(Product product) {
        String sql = "update products set title=?,price=?,stock=?,status=? where id=?";
        jdbcTemplate.update(sql,product.getTitle(),product.getPrice(),product.getStock(),product.getStatus(),product.getId());
    }

    public void delete(String id) {
        String sql = "delete form products where id=?";
        jdbcTemplate.update(sql,id);
    }

    public Product select(String id) {
        String sql = "select * from products where id=?";
        return jdbcTemplate.queryForObject(sql,new ProductRowMapper(),id);
    }

    public List<Product> select() {
        String sql = "select * from products";
        return jdbcTemplate.query(sql,new ProductRowMapper());
    }

    private class ProductRowMapper implements RowMapper<Product>{

        public Product mapRow(ResultSet resultSet, int i) throws SQLException {
            Product product = new Product();
            product.setId(resultSet.getString("id"));
            product.setTitle(resultSet.getString("title"));
            product.setPrice(resultSet.getDouble("price"));
            product.setStock(resultSet.getInt("stock"));
            product.setStatus(resultSet.getString("status"));
            return product;
        }
    }
}
```

# 1.Spring编程式事物处理
## 1.1 方式1 基于底层API的编程式事物处理
- PlatformTransactionManager
- TransactionDefinition
- TransactionStatus

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <import resource="spring-dao.xml"/>
    <context:component-scan base-package="com.imooc.os.service.impl1"/>
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <bean id="transactionDefinition" class="org.springframework.transaction.support.DefaultTransactionDefinition">
        <property name="propagationBehaviorName" value="PROPAGATION_REQUIRED"/>
        <!--PROPAGATION_REQUIRED 表示 事务传播行为--支持当前事务，如果没有当前事务，就新建一个事务-->
        <!--也可配置隔离级别等其他属性，不配置即使用默认-->
    </bean>
</beans>
```
```Java
package com.imooc.os.service;

import com.imooc.os.entity.Order;

public interface OrderService {
    void addOrder(Order order);
}
```
```Java
/*1.基于底层API的方式 PlatformTransactionManager TransactionDefinition*/
@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private OrderDao orderDao;
    @Autowired
    private ProductDao productDao;
    @Autowired
    private PlatformTransactionManager transactionManager;
    @Autowired
    private TransactionDefinition transactionDefinition;

    public void addOrder(Order order) {
        order.setCreateTime(new Date());
        order.setStatus("待付款");
        //开启事务
        TransactionProxyFactoryBean transactionProxyFactoryBean;
        TransactionStatus transactionStatus = transactionManager.getTransaction(transactionDefinition);
        try {
            orderDao.insert(order);
            Product product = productDao.select(order.getProductsId());
            product.setStock(product.getStock() - order.getNumber());
            productDao.update(product);
            //结束事务
            transactionManager.commit(transactionStatus);
        }catch (Exception e){
            e.printStackTrace();
            //出问题，将未提交的sql回滚
            transactionManager.rollback(transactionStatus);
        }
    }
}
```
```Java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-service1.xml")
//1.基于底层api的编程式事务处理方式
public class OrderTest {

    @Autowired
    private OrderService orderService;

    @Test
    public void testAddOrder(){
        Order order = new Order("100013","100002",2,1799,"","","");
        orderService.addOrder(order);
    }


}
```

## 1.2 基于TransactionTemplate的编程式事务管理
- TransactionTemplate

```Xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <import resource="spring-dao.xml"/>
    <context:component-scan base-package="com.imooc.os.service.impl2"/>
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
        <property name="transactionManager" ref="transactionManager"/>
    </bean>
</beans>
```

```Java
//2.基于TransactionTemplate的编程式事务管理
@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private OrderDao orderDao;
    @Autowired
    private ProductDao productDao;

    //2.TransactionTemplate 用于简化底层API
    @Autowired
    private TransactionTemplate transactionTemplate;

    public void addOrder(final Order order) {
        order.setCreateTime(new Date());
        order.setStatus("待付款");

        transactionTemplate.execute(new TransactionCallback() {
            public Object doInTransaction(TransactionStatus transactionStatus) {
                try {
                    orderDao.insert(order);
                    Product product = productDao.select(order.getProductsId());
                    product.setStock(product.getStock() - order.getNumber());
                    productDao.update(product);
                    //不需要提交，TransactionTemplate会帮你提交
                }catch (Exception e){
                    e.printStackTrace();
                    transactionStatus.setRollbackOnly();//回滚
                }
                return null;
            }
        });

    }
}
```
```Java
public interface OrderService {
    void addOrder(Order order);
}
```
```Java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-service2.xml")
//2.基于TransactionTemplate的编程式事务管理
public class OrderTest {

    @Autowired
    private OrderService orderService;

    @Test
    public void testAddOrder(){
        Order order = new Order("100013","100002",2,1799,"","","");
        orderService.addOrder(order);
    }

}
```

# 2.Spring声明式事务处理
**简介**
- Spring的声明式事务处理是建立在AOP的基础之上的。其本质是对方法前后进行拦截，然后在目标方法开始之前创建或加一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。
- 建议在开发中使用声明式事务，使得业务代码纯粹干净，方便后期代码维护

**分类**
- 基于TransactionInterceptor的声明式事务处理。
- 基于TransactionProxyFactoryBean的声明式事务处理。
- 基于<tx> 命名空间的声明式事物管理。
- 基于 @Transactional的声明式事务管理。

## 2.1 基于TransactionInterceptor的声明式事务处理。

```Xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <import resource="spring-dao.xml"/>
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--1.基于TransactionInterceptor的声明式事务处理-->
    <bean id="orderServiceTarget" class="com.imooc.os.service.impl.OrderServiceImpl"/>
    <bean id="transactionInterceptor" class="org.springframework.transaction.interceptor.TransactionInterceptor">
        <!--配置事务管理器-->
        <property name="transactionManager" ref="transactionManager"/>
        <!--配置拦截器-->
        <property name="transactionAttributes">
            <props>
                <!--key是方法，配置事务传播行为，属性-->
                <prop key="get*">PROPAGATION_REQUIRED,readOnly</prop>
                <prop key="find*">PROPAGATION_REQUIRED,readOnly</prop>
                <prop key="search*">PROPAGATION_REQUIRED,readOnly</prop>
                <prop key="*">PROPAGATION_REQUIRED</prop>
            </props>
        </property>
    </bean>
    <!--用拦截器增强目标类，建立关联-->
    <bean id="orderService" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="orderServiceTarget"/>
        <property name="interceptorNames">
            <list>
                <idref bean="transactionInterceptor"/>
            </list>
        </property>
    </bean>
</beans>
```
```Java
@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private OrderDao orderDao;
    @Autowired
    private ProductDao productDao;

    public void addOrder(Order order) {
        order.setCreateTime(new Date());
        order.setStatus("待付款");

        orderDao.insert(order);
        Product product = productDao.select(order.getProductsId());
        product.setStock(product.getStock() - order.getNumber());
        productDao.update(product);

    }
}
```
对于addOrder，由于拦截器增强，出错时，这个函数的操作自动回滚。

```Java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-service3.xml")
//3.基于TransactionInterceptor的声明式事务处理
public class OrderTest {

    @Autowired
    private OrderService orderService;

    @Test
    public void testAddOrder(){
        Order order = new Order("100013","100002",2,1799,"","","");
        orderService.addOrder(order);
    }
}
```


## 2.2 基于TransactionProxyFactoryBean的声明式事务处理
```Xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <import resource="spring-dao.xml"/>
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--2.基于TransactionProxyFactoryBean的声明式事务处理，其实就是简化上面的那种拦截器-->
    <bean id="orderServiceTarget" class="com.imooc.os.service.impl.OrderServiceImpl"/>
    <bean id="orderService" class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean">
        <property name="transactionManager" ref="transactionManager"/>
        <property name="transactionAttributes">
            <props>
                <prop key="get*">PROPAGATION_REQUIRED,readOnly</prop>
                <prop key="find*">PROPAGATION_REQUIRED,readOnly</prop>
                <prop key="search*">PROPAGATION_REQUIRED,readOnly</prop>
                <prop key="*">PROPAGATION_REQUIRED</prop>
            </props>
        </property>
        <property name="target" ref="orderServiceTarget"/>
    </bean>
</beans>
```
```Java
@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private OrderDao orderDao;
    @Autowired
    private ProductDao productDao;

    public void addOrder(Order order) {
        order.setCreateTime(new Date());
        order.setStatus("待付款");

        orderDao.insert(order);
        Product product = productDao.select(order.getProductsId());
        product.setStock(product.getStock() - order.getNumber());
        productDao.update(product);

    }
}
```
```Java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-service4.xml")
//4.基于TransactionProxyFactoryBean的声明式事务处理，其实就是简化上面的那种拦截器
public class OrderTest {

    @Autowired
    private OrderService orderService;

    @Test
    public void testAddOrder(){
        Order order = new Order("100013","100002",2,1799,"","","");
        orderService.addOrder(order);
    }
}
```

## 2.3 (最推荐)基于<tx> 命名空间的声明式事物管理。
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
    <import resource="spring-dao.xml"/>
    <context:component-scan base-package="com.imooc.os.service.impl"/>
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--3.基于<tx>命名空间的声明式事务处理，这种是实际开发常用的-->
    <!--增加通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="get*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="find*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="search*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>
    <!--增强的地方,在哪个方法增强-->
    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.imooc.os.service.impl.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```
```Java
@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private OrderDao orderDao;
    @Autowired
    private ProductDao productDao;

    public void addOrder(Order order) {
        order.setCreateTime(new Date());
        order.setStatus("待付款");

        orderDao.insert(order);
        Product product = productDao.select(order.getProductsId());
        product.setStock(product.getStock() - order.getNumber());
        productDao.update(product);

    }
}
```
```Java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-service5.xml")
//5.基于<tx>命名空间的声明式事务处理，这种是实际开发常用的
public class OrderTest {

    @Autowired
    private OrderService orderService;

    @Test
    public void testAddOrder(){
        Order order = new Order("100013","100002",2,1799,"","","");
        orderService.addOrder(order);
    }
}
```

## 2.4 基于 @Transactional的声明式事务管理。
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
    <import resource="spring-dao.xml"/>
    <context:component-scan base-package="com.imooc.os.service.impl6"/>
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--4.基于@Transactional的声明式事务管理-->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```
```Java
@Service
public class OrderServiceImpl implements OrderService {

    @Autowired
    private OrderDao orderDao;
    @Autowired
    private ProductDao productDao;

    //4.基于@Transactional的声明式事务管理
    @Transactional(propagation = Propagation.REQUIRED)
    public void addOrder(Order order) {
        order.setCreateTime(new Date());
        order.setStatus("待付款");

        orderDao.insert(order);
        Product product = productDao.select(order.getProductsId());
        product.setStock(product.getStock() - order.getNumber());
        productDao.update(product);

    }
}
```

```Java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-service6.xml")
//6.基于@Transactional的声明式事务管理,这种是实际开发常用的

public class OrderTest {

    @Autowired
    private OrderService orderService;

    @Test
    public void testAddOrder(){
        Order order = new Order("100013","100002",2,1799,"","","");
        orderService.addOrder(order);
    }
}
```


