# Spring整合MyBatis
## 1. 需要导入的jar包
```xml
<dependencies>
        <!-- spring核心包 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>
        <!-- spring end -->

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.3.RELEASE</version>
        </dependency>

        <!-- AOP begin -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.7.4</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.7.4</version>
        </dependency>
        <dependency>
            <groupId>cglib</groupId>
            <artifactId>cglib</artifactId>
            <version>3.1</version>
        </dependency>
        <!-- aop end -->

        <!-- mybatis核心包 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>
        <!-- mybatis end -->

        <!-- Mysql数据库驱动包 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.34</version>
        </dependency>
        <!-- SQLserver -->
        <dependency>
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <version>7.0.0.jre8</version>
        </dependency>
        <!-- driver end -->
        <!-- connection pool -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.5</version>
        </dependency>

        <!-- 日志文件管理包 -->
        <!-- log start -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.11.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>2.11.1</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.25</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.25</version>
        </dependency>
        <!-- log end -->

        <!--单元测试依赖-->
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>6.14.3</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```
## 2.命名规范
1.mybatis中的mapper映射文件的namespace属性的值，要跟对应类的全限定性类名一致。
2.mapper映射文件的id属性的值，要跟对应接口中的对应的方法名一致。
比如：
StudentDao接口：
```java
public interface  StudentDao {
    Student selectStudentById(int id);
}
```
student_mapper.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.dao.StudentDao">
    <select id="selectStudentById" resultType="Student">
        select tname,score from t_student where id=#{id}
    </select>
</mapper>
```
若两者命名不一致，则会导致如下错误：
```log
org.apache.ibatis.binding.BindingException: Invalid bound statement (not found)
```
## 3. spring主配置文件
1. 首先需要导入jdbc.properties文件
```xml
<context:property-placeholder location="jdbc_sqlServer.properties"/>
```
2. 注册数据源
```xml
<!--注册数据源-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```
3. 注册SqlSessionFactory
```xml
<!--注册sqlSessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
     <!-- 指明数据源 -->
    <property name="dataSource" ref="dataSource"/>
    <!--<property name="configLocation" value="mybatis.xml"/>-->
    <!-- 给com.entity包下的类取别名，与mybatis.xml中的typeAliase的功能相同 -->
    <property name="typeAliasesPackage" value="com.entity"/>
    <!-- 扫描mapper下的所有xml文件，与mybatis中的mappers功能相同 -->
    <property name="mapperLocations" value="classpath:mapper/*.xml"/>
</bean>
```
4. 注册自动扫描映射
```xml
<!--自动扫描映射-->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.dao"/>
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
</bean>
```
此处的功能相当于mybatis中的：
```java
XXXDao dao=SqlSessionFactory.getSqlSession(XXXDao.class);
```
5.完整整合配置
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <context:annotation-config/>
    <context:component-scan base-package="com"/>
    <context:property-placeholder location="jdbc_sqlServer.properties"/>
    <aop:aspectj-autoproxy proxy-target-class="true"/>

    <!--注册数据源-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    <!--注册sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>

        <property name="typeAliasesPackage" value="com.entity"/>
        <property name="mapperLocations" value="classpath:mapper/*.xml"/>
    </bean>
    <!--自动扫描映射-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.dao"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>
    <!-- 注册事务管理器--> 
    <bean id="tx" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```
6.service及entity
1)StudentServiceImpl
```java
@Service("studentService")
public class StudentServiceImpl implements IStudentService {
    @Resource
    private StudentDao dao;
    
    public void getStudent() {
        Student student = dao.selectStudentById(31);
        System.out.println(student);
    }
}
```
2) Student
```java
public class Student {
    private Integer id;
    private String tname;
    private Double score;
    //省略getter,setter,toString
```
3)测试类
```java
public class StudentServiceImplTest {
    private ApplicationContext ctx;
    @BeforeMethod
    public void before(){
         ctx=new ClassPathXmlApplicationContext("applicationContext.xml");
    }
    @Test
    public void test(){
        IStudentService studentService = ctx.getBean("studentService",IStudentService.class);
        studentService.getStudent();
    }
}
```
输出结果：
```log
Student{id=null, tname='李四', score=23.0}
```

