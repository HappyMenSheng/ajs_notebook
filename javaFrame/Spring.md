# Spring

spring就是一个java框架，使用java语言开发的， 轻量级的， 开源的框架。 

spring又叫做：容器， spring作为容器， 装的是java对象。 可以让spring创建java对象， 给属性赋值。

spring作用： 实现解耦合， 解决java对象之间的耦合， 解决模块之间的耦合。



spring核心技术： ioc， aop



# IOC控制反转

​	控制： 对象创建，属性赋值， 对象声明周期管理

​    反转：把开发人员管理对象的权限转移给了代码之外的容器实现。 由容器完成对象的管理。

​    正转：开发人员在代码中， 使用 new 构造方法创建对象。 开发人员掌握了对象的创建，属性赋值，对象从开始到销毁的全部过程。 开发人员有对 对象 全部控制。



**作用： 实现解耦合**



对象之间耦合，关系紧密，任何一个对象的变化都可能会影响整体

![1650442489224](E:\建生litterNotBook\Typora_图片\1650442489224.png)

 于引进了中间位置的“第三方”，也就是IOC容器，使得A、B、C、D这4个对象没有了耦合关系，齿轮之间的传动全部依靠“第三方”了，全部对象的控制权全部上缴给“第三方”IOC容器，所以，IOC容器成了整个系统的关键核心，它起到了一种类似“粘合剂”的作用，把系统中的所有对象粘合在一起发挥作用，如果没有这个“粘合剂”，对象与对象之间会彼此失去联系，这就是有人把IOC容器比喻成“粘合剂”的由来。 

![1650442516499](E:\建生litterNotBook\Typora_图片\1650442516499.png)



Spring 创建对象流程图

![1650444836492](E:\建生litterNotBook\Typora_图片\1650444836492.png)



# Spring IOC入门代码案例

**pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.bjpowernode</groupId>
  <artifactId>ch01-first</artifactId>
  <version>1.0.0</version>


  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

  <dependencies>
    <!--spring依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.5.RELEASE</version>
    </dependency>

    <!--单元测试-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

</project>

```



**Sring配置文件**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">



    <!--声明对象
        id:自定义对象名称，唯一值。(可以没有，spring可以提供默认名称)
        class：类的全限定名称，spring通过反射机制创建对象，不能是接口

        spring根据id，class创建对象， 把对象放入到spring的一个map对象。
        map.put(id,对象)
    -->
    <bean id="someService" class="com.bjpowernode.service.impl.SomeServiceImpl" />
    <bean id="someService1" class="com.bjpowernode.service.impl.SomeServiceImpl" />

    <bean id="otherService" class="com.bjpowernode.service.OtherService"/>

    <!--创建非自定义对象-->
    <bean id="mydate" class="java.util.Date" />

</beans>
```

**测试代码**

```java
 //1.指定spring配置文件: 从类路径（classpath）之下开始的路径
        String config="beans.xml";
        //2.创建容器对象， ApplicationContext 表示spring容器对象。 通过ctx获取某个java对象
        ApplicationContext ctx = new ClassPathXmlApplicationContext(config);
        //3.从容器中获取指定名称的对象， 使用getBean(“id”)
        SomeService service = (SomeService) ctx.getBean("someService");
        //4.调用对象的方法，接口中的方法
        service.doSome();
```





#DI注入 给属性赋值

DI分类： 1 set注入，也叫做设值注入 ； 2 构造注入。



2.4.1 基于xml的DI

```xml
简单类型的设值注入，使用value
<bean id="mySchool" class="com.bjpowernode.ba02.School">
    <property name="name" value="北京大学"/>
    <property name="address" value="北京的海淀区"/>
</bean>


<!--引用类型set注入-->
<bean id="myStudent" class="com.bjpowernode.ba02.Student">
    <property name="name" value="李四"/>
    <property name="age" value="22" />
    <!--引用类型的赋值-->
    <property name="school" ref="mySchool" /><!--setSchool(mySchool)-->
</bean>
```

注意：set属性注入是通过调用set方法给属性赋值，并不是直接给属性赋值

2） 构造注入

构造注入：spring调用类中的有参数构造方法， 在创建对象的同时，给属性赋值

```xml
<!--构造注入，使用name属性-->
<bean id="myStudent" class="com.bjpowernode.ba03.Student">
    <constructor-arg name="myage" value="22" />
    <constructor-arg name="myname" value="李四"/>
    <constructor-arg name="mySchool" ref="mySchool"/>
</bean>

<!--构造注入，使用index，参数的位置，构造方法参数从左往右位置是0,1,2-->
<bean id="myStudent2" class="com.bjpowernode.ba03.Student">
    <constructor-arg index="1" value="28"/>
    <constructor-arg index="0" value="张三"/>
    <constructor-arg index="2" ref="mySchool" />
</bean>


<!--构造注入，省略index属性-->
<bean id="myStudent3" class="com.bjpowernode.ba03.Student">
    <constructor-arg  value="张峰"/>
    <constructor-arg  value="28"/>
    <constructor-arg  ref="mySchool" />
</bean>
```





