# SSM整合

# 整合思路

Spring+MyBatis+SpringMVC

整合思路：

Spring MVC 负责实现 MVC 设计模式，MyBatis 负责数据持久层，Spring 负责管理 Spring MVC 和 MyBatis 相关对象的创建和依赖注入。

在Tomcat启动的时候，把框架容器启动，便于无服务调用容器中所需的对象

因为MyBatis框架不属于Spring里的子框架，所以我们需要将MyBatis整合到Spring中

也就是把MyBatis的所需的对象放入Spring容器中



![1652512323761](E:\建生litterNotBook\Typora_图片\1652512323761.png)



整合步骤：

1、创建 Maven 工程，pom.xml

2、web.xml 中配置 SpringMVC、Spring、字符编码过滤器、

3、Spring整合mybatis、业务类

4、SpringMVC

注意一些辅助配置请在代码案例中查看

# 代码案例

pom.xml环境搭建

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.ajs</groupId>
  <artifactId>ssm01</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>


  <!-- 集中定义依赖版本号 -->
  <properties>
    <!--单元测试的依赖-->
    <junit.version>4.12</junit.version>
    <!--spring的相关依赖-->
    <spring.version>5.2.5.RELEASE</spring.version>
    <!--mybatis的相关依赖-->
    <mybatis.version>3.5.1</mybatis.version>
    <!--mybaits与spring整合的依赖-->
    <mybatis.spring.version>1.3.1</mybatis.spring.version>
    <!--mybatis支持的分页插件的依赖-->
    <mybatis.paginator.version>1.2.15</mybatis.paginator.version>
    <!--mysql的依赖-->
    <mysql.version>5.1.32</mysql.version>
    <!--slf4j日志依赖-->
    <slf4j.version>1.6.4</slf4j.version>
    <!--阿里的数据库连接池-->
    <druid.version>1.1.12</druid.version>
    <!--分页插件的依赖-->
    <pagehelper.version>5.1.2</pagehelper.version>
    <!--JSTL的依赖(jsp的标准标签库)-->
    <jstl.version>1.2</jstl.version>
    <!--servlet的依赖-->
    <servlet-api.version>3.0.1</servlet-api.version>
    <!--jsp的依赖-->
    <jsp-api.version>2.0</jsp-api.version>
    <!--jackson的依赖,springmvc框架默认进行JSON转换的依赖工具-->
    <jackson.version>2.9.6</jackson.version>
  </properties>


  <dependencies>
    <!-- spring -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jms</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context-support</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.2.5.RELEASE</version>
    </dependency>
    <!-- Mybatis -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>${mybatis.version}</version>
    </dependency>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>${mybatis.spring.version}</version>
    </dependency>
    <dependency>
      <groupId>com.github.miemiedev</groupId>
      <artifactId>mybatis-paginator</artifactId>
      <version>${mybatis.paginator.version}</version>
    </dependency>
    <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>${pagehelper.version}</version>
    </dependency>
    <!-- MySql -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>${mysql.version}</version>
    </dependency>
    <!-- 连接池 -->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>${druid.version}</version>
    </dependency>

    <!-- junit -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>${junit.version}</version>
      <scope>test</scope>
    </dependency>


    <!-- JSP相关 -->
    <dependency>
      <groupId>jstl</groupId>
      <artifactId>jstl</artifactId>
      <version>${jstl.version}</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.0.1</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jsp-api</artifactId>
      <scope>provided</scope>
      <version>${jsp-api.version}</version>
    </dependency>
    <!-- Jackson Json处理工具包 -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>${jackson.version}</version>
    </dependency>

  </dependencies>

  <!-- 插件配置 -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
          <encoding>UTF-8</encoding>
        </configuration>
      </plugin>
    </plugins>
    <!--识别所有的配置文件-->
    <resources>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
    </resources>
  </build>
</project>

