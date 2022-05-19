# 我的MyBatispom.xml 常用配置

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>9.0.4</maven.compiler.source>
    <maven.compiler.target>9.0.4</maven.compiler.target>
  </properties>

  <dependencies>
    <!--mybatis依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.9</version>
    </dependency>

    <!--mysql驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.27</version>
    </dependency>

    <!--单元测试依赖-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <!--资源插件： 处理src/main/java目录中的xml-->
    <resources>
      <resource>
        <directory>src/main/java</directory><!--所在的目录-->
        <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
    </resources>
  </build>
```



# MyBatis 入门案例

1、在Maven项目的配置文件中添加Mybatis依赖

2、创建mybatis主配置

3、mapper 配置文件





**注意点：**

这里没有dao接口，mybatis使用通过配置文件来执行sql语句，也就是是底层没有用到jdk动态代理。



**pom.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!--当前项目的坐标-->
  <groupId>com.bjpowernode</groupId>
  <artifactId>ch01-first</artifactId>
  <version>1.0</version>


  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

  <!--依赖列表-->
  <dependencies>
    <!--mybatis依赖-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.1</version>
    </dependency>

    <!--mysql驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.9</version>
    </dependency>


    <!--单元测试-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>

    <!--资源插件： 处理src/main/java目录中的xml-->
    <resources>
      <resource>
        <directory>src/main/java</directory><!--所在的目录-->
        <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
    </resources>
  </build>
</project>

```

**主配置文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--设置日志-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <!--配置数据源： 创建Connection对象。-->
            <dataSource type="POOLED">
                <!--driver:驱动的内容-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <!--连接数据库的url-->
                <property name="url" value="jdbc:mysql://localhost:3306/springdb?useUnicode=true&amp;characterEncoding=utf-8"/>
                <!--用户名-->
                <property name="username" value="root"/>
                <!--密码-->
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定其他mapper文件的位置：
        其他其他mapper文件目的是找到其他文件的sql语句
    -->
    <mappers>
        <!--
           使用mapper的resource属性指定mapper文件的路径。
           这个路径是从target/classes路径开启的

           使用注意：
              resource=“mapper文件的路径，使用 / 分割路径”
              一个mapper resource 指定一个mapper文件
        -->
        <mapper resource="com/bjpowernode/dao/StudentDao.xml"/>

        <!--<mapper resource="com/bjpowernode/dao/OrderDao.xml"/>
        <mapper resource="com/bjpowernode/dao/UserDao.xml"/>-->
    </mappers>
</configuration>
```

**Mapper配置文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.bjpowernode.dao.StudentDao">
<!--    <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>-->

    <!--查询一个学生Student

        <select>：表示查询操作， 里面是select 语句
        id: 要执行的sql语句的唯一标识， 是一个自定义字符串。
            推荐使用dao接口中的方法名称
        resultType:告诉mybatis，执行sql语句，把数据赋值给那个类型的java对象。
              resultType的值现在使用的java对象的全限定名称


        #{studentId}:占位符， 表示从java程序中传入过来的数据
    -->
    <select id="selectStudentById" resultType="com.bjpowernode.domain.Student">
         select id,name,email,age from student where id = #{studentId}
    </select>


    <!--添加insert
       insert into student values(1003,"李峰","lifeng@qq.com",26)

       如果传入给mybatis是一个java对象，使用#{属性名} 获取此属性的值。
       属性值放到 #{}占位符的位置, mybatis执行此属性 对应的getXXX().
       例如 #{id} ，执行getId();
    -->
    <insert id="insertStudent">
       insert into student values(#{id},#{name},#{email},#{age})
    </insert>
</mapper>

<!--
  1.约束文件
    http://mybatis.org/dtd/mybatis-3-mapper.dtd
    约束文件作用： 定义和限制当前文件中可以使用的标签和属性，以及标签出现的顺序。

  2.mapper是根标签
    namespace： 命名空间，必须有值，不能为空。唯一值。
                推荐使用Dao接口的全限定名称。
    作用： 参与识别sql语句的作用。

  3.在mapper里面可以写 <insert>,<update>,<delete>,<select>等标签。
    <insert>里面是 insert语句，表示执行的insert操作
    <update>里面是 update语句
    <delete>里面是 delete语句
    <select> 里面是 select语句
-->
```