基于注解的DI

1.在源代码加入注解，例如@Component

2.在spring的配置文件，加入组件扫描器的标签

```xml
<context:component-scan base-package="注解所在的包名"/>



<!--声明组件扫描器：使用注解必须加入这个语句
        component-scan:翻译过来是组件扫描器，组件是java对象。
            属性： base-package 注解在你的项目中的包名。
                  框架会扫描这个包和子包中的所有类，找类中的所有注解。
                  遇到注解后，按照注解表示的功能，去创建对象， 给属性赋值。
    -->
    <context:component-scan base-package="com.bjpowernode.ba07"/>


@Component("myStudent") 
//@Component //不指定id，默认为类名小写类名"student"
public class Student {
    @Value("王五")
    private String studentName;
    @Value("66")
    private int studentAge;
    /*医用类型注解*/
  @Autowired
    private School school;

    public School getSchool() {
        return school;
    }





    <!--读取外部的属性配置文件
        property-placeholder:读取properties这样的文件
    -->
    <context:property-placeholder location="classpath:/myconf.properties" />

    @Value("${myname}")
    private String name ;
    @Value("${myage}")
    private int age;
```



# aop  （Aspect Oriented Programming ）面向切面编程

 

**Spring aop 技术的实现是基于动态代理的：**

jdk动态代理：基于接口的动态代理技术

cglib动态代理：基于父类的动态代理技术

在 spring 中，框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式



**Spring AOP技术的实现：**

Spring 框架监控切入点方法的执行。一旦监控到切入点方法被运行，使用代理机制，动态创建目标对象的 

代理对象，根据通知类别，在代理对象的对应位置，将通知对应的功能织入，完成完整的代码逻辑运行。



**aop的重要概念：**

Pointcut（切入点）：被增强的方法（目标方法） 

Advice（通知/ 增强）：封装增强业务逻辑的方法 

Aspect（切面）：切点+通知 

Weaving（织入）：将切点与通知结合的过程





## aop切点表达式

**写法**

访问修饰符可以省略 

 返回值类型、包名、类名、方法名可以使用星号* 代表任意 

 包名与类名之间一个点 . 代表当前包下的类，两个点 .. 表示当前包及其子包下的类 

 参数列表可以使用两个点 .. 表示任意个数，任意类型的参数列表

```java
execution(public void com.itheima.aop.Target.method())
execution(void com.itheima.aop.Target.*(..))
execution(* com.itheima.aop.*.*(..))
execution(* com.itheima.aop..*.*(..))
execution(* *..*.*(..))
```

## aop 通知

aop的通知：控制增强方法执行是的位置（时机）

**通知的类型**

前置通知 (before): 指定增强方法在被增强方法之前执行

后置通知 (after-returning):指定增强方法在被增强方法之后执行

环绕通知(around):指定增强方法在被增强方法之前、之后都执行执行

异常抛出通知(throwing):指定被增强方法抛出异常是执行

最终通知(after):无论被增强方法执行是否有异常都会执行



## Spring aop 入门代码案例（基于xml配置）





**步骤：**

① 导入 AOP 相关坐标 

② 创建目标接口和目标类（内部有切点） 

③ 创建切面类（内部有增强方法） 

④ 将目标类和切面类的对象创建权交给 spring 

⑤ 在 applicationContext.xml 中配置织入关系 

⑥ 测试代码



导入 AOP 相关坐标 

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.ajs</groupId>
    <artifactId>SpringAop01</artifactId>
    <version>1.0</version>

    <name>SpringAop01</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>9.0.4</maven.compiler.source>
        <maven.compiler.target>9.0.4</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
        <!--导入spring的context坐标，context依赖aop-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.5.RELEASE</version>
        </dependency>
        <!-- aspectj的织入 aop框架-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.13</version>
        </dependency>
    </dependencies>


</project>

```

② 创建目标接口和目标类（内部有切点） 

③ 创建切面类（内部有增强方法） 

```java
package com.ajs.aop;

/**
 * 主业务方法的接口
 */
public interface TargetInterface {
    public void doSome();
}


package com.ajs.aop.Impl;

import com.ajs.aop.TargetInterface;

/**
 * 目标方法：被增强
 * 内部方法是被增强方法（切点）
 */
public class Target implements TargetInterface {

    //切点
    @Override
    public void doSome() {
        System.out.println("主业务逻辑方法");
    }
}



package com.ajs.aop.myAdivce;

/**
 * 切面类：增强功能
 * 内部方法是增强功能
 */
