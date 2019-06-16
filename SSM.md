# SSM

## 基本概念

SSM:	Spring+SpringMVC+MyBatis

### Spring

Spring是一个开源框架，是为了解决企业应用开发的复杂习惯而创建的

Spring使用JavaBean来完成以前只可能由EJB完成的事情，是一个轻量级的控制反转（IOC）和面向切面（AOP)的容器框架



### SpringMVC



#### 介绍

​	SpringMVC是Spring框架的一个分支产品，以SpringIOC容器为基础，并利用容器的特性来简化它的配置。

相当于Spring的一个子模块，SpringMVC是管理三层框架中的表现层，即WEB层，三层架构中，业务层（service层）依赖持久层，表现层（WEB层）依赖业务层。

#### SpringMVC实现原理

SpringMVC分离了控制器，模型对象，分派器以及处理程序对象的角色，这种分离让它们更容易进行定制

### MyBatis

MyBatis是一个基于Java的持久层框架，使用简单的xml或注解用于配置和原始映射，将接口和Java的POJOs(plain old java Object,普通的java对象)映射成数据库的记录

### SSM

SpringMVC负责MVC设计模式的实现

MyBatis负责数据持久层，

SpringIOC来管理SpringMVC和MyBatis相关对象的创建注入，Spring的AOP负责事务管理

![1560252058888](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1560252058888.png)

### 搭建SSM开发环境1

1.创建Java Web工程，Maven引入依赖jar包。

pom.xml：



```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.southwind</groupId>
  <artifactId>SSMMaven</artifactId>
  <packaging>war</packaging>
  <version>0.0.1-SNAPSHOT</version>
  <name>SSMMaven Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>

        <!-- SpringMVC -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!-- Spring JDBC -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!-- Spring AOP -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>

        <!-- MyBatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.2</version>
        </dependency>

        <!-- MyBatis整合Spring的适配包 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>

        <!-- MySQL驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.41</version>
        </dependency>

        <!-- C3P0数据源 -->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1</version>
        </dependency>

        <!-- JSTL -->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <!-- ServletAPI -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>

  </dependencies>
  <build>
    <finalName>SSMMaven</finalName>
  </build>
</project>
```

2.web.xml配置开启Spring，SpringMVC，字符编码过滤器，加载静态资源（因为SpringMVC会拦截所有请求，导致JSP页面中对js和css的引用也被拦截，配置后可以把对静态资源（js，css，图片等）的请求交给项目的默认拦截器而不是SpringMVC）。



```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <!-- 启动Spring -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <!-- SpringMVC的前端控制器，拦截所有请求 -->
  <servlet>
    <servlet-name>mvc-dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>

  <servlet-mapping>
    <servlet-name>mvc-dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

  <!-- 字符编码过滤器，一定要放在所有过滤器之前 -->
  <filter>
      <filter-name>CharacterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
          <param-name>encoding</param-name>
          <param-value>utf-8</param-value>
      </init-param>
      <init-param>
          <param-name>forceRequestEncoding</param-name>
          <param-value>true</param-value>
      </init-param>
      <init-param>
          <param-name>forceResponseEncoding</param-name>
          <param-value>true</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>CharacterEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>

  <!-- 加载静态资源 -->
  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.js</url-pattern>
  </servlet-mapping>

  <servlet-mapping>
      <servlet-name>default</servlet-name>
      <url-pattern>*.css</url-pattern>
  </servlet-mapping>

</web-app>
```

3.SSM框架的整合是通过设置各自的配置文件来完成的，配置文件存放在resources目录下。