**dao接口**

```java
package com.bjpowernode.dao;

import com.bjpowernode.domain.Student;

/**
 *
 */
public interface StudentDao {

    //查询一个学生
    Student selectStudentById(Integer id);


    //添加学生
    //返回值int：表示本次操作影响的数据库的行数
    int insertStudent(Student student);
}

```

**测试类**

```java
package com.bjpowernode;

import com.bjpowernode.domain.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

/**
 *
 */
public class MyTest {


    //测试mybatis执行sql语句
    @Test
    public void testSelectStudentById() throws IOException {
        //调用mybatis某个对象的方法，执行mapper文件中的sql语句
        //mybatis核心类： SqlSessionFactory

        //1.定义mybatis主配置文件的位置, 从类路径开始的相对路径
        String config="mybatis.xml";
        //2.读取主配置文件。使用mybatis框架中的Resources类
        InputStream inputStream = Resources.getResourceAsStream(config);
        //3.创建SqlSessionFactory对象， 使用SqlSessionFactoryBuidler类
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);

        //4.获取SqlSession对象。
        SqlSession session = factory.openSession();

        //5.指定要执行的sql语句的 id
        //  sql的id = namespace+"."+ select|update|insert|delete标签的id属性值
        String sqlId = "com.bjpowernode.dao.StudentDao"+"."+"selectStudentById";

        // 6.通过SqlSession的方法，执行sql语句
        Student student = session.selectOne(sqlId);
        System.out.println("使用mybatis查询一个学生："+student);

        // 7.关闭SqlSession对象
        session.close();

    }

    //测试mybatis执行sql语句
    @Test
    public void testSelectStudentById2() throws IOException {
        //调用mybatis某个对象的方法，执行mapper文件中的sql语句
        //mybatis核心类： SqlSessionFactory

        //1.定义mybatis主配置文件的位置, 从类路径开始的相对路径
        String config="mybatis.xml";
        //2.读取主配置文件。使用mybatis框架中的Resources类
        InputStream inputStream = Resources.getResourceAsStream(config);
        //3.创建SqlSessionFactory对象， 使用SqlSessionFactoryBuidler类
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);

        //4.获取SqlSession对象。
        SqlSession session = factory.openSession();

        //5.指定要执行的sql语句的 id
        //  sql的id = namespace+"."+ select|update|insert|delete标签的id属性值
        String sqlId = "com.bjpowernode.dao.StudentDao"+"."+"selectStudentById";


        // 6.通过SqlSession的方法，执行sql语句
        Student student = session.selectOne(sqlId,1001);
        System.out.println("使用mybatis查询一个学生："+student);

        // 7.关闭SqlSession对象
        session.close();

    }

    @Test
    public void testInsertStudent() throws IOException {

        String config="mybatis.xml";
        InputStream inputStream = Resources.getResourceAsStream(config);
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession session = factory.openSession();

        //5.指定要执行的sql语句的 id
        //  sql的id = namespace+"."+ select|update|insert|delete标签的id属性值
        String sqlId = "com.bjpowernode.dao.StudentDao"+"."+"insertStudent";

        // 6.通过SqlSession的方法，执行sql语句
        Student student  = new Student();
        student.setId(1006);
        student.setName("张飞");
        student.setEmail("zhangfie@qq.com");
        student.setAge(26);

        int rows = session.insert(sqlId,student);
        System.out.println("使用mybatis添加一个学生，rows="+rows);

        //mybatis默认执行sql语句是  手工提交事务 模式， 在做insert，update ，delete 后需要提交事务。
        session.commit();

        // 7.关闭SqlSession对象
        session.close();

    }


    @Test
    public void testAutoCommitInsertStudent() throws IOException {

        String config="mybatis.xml";
        InputStream inputStream = Resources.getResourceAsStream(config);
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession session = factory.openSession(true);

        //5.指定要执行的sql语句的 id
        //  sql的id = namespace+"."+ select|update|insert|delete标签的id属性值
        String sqlId = "com.bjpowernode.dao.StudentDao"+"."+"insertStudent";

        // 6.通过SqlSession的方法，执行sql语句
        Student student  = new Student();
        student.setId(1007);
        student.setName("小乔");
        student.setEmail("zhangfie@qq.com");
        student.setAge(26);

        int rows = session.insert(sqlId,student);
        System.out.println("使用mybatis添加一个学生，rows="+rows);


        // 7.关闭SqlSession对象
        session.close();

    }
}

```