public class TargetAdivce {
    public void before(){
        System.out.println("前置增强。。。。");
    }

}

```

④ 将目标类和切面类的对象创建权交给 spring 

⑤ 在 applicationContext.xml 中配置织入关系 

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd
http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop.xsd
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd">


<!--向Spring容器中创建 目标类和切面类-->
    <bean id="target" class="com.ajs.aop.Impl.Target"></bean>
    <bean id="targetAdivce" class="com.ajs.aop.myAdivce.TargetAdivce"></bean>

    <!--配置切点表达式和前置增强的织入关系-->
    <aop:config>
        <!--声明切面-->
        <aop:aspect ref="targetAdivce">
        <!--通知+切点：配置被增强放和增强方法的织入关系 -->

            <!--
            before:前置通知
            method="before"：切面类的增强方法
            pointcut="execution(public void com.ajs.aop.Impl.Target.doSome())：切入点表达式
            com.ajs.aop.Impl.Target.doSome()：被增强的方法（目标类的方法）
            -->
            <aop:before method="before" pointcut="execution(public void
            com.ajs.aop.Impl.Target.doSome())"></aop:before>

        </aop:aspect>

    </aop:config>

</beans>
```

⑥ 测试代码

```java
public class myTest {
    @Test
    public void test01(){
        ApplicationContext ctx=
                new ClassPathXmlApplicationContext("applicationContext.xml");
        
        //注意：这里有接口，所以底层使用jdk动态代理，所以返回值类型要使用接口接收
        TargetInterface target = (TargetInterface) ctx.getBean("target");
            target.doSome();

    }
```



## 切点表达式抽取

```xml
<aop:config>
<!--引用myAspect的Bean为切面对象--> <aop:aspect ref="myAspect"> <aop:pointcut id="myPointcut" expression="execution(* com.itheima.aop.*.*(..))"/>
    
    
<aop:before method="before" pointcut-ref="myPointcut"></aop:before>
<aop:after_returning method="before" pointcut-ref="myPointcut"></aop:after_returning>
</aop:aspect>
</aop:config>
```





## 更多代码展示

```java

!--Spring容器中创建目标对象和切面类对象-->
    <bean id="target" class="com.ajs.aop.Impl.Target"></bean>
    <bean id="targetAdivce" class="com.ajs.aop.myAdivce.TargetAdivice"></bean>

    <!--织入增强方法和被增强方法的关系-->
    <aop:config>
        <aop:aspect ref="targetAdivce">
            <!--&lt;!&ndash;前置通知&ndash;&gt;
            <aop:before method="showBrfoer" pointcut="execution(
            public void com.ajs.aop.Impl.Target.doSome())"></aop:before>

            &lt;!&ndash;前置通知&ndash;&gt;
            <aop:after-returning method="showAfterReturning" pointcut="execution(
            public void com.ajs.aop.Impl.Target.doSome())"></aop:after-returning>

            &lt;!&ndash;环绕通知&ndash;&gt;
            <aop:around method="showAroud" pointcut="execution(
            public void com.ajs.aop.Impl.Target.doSome())"></aop:around>-->

            <!--切点表达式抽取-->
            <aop:pointcut id="myPointcut" expression="execution(
            * *..aop.Impl.*.*(..))"/>

       <!-- &lt;!&ndash;环绕通知&ndash;&gt;
            <aop:around method="showAroudObj" pointcut="execution(
            * *..aop.Impl.*.*(..))"></aop:around>-->

            <!--环绕通知-->
            <aop:around method="showAroudObj" pointcut-ref="myPointcut"></aop:around>

        </aop:aspect>
    </aop:config>





import org.aspectj.lang.ProceedingJoinPoint;

public class TargetAdivice {
    public void showBrfoer(){
        System.out.println("前置增强....");
    }

    public void showAfterReturning(){
        System.out.println("后置增强....");
    }

    /**
     * 环绕通知增强方法
     * @param pjp 切点 被增强方法
     * @throws Throwable
     */
    public void showAroud(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("环绕前增强");
         pjp.proceed();
        System.out.println("环绕后增强");

    }


    public Object showAroudObj(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("环绕前增强");
        Object proceed = pjp.proceed();
        System.out.println("环绕后增强");
        return proceed;

    }
}
```

## Spring aop 注解代码案例

接口、实现类（目标类）、切面类