```

配置web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--SpringMVC-->
    <!--配置前端控制器-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--加载springmvc.xml配置文件-->
        <!--在Tomcat启动的时候创建SpringMVC容器-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--启动服务器，创建该servlet-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>


    <!--解决中文乱码的过滤器-->
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


        <!--Spring-->
    <!--Spring-->
    <!--配置Spring的监听器，默认只加载WEB-INF目录下的applicationContext.xml配置文件-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--设置配置文件的路径-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
</web-app>
```

SpringMVC.xml 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"

       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--开启注解扫描，只扫描Controller注解-->
    <context:component-scan base-package="com.ajs">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--配置的视图解析器对象-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--过滤静态资源-->
    <!--<mvc:resources location="/css" mapping="/css/**"/>
    <mvc:resources location="/images/" mapping="/images/**"/>
    <mvc:resources location="/js/" mapping="/js/**"/>-->

    <!--开启SpringMVC注解的支持-->
    <mvc:annotation-driven/>
</beans>
```



Spring.xml 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--开启注解的扫描，希望处理service和dao，controller不需要Spring框架去处理-->
    <context:component-scan base-package="com.ajs">
        <!--配置哪些注解不扫描-->
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
    </context:component-scan>


    <!-- 读取属性文件
    这里数据源需要外部属性的的方式，需要次属性配置
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>  -->
    
    <!--Spring整合MyBatis框架-->
    <!--配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/ajsnode_ssm"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>
    <!--配置SqlSessionFactory工厂-->
    <bean id="sqlSessonFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        
        <!-- 配置SqlMapConfig.xml核心配置 
        这里也可以把mybatis配置配置进去，可以配置一些辅助配置
        <property name="configLocation" value="classpath:mybatis.xml"></property>
        注册实体类
        配置了实体类，那Mapper配置文件中的实体类就只需要写类名就行
        <property name="typeAliasesPackage" value="com.ajs.pojo"></property>-->
    </bean>
    <!--配置dao接口所在包 得到dao代理对象-->
    <bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.ajs.dao"/>
    </bean>


    <!--配置Spring框架声明式事务管理-->
    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>


   <!-- 配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="find*" read-only="true"/>
            <tx:method name="select*" read-only="true"/>
            <tx:method name="*" isolation="DEFAULT"/>
        </tx:attributes>
    </tx:advice>

    <!--配置AOP增强-->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.ajs.service.Impl.*ServiceImpl.*(..))"/>
    </aop:config>
    <!--配置切入点+绑定-->
    <!--<aop:config>
        <aop:pointcut id="mycut" expression="execution(* com.bjpowernode.service.impl.*.*(..))"></aop:pointcut>
        <aop:advisor advice-ref="myadvice" pointcut-ref="mycut"></aop:advisor>
    </aop:config>-->

</beans>
```

dao接口，Mapper配置文件、Service、Controller

实体类这里就省略了

```java
public interface StudentDao {
    //查询所有学生
    List<Student> findAll();
}

```

```xml
<?xml version="1.0" encoding="UTF-8" ?> <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ajs.dao.StudentDao">
    <resultMap id="stuMap" type="com.ajs.Entity.Student">
        <!--
            private int id;
            private String name;
            private int age;
            private char sex;
            private String sclass;
        -->
        <id property="id" column="stu_id"/>
        <result property="name" column="stu_name"/>
        <result property="age" column="stu_age"/>
        <result property="sex" column="stu_sex"/>
        <result property="sclass" column="stu_class"/>
    </resultMap>
    <select id="findAll" resultMap="stuMap">
        select*from student
    </select>
</mapper>
```

```java
@Service
public class StudentServiceImpl implements StudentService {
    @Autowired
    StudentDao studentDao;
    @Override
    public List<Student> findAll() {
        return studentDao.findAll();
    }
}


@Controller
public class StudentController {
    @Autowired
    StudentService studentService;
    @RequestMapping("/fs")
    public String findAll(HttpServletRequest request){
        List<Student> all = studentService.findAll();
        request.setAttribute("list",all);
        return "list";
    }
}
```