# mybatis执行流程图

![1650361091915](E:\建生litterNotBook\Typora_图片\1650361091915.png)

# Mapper 、dao接口、测试类（用户）三者的关系

![1650371737066](E:\建生litterNotBook\Typora_图片\1650371737066.png) 

# dao接口中方法传参

**传参总结**

单个传参用不用别名都可以

多个传参必须使用别名

对象类型的传参 Mapper中的参数名称和对象类的属性名称一样

Map集合类传参 Mapper中的参数是Map集合的key值



**dao接口**

```java
package com.ajs.dao;

import com.ajs.bean.User;
import org.apache.ibatis.annotations.Param;

import java.util.List;
import java.util.Map;

public interface daoUser {
    //无参传参
List<User> selectAll();

//单个传参
    User selectOne(int id);

    /*
    * 多个个传参必须起别名
    * */
    List<User> selectOr(@Param("myid") int id,
                        @Param("myname") String ename);

    /*
    * 对象传参
    * */

    User selctParamObj(User user);
    /*
    * 方法的参数是Map集合
    * */

    List<User> selectMap(Map<String,Object> map);
}

```

Mppar

```xml
<mapper namespace="com.ajs.dao.daoUser" >
    <select id="selectAll" resultType="com.ajs.bean.User">
        select*from ajs
    </select>
    <select id="selectOne" resultType="com.ajs.bean.User">
        select*from ajs where id=#{id}
    </select>

    <select id="selectOr" resultType="com.ajs.bean.User">
        select*from ajs where id=#{myid} or ename=#{myname}
    </select>

    <select id="selctParamObj" resultType="com.ajs.bean.User">
        select*from ajs where id=#{id}
    </select>


    <select id="selectMap" resultType="com.ajs.bean.User">
        select*from ajs where id=#{mapid} or ename=#{mapname}
    </select>
    </mapper>
```

test