```java
package com.ajs.aop;

public interface TargetInterface {
    void doSome();
}

package com.ajs.aop.Impl;

import com.ajs.aop.TargetInterface;
import org.springframework.stereotype.Component;

@Component("targetImpl")
public class TargetImpl implements TargetInterface {
    @Override
    public void doSome() {
        System.out.println("主方法！！！");
    }


}




package com.ajs.aop.myAdivce;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component("AdivceClass")
@Aspect
public class AdivceClass {
    @Before("execution(* *..aop.Impl.*.*(..))")
    public void beforeMethod() {
        System.out.println("前置通知");
    }

    @AfterReturning("execution(* *..aop.Impl.*.*(..))")
    public void afterReturning() {
        System.out.println("后置通知");
    }
@Around("execution(* *..aop.Impl.*.*(..))")
    public void around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("环绕通知1");
        pjp.proceed();
        System.out.println("环绕通知2");
    }


@After("mypt()")
    public void after() throws Throwable {
    System.out.println("我是最终方法！！！");
    }
    /**
     * @Pointcut: 定义和管理切入点，不是通知注解。
     *     属性： value 切入点表达式
     *     位置： 在一个自定义方法的上面， 这个方法看做是切入点表达式的别名。
     *           其他的通知注解中，可以使用方法名称，就表示使用这个切入点表达式了
     */
    //注解表达式抽取方法
    @Pointcut("execution(* *..SomeServiceImpl.doThird(..))")
    private void mypt(){
        //无需代码
    }

}



```



Spring 配置文件

```java
//Spring IOC注解扫描器
<context:component-scan base-package="com.ajs.aop"/>
    
     <!--声明自动代理生成器：目的是创建目标对象的代理（就是06项目中的ServiceProxy）
        调用aspectj框架中的功能， 寻找spring容器中的所有目标对象，
        把每个目标对象加入切面类中的功能， 生成代理。
        这个代理对象是修改的内存中的目标对象， 这个目标对象就是代理对象（ServiceProxy）
    -->
    <aop:aspectj-autoproxy />
```







## Spring aop总结和注意点

1、在测试代码是获取对象的返回值是目标对象有接口，底层是使用jdk动态代理，也就是Spring 容器要执行aop

实现目标对象的增强功能，从Spring容器中获取对象的类型是接口类型的。

没有目标对象没有实现接口，底层实现cglib动态代理，Spring容器获取的对象类型是目标方法类型的

2、在切面类中的方法中，也就是增强功能。这个方法里传参ProceedingJoinPoint这个接口，获取目标对象方法（被增强的方法）。







# Spring 整合MyBatis

**Spring-MyBatis：**

思路：把mybatis中的对象交给Spring托管，也就是把mybatis对象的创建由Spring创建放入到Spring容器中,使用了Spring的IOC核心框架

**spring整合mybatis的过程参照以下的流程 **

1、声明数据源对象 ：声明数据源可以有多种连接吃池方式，这里用了阿里巴巴的连接池：com.alibaba.druid.pool.DruidDataSource

2、SqlSessionFactoryBean,在这个类的内部，创建SqlSessionFactory

3、配置SqlSessionTemplate对象，相当于SqlSession对象

注意：com.alibaba.druid.pool.DruidDataSource 依赖与jdbc驱动的版本关系

## Spring-MyBatis方式一：

**dao接口实现类注入SqlSessionTemplate**

pom.xml

注意：在配置此文件是要注意各依赖之间的版本关系

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.ajs</groupId>
  <artifactId>SpringMyBatis01</artifactId>
  <version>1.0</version>


  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>9.0.4</maven.compiler.source>
    <maven.compiler.target>9.0.4</maven.compiler.target>
  </properties>

  <dependencies>
    <!-- spring相关jar -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <!-- junit测试jar -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>

    <!-- aspectjweaver面向切面相关jar -->
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.6</version>
      <scope>runtime</scope>
    </dependency>

    <!-- mysql驱动jar -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.9</version>
    </dependency>

    <!-- spring jdbc支持的jar -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.0.RELEASE</version>
    </dependency>

    <!-- lombok支持的jar -->
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.16</version>
    </dependency>

    <!-- mybatis的jar -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.6</version>
    </dependency>

    <!-- spring整合mybatis的jar-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>2.0.6</version>
    </dependency>
    <!--阿里的连接池-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.12</version>
    </dependency>
  </dependencies>

  <build>
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

定义dao接口、dao接口实现类、实体类

```java
package com.ajs.dao;
import com.ajs.Entity.Student;
import java.util.List;
public interface daoStudent {
    List<Student> selectAll();
}


package com.ajs.dao.Impl;
import com.ajs.Entity.Student;
import com.ajs.dao.daoStudent;
import org.mybatis.spring.SqlSessionTemplate;
import java.util.List;
public class daoStudentImpl implements daoStudent {
    private SqlSessionTemplate sqlSession;

   /* public void setSqlSession(SqlSessionTemplate sqlSession) {
        SqlSession = sqlSession;
    }*/

    public daoStudentImpl(SqlSessionTemplate sqlSessionTemplate) {
        sqlSession = sqlSessionTemplate;
    }

    @Override
    public List<Student> selectAll() {
        System.out.println(sqlSession);
        daoStudent dao = sqlSession.getMapper(daoStudent.class);
        return dao.selectAll();
    }
}


package com.ajs.Entity;
public class Student {
    private int id;
    private String name;
    private int age;
    private char sex;
    private String sclass;

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", sex=" + sex +
                ", sclass='" + sclass + '\'' +
                '}';
    }

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

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public char getSex() {
        return sex;
    }

    public void setSex(char sex) {
        this.sex = sex;
    }

    public String getSclass() {
        return sclass;
    }

    public void setSclass(String sclass) {
        this.sclass = sclass;
    }

    public Student(int id, String name, int age, char sex, String sclass) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.sex = sex;
        this.sclass = sclass;
    }

    public Student() {
    }
}

```

Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ajs.dao.daoStudent" >

    <select id="selectAll" resultType="com.ajs.Entity.Student">
        select *from student
    </select>