![img](https://mmbiz.qpic.cn/mmbiz_png/t4kgaKYXPLdic0QFRNQNNU9ZKndqQBhdGncTYibLkBy33BWn6fFYmqCsHFplcUzhSmjrT1UialuzSLMbJMKFnQibAg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





**applicationContext.xml：Spring的配置文件。**

**dbconfig.properties：数据库配置文件。**

**mybatis-config.xml：MyBatis的配置文件。**

**springmvc.xml：SpringMVC的配置文件。**



我们知道SpringMVC本就是Spring框架的一个后续产品，所以SpringMVC和Spring不存在整合，所谓的SSM整合实际上是将MyBatis和Spring进行整合，换句话说，让Spring来管理MyBatis。



4.applicationContext.xml配置MyBatis相关信息，以及事务管理。



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!-- 加载外部文件 -->
    <context:property-placeholder location="classpath:dbconfig.properties"/>
    
    <!-- 配置C3P0数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="${jdbc.user}"></property>
        <property name="password" value="${jdbc.password}"></property>
        <property name="driverClass" value="${jdbc.driverClass}"></property>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
        <property name="initialPoolSize" value="5"></property>
        <property name="maxPoolSize" value="10"></property>
    </bean>

    <!-- 配置MyBatis SqlSessionFactory -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 指定MyBatis数据源 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 指定MyBatis mapper文件的位置 -->
        <property name="mapperLocations" value="classpath:com/southwind/dao/*.xml"/>
        <!-- 指定MyBatis全局配置文件的位置 -->
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    </bean>

    <!-- 扫描MyBatis的mapper接口 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--扫描所有DAO接口的实现，加入到IOC容器中 -->
        <property name="basePackage" value="com.southwind.dao"/>
    </bean>

    <!-- 配置事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 配置数据源 -->
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!-- 配置事务增强，事务如何切入  -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!-- 所有方法都是事务方法 -->
            <tx:method name="*"/>
            <!-- 以get开始的所有方法  -->
            <tx:method name="get*" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!-- 开启基于注解的事务  -->
    <aop:config>
        <!-- 切入点表达式 -->
        <aop:pointcut expression="execution(* com.southwind.service.impl.*.*(..))" id="txPoint"/>
        <!-- 配置事务增强 -->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPoint"/>
    </aop:config>
</beans>
```

5.dbconfig.properties配置数据库连接信息。



```
jdbc.jdbcUrl=jdbc:mysql://localhost:3306/ssm?useUnicode=true&characterEncoding=UTF-8
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.user=root
jdbc.password=root
```



6.mybatis-config.xml配置MyBatis的相关设置，因为MyBatis的大部分配置交给Spring来管理了，即在applicationContext.xml中进行了配置，所以，mybatis-config.xml只是配置一些辅助性设置，可以省略。



```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!-- 打印SQL-->
        <setting name="logImpl" value="STDOUT_LOGGING" />
    </settings>

    <typeAliases>
        <!-- 指定一个包名，MyBatis会在包名下搜索需要的JavaBean-->
        <package name="com.southwind.entity"/>
    </typeAliases>

</configuration>
```



7.配置springmvc.xml。



```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd">

    <!-- 告知spring，启用springmvc的注解驱动 -->
    <mvc:annotation-driven />

    <!-- 扫描业务代码 -->
    <context:component-scan base-package="com.southwind"></context:component-scan>

    <!-- 配置视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

</beans>
```



8.SSM环境搭建完成，在MySQL中创建数据表department，employee。



```
DROP TABLE IF EXISTS `department`;
CREATE TABLE `department` (
  `d_id` int(11) NOT NULL AUTO_INCREMENT,
  `d_name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`d_id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;

INSERT INTO `department` VALUES ('1', '研发部');
INSERT INTO `department` VALUES ('2', '销售部');
INSERT INTO `department` VALUES ('3', '行政部');
```



```
DROP TABLE IF EXISTS `employee`;
CREATE TABLE `employee` (
  `e_id` int(11) NOT NULL AUTO_INCREMENT,
  `e_name` varchar(255) DEFAULT NULL,
  `e_gender` varchar(255) DEFAULT NULL,
  `e_email` varchar(255) DEFAULT NULL,
  `e_tel` varchar(255) DEFAULT NULL,
  `d_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`e_id`),
  KEY `d_id` (`d_id`),
  CONSTRAINT `employee_ibfk_1` FOREIGN KEY (`d_id`) REFERENCES `department` (`d_id`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;

INSERT INTO `employee` VALUES ('1', '张三', '男', 'zhangsan@163.com', '13567896657', '1');
INSERT INTO `employee` VALUES ('2', '李四', '男', 'lisi@163.com', '16789556789', '1');
INSERT INTO `employee` VALUES ('3', '王五', '男', 'wangwu@163.com', '16678906541', '2');
INSERT INTO `employee` VALUES ('4', '小明', '男', 'xiaoming@163.com', '15678956781', '2');
INSERT INTO `employee` VALUES ('5', '小红', '女', 'xiaohong@163.com', '13345678765', '3');
INSERT INTO `employee` VALUES ('6', '小花', '女', 'xiaohua@163.com', '18367654678', '3');
```



9.创建实体类Department，Employee。



```
public class Department {
    private int id;
    private String name;
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Department(int id, String name) {
        super();
        this.id = id;
        this.name = name;
    }
    public Department() {
        super();
    }

}
```



```java
public class Employee {
    private int id;
    private String name;
    private String gender;
    private String email;
    private String tel;
    private Department department;
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getGender() {
        return gender;
    }
    public void setGender(String gender) {
        this.gender = gender;
    }
    public String getEmail() {
        return email;
    }
    public void setEmail(String email) {
        this.email = email;
    }
    public String getTel() {
        return tel;
    }
    public void setTel(String tel) {
        this.tel = tel;
    }
    public Department getDepartment() {
        return department;
    }
    public void setDepartment(Department department) {
        this.department = department;
    }


}
```



10.数据库测试数据创建完成，接下来开始写业务代码，首先Controller。



```java
@Controller
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;

    /**
     * 查询所有员工
     * @return
     */
    @RequestMapping(value="/queryAll")
    public ModelAndView test(){
        List<Employee> list = employeeService.queryAll();
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        modelAndView.addObject("list", list);
        return modelAndView;
    }

}
```



11.Controller调用Service，创建Service接口，实现类。



```java
public interface EmployeeService {
    public List<Employee> queryAll();
}
```



```java
@Service
public class EmployeeServiceImpl implements EmployeeService{

    @Autowired
    private EmployeeDAO employeeDAO;

    public List<Employee> queryAll() {
        // TODO Auto-generated method stub
        return employeeDAO.queryAll();
    }

}
```



12.Service调用DAO，创建DAO接口，此时没有DAO的实现类。使用MyBatis框架，在DAO.xml中配置实现接口方法需要的SQL，程序运行时，通过动态代理产生实现接口的代理对象。



```java
public interface EmployeeDAO {
    public List<Employee> queryAll();
}
```



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd"> 
<mapper namespace="com.southwind.dao.EmployeeDAO">

    <resultMap type="Employee" id="employeeMap">
        <id property="id" column="e_id"/>
        <result property="name" column="e_name"/>
        <result property="gender" column="e_gender"/>
        <result property="email" column="e_email"/>
        <result property="tel" column="e_tel"/>
        <association property="department" javaType="Department">
            <id property="id" column="d_id"/>
            <result property="name" column="d_name"/>
        </association>
    </resultMap> 

    <select id="queryAll" resultMap="employeeMap">
        select * from employee e, department d where e.d_id = d.d_id
    </select>

</mapper>
```



13.创建index.jsp，前端使用bootstrap框架。



```xml
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ page isELIgnored="false" %>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>员工列表</title>
<link rel="stylesheet" href="static/bootstrap-3.3.7-dist/css/bootstrap.min.css">
</head>
<body>

    <div class="container">
        <!-- 标题 -->
        <div class="row">
            <div class="col-md-12">
                <h1>SSM-员工管理</h1>
            </div>
        </div>
        <!-- 显示表格数据 -->
        <div class="row">
            <div class="col-md-12">
                <table class="table table-hover" id="emps_table">
                    <thead>
                        <tr>
                            <th>
                                <input type="checkbox" id="check_all"/>
                            </th>
                            <th>编号</th>
                            <th>姓名</th>
                            <th>性别</th>
                            <th>电子邮箱</th>
                            <th>联系电话</th>
                            <th>部门</th>
                            <th>操作</th>
                        </tr>
                    </thead>
                    <tbody>
                        <c:forEach items="${list }" var="employee">
                            <tr>
                                <td><input type='checkbox' class='check_item'/></td>
                                <td>${employee.id }</td>
                                <td>${employee.name }</td>
                                <td>${employee.gender }</td>
                                <td>${employee.email }</td>
                                <td>${employee.tel }</td>
                                <td>${employee.department.name }</td>
                                <td>
                                    <button class="btn btn-primary btn-sm edit_btn">
                                        <span class="glyphicon glyphicon-pencil">编辑</span>
                                    </button>&nbsp;&nbsp;
                                    <button class="btn btn-danger btn-sm delete_btn">
                                        <span class="glyphicon glyphicon-trash">删除</span>
                                    </button>
                                </td>
                            </tr>
                        </c:forEach>
                    </tbody>
                </table>
            </div>
        </div>

    </div>
</body>
</html>
```



14.部署tomcat，启动，测试。



![img](https://mmbiz.qpic.cn/mmbiz_png/t4kgaKYXPLdic0QFRNQNNU9ZKndqQBhdG8yibNZymsjLuLAVpLzOgHwW2Dz2cUT0hAHk30nG2R2xrEicVWpR2Pb2Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



SSM框架搭建成功。



**注意：**

1.Controller，Service，DAO交给IOC容器管理，一定要结合配置文件的自动扫描和类定义处的注解完成，对象之间的依赖注入通过@Autowire来完成。



```xml
<!-- 扫描业务代码 -->
<context:component-scan base-package="com.southwind"></context:component-scan>
```



```java
@Controller
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;
```



2.DAO.xml的namespace与DAO接口一定要对应起来，不能写错。



```java
<mapper namespace="com.southwind.dao.EmployeeDAO">
```



3.DAO.xml中的parameterType和resultType，或者resultMap所对应的类型要与mybatis-config.xml中配置的typeAliases结合使用，组成对应实体类的全类名，如果mybatis-config.xml中没有配置typeAliases，则DAO.xml中直接写实体类的全类名即可。



```java
<resultMap type="Employee" id="employeeMap">
```



```java
<typeAliases>
    <!-- 指定一个包名，MyBatis会在包名下搜索需要的JavaBean-->
    <package name="com.southwind.entity"/>
</typeAliases>
```

### 搭建SSM开发环境2

controller

```java
package com.itcast.controller;

import com.itcast.domain.Account;
import com.itcast.service.AccountService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;


@RequestMapping("/account")
@Controller
public class AccountController {

    @Autowired
    private AccountService accountService;


    @RequestMapping("/findAll")
    public ModelAndView findAllAccount() {
        System.out.println("表现层");
        List<Account> accounts = accountService.findAll();
        ModelAndView mv = new ModelAndView();
        mv.addObject("accounts", accounts);
        mv.setViewName("list");
        return mv;
    }


    @RequestMapping("/save")
    public void save(Account account, HttpServletRequest request, HttpServletResponse response) throws IOException {
        System.out.println("表现层");
        accountService.save(account);
        //重定向
        //response.sendRedirect("/account/findAll");
        response.sendRedirect(request.getContextPath()+"/account/findAll");
    }
}

```

dao

```java
package com.itcast.dao;

import com.itcast.domain.Account;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface AccountDao {

    /**
     * 查询账户
     * @return
     */
    @Select("select * from account")
    List<Account> findAll();


    /**
     * 保存账户
     * @param account
     */
    @Insert("insert into account (name,money) values (#{name},#{money})")
    void save(Account account);




}

```

domain

```java
package com.itcast.domain;

import java.io.Serializable;

public class Account implements Serializable {

    private Integer id;
    private String name;
    private Double money;

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", money=" + money +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Double getMoney() {
        return money;
    }

    public void setMoney(Double money) {
        this.money = money;
    }
}

```

service-Impl

```java
package com.itcast.service.Impl;

import com.itcast.dao.AccountDao;
import com.itcast.domain.Account;
import com.itcast.service.AccountService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;




@Service("accountService")
public class AccountServiceImpl implements AccountService {
        @Autowired
    private  AccountDao accountDao;


    @Override
    public List<Account> findAll() {
        System.out.println("业务层，查询所有账户");
        List<Account> list = accountDao.findAll();
        return list;
    }

    @Override
    public void save(Account account) {
        System.out.println("业务层，保存账户");
        accountDao.save(account);
    }
}

```

service

```
package com.itcast.service;

import com.itcast.domain.Account;

import java.util.List;

public interface AccountService {




    /**
     * 查询账户
     * @return
     */
    List<Account> findAll();


    /**
     * 保存账户
     * @param account
     */
    void save(Account account);





}

```

resource-applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

    <!--1. 搭建和测试Spring的开发环境-->
    <!-- 开启注解扫描，要扫描的是service和dao层的注解，要忽略web层注解，因为web层让SpringMVC框架
去管理 -->

    <context:component-scan base-package="com.itcast">
<!--配置要忽略的注解-->
    <context:exclude-filter type="annotation"
                expression="org.springframework.stereotype.Controller"/>
</context:component-scan>



    <!--1. 搭建和测试mybatis的开发环境-->
    <!-- 配置C3P0的连接池对象 -->
    <bean id="dataSource"
          class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql:///mydb1" />
        <property name="username" value="root" />
        <property name="password" value="root" />
    </bean>

    <!-- 配置SqlSession的工厂 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <!-- 配置扫描dao的包 -->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.itcast.dao"/>
    </bean>



    <!--配置Spring框架声明式事务管理-->
    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="*" isolation="DEFAULT"/>
        </tx:attributes>
    </tx:advice>

    <!--配置AOP增强-->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.itcast.service.Impl.AccountServiceImpl.*(..))"/>
    </aop:config>

    </beans>
```

log4j.properties

```xml
# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=info, CONSOLE, LOGFILE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n

# LOGFILE is set to be a File appender using a PatternLayout.
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=d:\axis.log
log4j.appender.LOGFILE.Append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n


```

springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 扫描controller的注解，别的不扫描 -->

    <context:component-scan base-package="com.itcast">
        <context:include-filter type="annotation"
            expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!-- 配置视图解析器 -->

    <bean id="viewResolver"
          class="org.springframework.web.servlet.view.InternalResourceViewResolver">

        <!-- JSP文件所在的目录 -->

        <property name="prefix" value="/WEB-INF/pages/" />
        <!-- 文件的后缀名 -->
        <property name="suffix" value=".jsp" />
    </bean>

    <!-- 设置静态资源不过滤 -->

    <mvc:resources location="/css/" mapping="/css/**" />
    <mvc:resources location="/images/" mapping="/images/**" />
    <mvc:resources location="/js/" mapping="/js/**" />

    <!-- 开启对SpringMVC注解的支持 -->
    <mvc:annotation-driven />





</beans>
```

webapp下WEB-INF下pages-list，视图解析器目录

```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" isELIgnored="false" %>

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<!DOCTYPE  html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>账户的列表页面</title>
</head>

<body>

<table border="1" width="300px">
    <tr>
        <th>编号</th>
        <th>账户名称</th>
        <th>账户金额</th>
    </tr>

    <c:forEach items="${accounts}" var="account" varStatus="vs">
        <tr>

            <td>${vs.count}</td>

            <td>${account.name }</td>

            <td>${account.money }</td>
        </tr>

    </c:forEach>
</table>
</body>
</html>
```

web.xml

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <!-- 配置解决中文乱码的过滤器 -->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>


  <!-- 配置前端控制器：服务器启动必须加载，需要加载springmvc.xml配置文件 -->
  <servlet>
  <servlet-name>dispatcherServlet</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <!-- 配置初始化参数，创建完DispatcherServlet对象，加载springmvc.xml配置文件 -->
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:springmvc.xml</param-value>
  </init-param>
    <!-- 服务器启动的时候，让DispatcherServlet对象创建 -->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>


  <!-- 配置Spring的监听器 -->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  <!-- 配置加载类路径的配置文件 -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
  </context-param>




</web-app>
```

index.jsp

```jsp
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2019/6/10
  Time: 18:42
  To change this template use File | Settings | File Templates.
--%>
<%@     page     language="java"     contentType="text/html;     charset=UTF-8" pageEncoding="UTF-8"%>

<!DOCTYPE    html    PUBLIC    "-//W3C//DTD    HTML    4.01   Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
<html>

<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">




<html>
<head>
    <title>Title</title>
</head>
<body>
<a href="account/findAll">查询所有</a>

<h3>测试保存账户</h3>

<form action="account/save" method="post">
姓名:<input type="text" name="name"/><br>
余额:<input type="text" name="money"/><br>
    <input type="submit" value="保存"/><br>
</form>


</body>
</html>
```

pom

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.heima</groupId>
  <artifactId>ssm1</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <name>ssm Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

<!--版本控制-->
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.0.2.RELEASE</spring.version>
    <slf4j.version>1.6.6</slf4j.version>
    <log4j.version>1.2.12</log4j.version>
    <mysql.version>5.1.6</mysql.version>
    <mybatis.version>3.4.5</mybatis.version>
  </properties>

  <dependencies>
    <!-- spring -->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.6.8</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>compile</scope>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>${mysql.version}</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>jstl</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>

    <!-- log start -->
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>${log4j.version}</version>
    </dependency>

    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>${slf4j.version}</version>
    </dependency>

    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>${slf4j.version}</version>
    </dependency>

    <!-- log end -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>${mybatis.version}</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
    </dependency>

    <dependency>
      <groupId>c3p0</groupId>
      <artifactId>c3p0</artifactId>
      <version>0.9.1.2</version>
      <type>jar</type>
      <scope>compile</scope>
    </dependency>
  </dependencies>

  <build>
    <finalName>ssm</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>
```