```java
package com.ajs;

import com.ajs.bean.User;
import com.ajs.dao.daoUser;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.HashMap;
import java.util.List;

/**
 * 测试mybatis
 */
public class myTest01 {
    /*
     * 这里没有使用到动态代理模式，也就是可以不需要接口
     * */
    @Test
    public void test01() throws IOException {
        //1、读取mybatis主配置文件
        InputStream in = Resources.getResourceAsStream("mybatis.xml");
        //2、获取SeSessionFactory对象
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        //3、获取SqlSession对象
        SqlSession sqlSession = factory.openSession();
        //4、执行指定的sql语句
        List<User> userList = sqlSession.selectList("com.ajs.dao.daoUser.selectAll");
        System.out.println(userList);
        //close
        sqlSession.close();
        in.close();

    }

    @Test
    public void test02() throws IOException {
        /*
         * 加载主配置文件
         * 获取SqlsessionFactroy对象
         * 获取SqlSession对象
         * 创建dao对象
         * 执行sql语句
         * close
         * */

        InputStream in = Resources.getResourceAsStream("mybatis.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = factory.openSession();
        daoUser dao = sqlSession.getMapper(daoUser.class);
        User user = dao.selectOne(1);
        System.out.println(user);

        sqlSession.close();
        in.close();


    }

    @Test
    public void test03() throws IOException {
        /*
         * 加载主配置文件
         * 获取SqlsessionFactroy对象
         * 获取SqlSession对象
         * 创建dao对象
         * 执行sql语句
         * close
         * */

        InputStream in = Resources.getResourceAsStream("mybatis.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = factory.openSession();
        daoUser dao = sqlSession.getMapper(daoUser.class);
        List<User> userList = dao.selectOr(1, "lisi");
        for (User user : userList
        ) {
            System.out.println(user);
        }

        sqlSession.close();
        in.close();


    }


    @Test
    public void test04() throws IOException {
        /*
         * 加载主配置文件
         * 获取SqlsessionFactroy对象
         * 获取SqlSession对象
         * 创建dao对象
         * 执行sql语句
         * close
         * */
        InputStream in = Resources.getResourceAsStream("mybatis.xml");
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = factory.openSession();
        daoUser dao = sqlSession.getMapper(daoUser.class);
        User user1 = new User();
        user1.setId(1);
        User user = dao.selctParamObj(user1);
        System.out.println(user);

        sqlSession.close();
        in.close();
    }

    @Test
    public void test05() throws IOException {
        InputStream in= Resources.getResourceAsStream("mybatis.xml");
        SqlSessionFactory factory=new SqlSessionFactoryBuilder().build(in);
        SqlSession sqlSession = factory.openSession();
        daoUser dao = sqlSession.getMapper(daoUser.class);
        HashMap<String, Object> map = new HashMap<>();
        map.put("mapid",1);
        map.put("mapname","lisi");
        List<User> users = dao.selectMap(map);
        for (User user:users
             ) {
            System.out.println(user);
        }
        sqlSession.close();
        in.close();
    }
}

```



# 占位符 ${}和#{}的区别

**#{字符}**

1）使用的PrepareStatement对象，执行sql语句，效率高。

2）使用的PrepareStatement对象，能避免sql语句， sql语句执行更安全。

3） #{} 常常作为 列值使用的， 位于等号的右侧，  #{}位置的值和数据类型有关的。

 **${字符}**

1）使用Statement对象，执行sql语句，效率低

2）${}占位符的值，使用的字符串连接方式， 有sql注入的风险。 有代码安全的问题

3)  ${} 数据是原样使用的， 不会区分数据类型。

4）${} 常用作 表名或者列名， 在能保证数据安全的情况下使用 ${}



# 封装返回集输出结果 resultType、resultMap

resultType属性： 在执行select时使用， 作为<select>标签的属性出现的。

resultMap: 结果映射。 自定义列名和java对象属性的对应关系。 常用在列名和属性名不同的情况。

```xml
<!--使用resultMap定义列和属性的关系-->
<!--定义resultMap
    id:给resultMap的映射关系起个名称，唯一值
    type:java类型的全限定名称
-->
<resultMap id="customMap" type="com.bjpowernode.vo.CustomObject">

    <!--定义列名和属性名的对应-->
    <!--主键类型使用id标签-->
    <id column="id" property="cid" />
    <!--非主键类型使用result标签-->
    <result column="name" property="cname" />
    <!--列名和属性名相同不用定义-->
    <result column="email" property="email" />
    <result column="age" property="age" />
</resultMap>

<!--使用resultMap属性，指定映射关系的id
    resultMap和resultType 不能同时使用， 二选一。
-->
<select id="selectById2" resultMap="customMap">
  select id,name,email,age from student where id=#{stuid}
</select>
```



# 列名和java对象属性名称不一样解决方式

1） 使用resultMap： 自定义列名和属性名称对应关系

2）使用resultType:  使用列别名，让别名和java对象属性名称一样



# like

# 动态sql

 # mybatis 配置文件

# PageHelper(数据分页)