</mapper>
```



Mybatis主配置文件：配置MyBatis.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 开启日志 -->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <mappers>
        <mapper resource="com\ajs\dao\daoStudent.xml"/>
    </mappers>



</configuration>

```

Spring配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">




    <!--声明数据源DataSource-->
    <!--声明数据源DataSource-->
    <bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="jdbc:mysql://localhost:3306/ajsnode_mybatis"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>


    <!--声明SqlSessionFactoryBean,在这个类的内部，创建SqlSessionFactory-->
    <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--指定数据源-->
        <property name="dataSource" ref="myDataSource" />
        <!--指定mybatis主配置文件
            Resource可以直接使用 value属性赋值。
        -->
        <property name="configLocation" value="classpath:MyBatis.xml" />
    </bean>
    <!--
      SqlSessionFactory  factory  = new SqlSessonFactoryBuider.build(classpath:mybatis.xml)
    -->


    <!--声明MapperScannerConfigurer
        SqlSession.getMapper(StudentDao.class)

        MapperScannerConfigurer作用是：

        循环basePackage所表示的包，把包中的每个接口都找到，调用SqlSession.getMapper
        把每个dao接口都创建出dao对象 ，dao代理对象放在容器中。


        ApplicationContext ctx = ....

        SqlSessionFactory sqlSessionFactory  = ctx.getBean("factory");
        SqlSession session = sqlSessionFactory.openSession();

        for(接口： com.bjpowernode.dao){

             接口 对象 =  session.getMapper(接口)
             springMap.put(接口名的首字母小写， 对象)

        }

    -->
    <!--<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        &lt;!&ndash;指定SqlSessionFactory对象的名称&ndash;&gt;
        <property name="sqlSessionFactoryBeanName" value="factory" />
        &lt;!&ndash;指定基本包，dao接口所在的包名&ndash;&gt;
        <property name="basePackage" value="com.ajs.dao" />
    </bean>-->


    <!-- 配置SqlSessionTemplate对象，相当于SqlSession对象 -->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg name="sqlSessionFactory" ref="factory"/>
    </bean>

    <!--该方式需要在配置sqlSessionFactory时引入mapper文件的路径, 不然会找不到对应的mapper配置文件-->
    <!-- 配置UserMapper接口的实现类对象，注入sqlSessionTemplate对象 -->
    <bean id="daoStudentImpl" class="com.ajs.dao.Impl.daoStudentImpl">
        <!-- 注入sqlSessionTemplate -->
        <constructor-arg name="sqlSessionTemplate" ref="sqlSession"/>
    </bean>


</beans>
```

测试：

```java
package com.ajs;

import com.ajs.Entity.Student;
import com.ajs.dao.Impl.daoStudentImpl;
import com.ajs.dao.daoStudent;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.Test;
import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.util.List;

public class MyTest {
    @Test
    public void test(){
        ApplicationContext ctx=
                new ClassPathXmlApplicationContext("applicationCotect.xml");
        daoStudentImpl daoStudentImpl = (com.ajs.dao.Impl.daoStudentImpl) ctx.getBean("daoStudentImpl");
        List<Student> students = daoStudentImpl.selectAll();
        for (Student student:students
             ) {
            System.out.println(student);
        }



    }


    @Test
    public void test01(){
        ApplicationContext ctx=
                new ClassPathXmlApplicationContext("applicationCotect.xml");
        SqlSessionFactory factory = (SqlSessionFactory) ctx.getBean("factory");
        SqlSession sqlSession = factory.openSession();
        daoStudent dao = sqlSession.getMapper(daoStudent.class);
        List<Student> students = dao.selectAll();
        for (Student student:students
             ) {
            System.out.println(student);
        }

    }

    @Test
    public void test02(){
        ApplicationContext ctx=
                new ClassPathXmlApplicationContext("applicationCotect.xml");
        SqlSessionTemplate sqlSession = (SqlSessionTemplate) ctx.getBean("sqlSession");
        daoStudent dao = sqlSession.getMapper(daoStudent.class);
        System.out.println(dao);
        List<Student> students = dao.selectAll();
        System.out.println(students);


    }
}

```



## Spring-MyBatis方式二：

**dao接口实现类继承 SqlSessionDaoSupport  **

Spring 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">




    <!--声明数据源DataSource-->
    <!--声明数据源DataSource-->
    <bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="jdbc:mysql://localhost:3306/ajsnode_mybatis"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>


    <!--声明SqlSessionFactoryBean,在这个类的内部，创建SqlSessionFactory-->
    <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--指定数据源-->
        <property name="dataSource" ref="myDataSource" />
        <!--指定mybatis主配置文件
            Resource可以直接使用 value属性赋值。
        -->
        <property name="configLocation" value="classpath:MyBatis.xml" />
    </bean>
    <!--声明dao接口实现类
        -->
<bean id="daoStudentImpl" class="com.ajs.dao.Impl.daoStudentImpl">
    <property name="sqlSessionFactory" ref="factory"/>

</bean>


</beans>
```

dao接口实现类：

```java
public class daoStudentImpl extends SqlSessionDaoSupport implements daoStudent {
    @Override
    public List<Student> selectAll() {
        SqlSession sqlSession = getSqlSession();
        return sqlSession.getMapper(daoStudent.class).selectAll();
    }
}
```



测试：

```java

public class MyTest {
    @Test
    public void Test01(){
        ApplicationContext ctx=
                new ClassPathXmlApplicationContext("applicationCotect.xml");
        daoStudentImpl daoStudentImpl = (com.ajs.dao.Impl.daoStudentImpl) ctx.getBean("daoStudentImpl");
        List<Student> students = daoStudentImpl.selectAll();
        for(Student student:students){
            System.out.println(student);
        }
    }
}

```



## Spring-MyBatis方式三：

**dao接口没有实现类，实现类在业务类中注入**

Spring配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">




    <!--声明数据源DataSource-->
    <!--声明数据源DataSource-->
    <bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="jdbc:mysql://localhost:3306/ajsnode_mybatis"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>


    <!--声明SqlSessionFactoryBean,在这个类的内部，创建SqlSessionFactory-->
    <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--指定数据源-->
        <property name="dataSource" ref="myDataSource" />
        <!--指定mybatis主配置文件
            Resource可以直接使用 value属性赋值。
        -->
        <property name="configLocation" value="classpath:MyBatis.xml" />
    </bean>
    <!--
         SqlSessionFactory  factory  = new SqlSessonFactoryBuider.build(classpath:mybatis.xml)
       -->


    <!--声明MapperScannerConfigurer
        SqlSession.getMapper(StudentDao.class)

        MapperScannerConfigurer作用是：

        循环basePackage所表示的包，把包中的每个接口都找到，调用SqlSession.getMapper
        把每个dao接口都创建出dao对象 ，dao代理对象放在容器中。


        ApplicationContext ctx = ....

        SqlSessionFactory sqlSessionFactory  = ctx.getBean("factory");
        SqlSession session = sqlSessionFactory.openSession();

        for(接口： com.bjpowernode.dao){

             接口 对象 =  session.getMapper(接口)
             springMap.put(接口名的首字母小写， 对象)

        }

    -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--指定SqlSessionFactory对象的名称-->
        <property name="sqlSessionFactoryBeanName" value="factory" />
        <!--指定基本包，dao接口所在的包名-->
        <property name="basePackage" value="com.ajs.dao" />
    </bean>

    <!--声明service对象-->
    <bean id="serivceStudent" class="com.ajs.service.serivceStudent">
        <property name="daoStu" ref="daoStudent"/>
    </bean>

</beans>
```



dao接口，service类、测试类

```java
public interface daoStudent {
    //查询所有学生
    List<Student> selectAll();
}

public class serivceStudent {
    private daoStudent daoStu;

    public void setDaoStu(daoStudent daoStu) {
        this.daoStu = daoStu;
    }
    //业务 selectAll
    public List<Student> selectAll(){

        return daoStu.selectAll();
    }

}

public class MyTest {
    @Test
    public void Test01(){
        ApplicationContext ctx=
                new ClassPathXmlApplicationContext("applicationCotect.xml");
        serivceStudent serivceStudent = (com.ajs.service.serivceStudent) ctx.getBean("serivceStudent");
        List<Student> students = serivceStudent.selectAll();
        for(Student student:students){
            System.out.println(student);
        }

    }
}

```





# Spring事务

## 事务管理器接口PlatformTransactionManager

Spring框架使用事务管理器对象，管理所有的事务。

事务管理器接口： PlatformTransactionManager

​                   作用： 定义了事务的操作， 主要是commit(), rollback()



spring统一管理事务， 把不同的数据库访问技术的事务处理统一起来。

管理事务面向的是spring， 有spring管理事务，做事务提交，事务回顾。



**事务的提交和回滚的时机**

 当你的业务方法正常执行时，没有异常，事务是提交的。 如果你的业务方法抛出了运行时异常， 事务是回滚的。



** Spring 事务技术实现：**

环绕通知：可以在目标方法的前和后都能增强功能，不需要修改代码代码



## 事务定义接口TransactionDefinition

TransactionDefinition接口。定义了三类常量， 定义了有关事务控制的属性。

事务的属性：1） 隔离级别  2）传播行为 3）事务的超时

#### 5.4.1 隔离级别

隔离级别：控制事务之间影响的程度。

5个值，只有四个隔离级别

1）DEFAULT ： 采 用 DB 默 认 的 事 务 隔 离 级 别 。 MySql 的 默 认 为  REPEATABLE_READ； 

​      Oracle 默认为 READ_COMMITTED。

2）READ_UNCOMMITTED：读未提交。未解决任何并发问题。
3）READ_COMMITTED：读已提交。解决脏读，存在不可重复读与幻读。
4）REPEATABLE_READ：可重复读。解决脏读、不可重复读，存在幻读
5）SERIALIZABLE：串行化。不存在并发问题。

#### 5.4.2 超时时间

超时时间，以秒为单位 。 整数值。 默认是 -1

超时时间：表示一个业务方法最长的执行时间，没有到达时间没有执行完毕，spring回滚事务。

#### 5.4.3 传播行为

传播行为有7个值。 

传播行为：业务方法在调用时，事务在方法之间的，传递和使用。

使用传播行为，标识方法有无事务。

PROPAGATION_REQUIRED
PROPAGATION_REQUIRES_NEW
PROPAGATION_SUPPORTS

以上三个需要掌握的。

PROPAGATION_MANDATORY
PROPAGATION_NESTED
PROPAGATION_NEVER
PROPAGATION_NOT_SUPPORTED

1） REQUIRED: spring默认传播行为， 方法在调用的时候，如果存在事务就是使用当前的事务，如果没有事务，则新建事务， 方法在新事务中执行。

2） SUPPORTS：支持， 方法有事务可以正常执行，没有事务也可以正常执行。

3）REQUIRES_NEW：方法需要一个新事务。 如果调用方法时，存在一个事务，则原来的事务暂停。 直到新事务执行完毕。  如果方法调用时，没有事务，则新建一个事务，在新事务执行代码





##  Spring框架使用自己的注解@Transactional控制事务

**事务的控制模式**

1. 编程式，在代码中编程控制事务。
2. 声明式事务。不用编码，以下使用的方式

​	属性： 

​     1. propagation ： 事务的传播行为， 他使用的 Propagation类的枚举值。例如 Propagation.REQUIRED

​     2.isolation ： 表示隔离级别， 使用Isolation类的枚举值，表示隔离级别。 默认 Isolation.DEFAULT

​     3.readOnly: boolean类型的值，表示数据库操作是不是只读的。默认是false

​     4.timeout： 事务超时，默认是-1， 整数值，单位是秒。 例如 timeout=20

​     5.rollbackFor：表示回滚的异常类列表， 他的值是一个数组，每个值是异常类型的class。 

​     6.rollbackForClassName：表示回滚的异常类列表,他的值是异常类名称，是String类型的值

​     7.noRollbackFor： 不需要回滚的异常类列表。 是class类型的。

​     8.noRollbackForClassName： 不需要回滚的异常类列表，是String类型的值

 位置：1）在业务方法的上面， 是在public方法的上面

​             2） 在类的上面。

##  注解@Transactional的注意事项

  ```java
//@Transactional 放在public方法的上面。表示方法有事务功能
    /*
    第一种设置方式
    @Transactional(
            propagation = Propagation.REQUIRED,
            isolation = Isolation.DEFAULT,
            readOnly = false, timeout = 20,
            rollbackFor = {NullPointerException.class,NotEnougthException.class})
     */


    /*
    第二种设置方式
     @Transactional(
            propagation = Propagation.REQUIRED,
            isolation = Isolation.DEFAULT,
            readOnly = false, timeout = 20
     )

     解释 rollbackFor 的使用；
     1）框架首先检查方法抛出的异常是不是在 rollbackFor 的数组中， 如果在一定回滚。
     2）如果方法抛出的异步不在 rollbackFor 数组， 框架会继续检查 抛出的异常 是不是 RuntimeException.
        如果是RuntimeException， 一定回滚。


     例如 抛出 SQLException , IOException
     rollbackFor={SQLException.class, IOException.class}
     */


    //第三种方式： 使用默认值 REQUIRED ， 发生运行时异常回滚。
  ```



## Spring事务@Transactional注解的入门案例

业务描述：

数据可表如下

数据库中2个用户转账是使用Spring事务控制

+----+--------+---------+
| id | name   | balance |
+----+--------+---------+
|  1 | 李四   |    1000      |
|  2 | 王五   |       0         |
+----+--------+---------+

pom.xml配置文件使用Spring-mybatis中的环境

Spring 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">



    <!--声明数据源DataSource-->
    <!--声明数据源DataSource-->
    <bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="jdbc:mysql://localhost:3306/ajsnode_mybatis"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>


    <!--声明SqlSessionFactoryBean,在这个类的内部，创建SqlSessionFactory-->
    <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--指定数据源-->
        <property name="dataSource" ref="myDataSource" />
        <!--指定mybatis主配置文件
            Resource可以直接使用 value属性赋值。
        -->
        <property name="configLocation" value="classpath:MyBatis.xml" />
    </bean>
    <!--获取dao对象-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--指定SqlSessionFactory对象的名称-->
        <property name="sqlSessionFactoryBeanName" value="factory" />
        <!--指定基本包，dao接口所在的包名-->
        <property name="basePackage" value="com.ajs.dao" />
    </bean>

    <!--业务类实现类对象-->
    <bean id="serviceImpl" class="com.ajs.Service.Impl.ServiceImpl">
        <property name="Userdao" ref="userDao"/>
    </bean>

    <!--声明事务的控制-->
    <!--声明事务管理器-->
    <bean id="transactionManager"  class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--指定数据源DataSource-->
        <property name="dataSource" ref="myDataSource" />
    </bean>



    <!--开启事务注解驱动： 告诉框架使用注解管理事务
        transaction-manager:指定事务管理器的id
    -->
    //注意这里约束引得是末尾带tx的
    <tx:annotation-driven transaction-manager="transactionManager" />
</beans>
```



dao接口、业务类接口、业务类 、测试类

```java
public interface UserDao {
    //转账
    int updateDown(@Param("id") int id,
                   @Param("balance") double balance);
    //收钱
    int updateAdd(@Param("id") int id,
                  @Param("balance") double balance);
}

public interface serviceInterface {
    /**
     * 转账业务方法
     * @param balance 转账金额
     * @param dId 转账用户的id
     * @param aId 收钱用户的id
     */
    public void transfer(double balance,int dId,int aId);

}

public class ServiceImpl implements serviceInterface {
    private UserDao userdao;

    public void setUserdao(UserDao userdao) {
        this.userdao = userdao;
    }

    /**
     *
     * @param balance 转账金额
     * @param dId 转账用户的id
     * @param aId 收钱用户的id
     */


    //添加Spring事务
   /* @Transactional*/
    @Transactional

    @Override
    public void transfer(double balance, int dId, int aId) {
        userdao.updateDown(dId, balance);
        String a=null;
        a.toString();
        userdao.updateAdd(aId,balance);

    }
}


public class MyTest {
    @Test
    public void test01(){
        ApplicationContext ctx=
                new ClassPathXmlApplicationContext("applicationContext.xml");
        serviceInterface serviceImpl = (serviceInterface) ctx.getBean("serviceImpl");
        System.out.println(serviceImpl);
        serviceImpl.transfer(100,2,1);


    }
}
```



# Spring-web

**执行思路：**

把Spring容器中的对象存入到Tomcat服务器中来提高对象访问的效率，优化内存

思路：创建ServletContextListener监听器。在Tomcat服务器启动的时候把Spring容器存入到ServletContext作用域当中。形成一个新的web容器，有请求的时候执行从Servlet作用域中拿对象



执行步骤：

1、环境搭建，这里省略

2、创建监听器

```java
package com.ajs.ajsListener;
import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * 监听器：ServletContextListener
 * 当Taomcat服务器启动的时候会创建ServletContext作用域
 * ServletContextListener监听器 监听servletContext作用域的创建，执行监听器
 *
 */
public class SpringlistenerContext implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ApplicationContext app=
                new ClassPathXmlApplicationContext("applicationContext.xml");
        ServletContext servletContext = sce.getServletContext();
        servletContext.setAttribute("app",app);
        System.out.println("spring容器创建完毕");
    }
}

```



3、web.xml中配置监听器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
<servlet>
    <servlet-name>QueryAll</servlet-name>
    <servlet-class>com.ajs.Controller.StudentServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>QueryAll</servlet-name>
    <url-pattern>/q</url-pattern>
</servlet-mapping>

    <context-param>
        <!--
          contextConfigLocation：名称是固定的， 表示自定义spring配置文件的路径
        -->
        <param-name>contextConfigLocation</param-name>
        <!--自定义配置文件的路径-->
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    
    
    
    
    
    
<!--注册监听器-->
    <listener>
        <listener-class>com.ajs.ajsListener.SpringlistenerContext</listener-class>
    </listener>



</web-app>

```



























































