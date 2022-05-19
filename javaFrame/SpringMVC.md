# SpringMVC

**MVC**

 它是一种开发模式,它是模型视图控制器的简称.所有的web应用都是基于MVC开发.
  M:模型层,包含实体类,业务逻辑层,数据访问层
  V:视图层,html,javaScript,vue等都是视图层,用来显现数据
  C:控制器,它是用来接收客户端的请求,并返回响应到客户端的组件,Servlet就是组件

**SpringMVC**

  它是基于MVC开发模式的框架,用来优化控制器.它是Spring家族的一员.它也具备IOC和AOP.

# SpringMVC执行流程

<img src="E:\建生litterNotBook\Typora_图片\4---SpringMVC执行流程分析.png" alt="4---SpringMVC执行流程分析" style="zoom:25%;" />

![1652318831006](E:\建生litterNotBook\Typora_图片\1652318831006.png)



# SpringMVC注解开发代码入门案例

步骤：

pom.xml配置

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>

  <dependencies>
      <!--单元测试依赖-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <!--添加springmvc的依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.5.RELEASE</version>
    </dependency>
    <!--添加servlet的依赖-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
    </dependency>
  </dependencies>

  <build>
       <!--资源插件： 处理src/main/java目录中的xml-->
      <!--包括目录下的.properties,.xml 文件都会扫描到-->
    <finalName>springmvc</finalName>
    <resources>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.xml</include>
          <include>**/*.properties</include>
        </includes>
      </resource>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.xml</include>
          <include>**/*.properties</include>
        </includes>
      </resource>
    </resources>
  </build>
```



web.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--注册SpringMVC框架 
		DispatcherServlet：前端控制器
    -->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--
		在服务器启动的时候创建Spring容器
    -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <!--
          指定拦截什么样的请求
          http://localhost:8080/one
          http://localhost:8080/index.jsp
          http://localhost:8080/demo.action
          <a href="${pageContext.request.contextPath}/demo.action">访问服务器</a>
        -->
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
</web-app>
```



springMVC.xml 配置文件

```xml
<!--添加包扫描-->
    <context:component-scan base-package="com.bjpowernode.controller"></context:component-scan>
    <!--添加视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--配置前缀-->
        <property name="prefix" value="/admin/"></property>
        <!--配置后缀-->
        <property name="suffix" value=".jsp"></property>
    </bean>
```

Controller 核心处理器处理器

```java

@Controller  //交给Spring去创建对象
@RequestMapping("/zar")
public class DemoAction {
    /**
     * 以前的Servlet的规范
     * protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {}
     * action中所有的功能实现都是由方法来完成的
     * action方法的规范
     * 1)访问权限是public
     * 2)方法的返回值任意
     * 3)方法名称任意
     * 4)方法可以没有参数,如果有可是任意类型
     * 5)要使用@RequestMapping注解来声明一个访问的路径(名称)
     *
     */
    @RequestMapping("/demo.do")
    public String demo(){
        System.out.println("zar服务器被访问到了.......");
        return "main";  //通过视图解析器转发到对应的页面上或.do上
    }
}

```

# @RequestMapping注解

**此注解就是来映射服务器访问的路径**

@RequestMapping此注解在处理器类中可以加在方法上也可以加在类上，对应的前端控制器的对应request路径，加载类上，那么加在方法上的都成了它的子路径

**区分不同的请求**

method =指定不同的请求方法

```java
@Controller
	public class ReqAction {
	    @RequestMapping(value = "/req",method = RequestMethod.GET)
	    public String req(){
	        System.out.println("我是处理get请求的........");
	        return "main";
	    }
	    @RequestMapping(value = "/req" ,method = RequestMethod.POST)
	    public String req1(){
	        System.out.println("我是处理post请求的........");
	        return "main";
	    }
	}
```

# 数据提交方式方法中参数的处理

```java
1)单个提交数据
  页面:  
  <form action="${pageContext.request.contextPath}/one.action">
      姓名:<input name="myname"><br>
      年龄:<input name="age"><br>
      <input type="submit" value="提交">
  </form>
  action:
  @RequestMapping("/one")
    public String one(String myname,int age){  ===>自动注入,并且类型转换
        System.out.println("myname="+myname+",age="+(age+100));
        return "main";
    }

 2)对象封装提交数据
    在提交请求中,保证请求参数的名称与实体类中成员变量的名称一致,则可以自动创建对象,则可以自动提交数据,自动类型转换,自动封装数据到对象中.
    实体类:
    public class Users {
    private String name;
    private int age;}
    页面:
	<form action="${pageContext.request.contextPath}/two.action" method="post">
	    姓名:<input name="name"><br>
	    年龄:<input name="age"><br>
	    <input type="submit" value="提交">
	</form>
    action:
    @RequestMapping("/two")
    public String two(Users u){
        System.out.println(u);
        return "main";
    }
 3)动态占位符提交
    仅限于超链接或地址拦提交数据.它是一杠一值,一杠一大括号,使用注解@PathVariable来解析.      
    <a href="${pageContext.request.contextPath}/three/张三/22.action">动态提交</a>    
    @RequestMapping("/three/{uname}/{uage}")
    public String three(
            @PathVariable("uname")  ===>用来解析路径中的请求参数
            String name,
            @PathVariable("uage")
            int age){
        System.out.println("name="+name+",age="+(age+100));
        return "main";
    }
  4)映射名称不一致 
    提交请求参数与action方法的形参的名称不一致,使用注解@RequestParam来解析
    /**
     *  姓名:<input name="name"><br>
     *  年龄:<input name="age"><br>
     */
    @RequestMapping("/four")
    public String four(
            @RequestParam("name")  ===>专门用来解决名称不一致的问题
            String uname,
            @RequestParam("age")
            int uage){
        System.out.println("uname="+uname+",uage="+(uage+100));
        return "main";
    }
  5)手工提取数据
  /**
     *  姓名:<input name="name"><br>
     *  年龄:<input name="age"><br>
     */
  @RequestMapping("/five")
    public String five(HttpServletRequest request){
        String name = request.getParameter("name");
        int age = Integer.parseInt(request.getParameter("age"));
        System.out.println("name="+name+",age="+(age+100));
        return "main";
    }   
```

# 中文乱码解决

数据乱码解决，通过过滤器来解决

```xml
 配置过滤器.
  <filter>
        <filter-name>encode</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <!--
          配置参数
            private String encoding;
            private boolean forceRequestEncoding;
            private boolean forceResponseEncoding;
        -->
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
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
        <filter-name>encode</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping> 
```

# 方法返回值

1)String:客户端资源的地址,自动拼接前缀和后缀.还可以屏蔽自动拼接字符串,可以指定返回的路径.
  2)Object:返回json格式的对象.自动将对象或集合转为json.使用的jackson工具进行转换,必须要添加jackson依赖.一般用于ajax请求.
  3)void:无返回值,一般用于ajax请求.
  4)基本数据类型,用于ajax请求.
  5)ModelAndView:返回数据和视图对象,现在用的很少.

# ajax请求处理

**代码入门案例**

添加jackson依赖：主要用于ajax的请求处理依赖

```xml
<dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.8</version>
    </dependency>
```



webapp引入Jquery库

前端ajax请求代码

```html

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <%--导入函数库--%>
    <script src="js/jquery-3.3.1.js"></script>
</head>
<body>
<br><br><br>
<a href="javascript:show()">访问服务器</a>
<div id="mydiv">等待服务器返回学生集合</div>
</body>
<script type="text/javascript">
    function show() {
        $.ajax({
            url:"${pageContext.request.contextPath}/ajax.action",
            dataType:"json",
            type:"get",
            success:function (list) {
              //  alert(list);
                var s="";
                $.each(list,function (i,stu) {
                  //  alert(stu);
                    s+=stu.name+"----"+stu.age+"<br>";
                });
                $("#mydiv").html(s);

            }
        });
    }
</script>
</html>

```

在springmvc.xml文件中添加注解驱动<mvc:annotationdriven/>,它用来解析@ResponseBody注解

```xml
<mvc:annotation-driven></mvc:annotation-driven>
```



处理其中处理ajax请求

```java

@Controller
public class AjaxAction {

    //处理ajax请求,一定要加@ResponseBody
    @ResponseBody
    @RequestMapping("/ajax")
    public List<Student> ajax(){
        Student stu1 = new Student("张三",22);
        Student stu2 = new Student("李四",24);
        Student stu3 = new Student("王五",23);
       List<Student> list = new ArrayList<>();
       list.add(stu1);
       list.add(stu2);
       list.add(stu3);
        //调用json转换工具ObjectMapper进行转换
        return list;  //===>springmvc负责转换成json
    }
}
```



# 转发、重定向

```java
  @RequestMapping("/one")
    public String one(){
        System.out.println("这是请求转发页面跳转.........");
        return "main";  //默认是请求转发,使用视图解析器拼接前缀后缀进行页面跳转
    }
@RequestMapping("/two")
    public String two(){
        System.out.println("这是请求转发action跳转.........");
        //  /admin/  /other.action  .jsp
        //forward: 这组字符串可以屏蔽前缀和后缀的拼接.实现请求转发跳转
        return "forward:/other.action";  //默认是请求转发,使用视图解析器拼接前缀后缀进行页面跳转
    }

    @RequestMapping("/three")
    public String three(){
        System.out.println("这是重定向页面.......");
        //redirect:  这组字符串可以屏蔽前缀和后缀的拼接.实现重定向跳转
        return "redirect:/admin/main.jsp";
    }

    @RequestMapping("/four")
    public String four(){
        System.out.println("这是重定向action.......");
        //redirect:  这组字符串可以屏蔽前缀和后缀的拼接.实现重定向跳转
        return "redirect:/other.action";
    }

    @RequestMapping("/five")
    public String five(){
        System.out.println("这是随便跳.......");

        return "forward:/fore/login.jsp";
    }
```



# 方法参数类型

  不需要去创建,直接拿来使用即可.
  1)HttpServletRequest
  2)HttpServletResponse
  3)HttpSession
  4)Model
  5)Map
  6)ModelMap

等等

```java
@Controller
public class DataAction {

    @RequestMapping("/data")
    public String data(HttpServletRequest request,
                       HttpServletResponse response,
                       HttpSession session,
                       Model model,
                       Map map,
                       ModelMap modelMap){

        //做一个数据,传到main.jsp页面上
        Users u = new Users("张三",22);

        //传递数据
        request.setAttribute("requestUsers",u);
        session.setAttribute("sessionUsers",u);
        model.addAttribute("modelUsers",u);
        map.put("mapUsers",u);
        modelMap.addAttribute("modelMapUsers",u);

        return "main";  //请求转发方式跳转
       // return "redirect:/admin/main.jsp";
    }
}
```



# 日期处理

注意：浏览器传过来的日期本质上是一个String，可以通过以下方式处理成日期对象

也可以自己手动出路，方法是将Spring对的格式通过对应的转化成日期格式，具体步骤参考javaSE日期对象

```java
@Controller
public class MyDateAction {

    SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd");
    /**
     * 日期:<input type="date" name="mydate"><br>
     *
     */
//    @RequestMapping("/mydate")
//    public String mydate(
//            @DateTimeFormat(pattern = "yyyy-MM-dd")
//            Date mydate){
//        System.out.println(mydate);
//        System.out.println(sf.format(mydate));
//        return "show";
//    }

    //注册一个全局的日期处理注解
    @InitBinder
    public void initBinder(WebDataBinder dataBinder){
        dataBinder.registerCustomEditor(Date.class,new CustomDateEditor(sf,true));
    }
    @RequestMapping("/mydate")
    public String mydate(Date mydate, HttpServletRequest request){
        System.out.println(mydate);
        System.out.println(sf.format(mydate));
        request.setAttribute("mydate",sf.format(mydate));
        return "show";
    }

    @RequestMapping("/list")
    public String list(HttpServletRequest request) throws ParseException {
        Student stu1 = new Student("张三",sf.parse("2000-01-01"));
        Student stu2 = new Student("李四",sf.parse("2001-08-11"));
        Student stu3 = new Student("王五",sf.parse("2002-09-21"));
        List<Student> list = new ArrayList<>();
        list.add(stu1);
        list.add(stu2);
        list.add(stu3);
        request.setAttribute("list",list);
        return "show";
    }
}
```

# 资源在WEB-INF目录下

在WEB-INF,目录下的服务器资源不能直接通过浏览器地址栏的方式访问，自然而然通过重定向也访问不了

只能通过服务器的转发访问、通过SpringMVC框架的视图解析器也可以访问，因为视图解析器也是转发



# SpringMVC的拦截器

**SpringMVC拦截器Interceptor**

拦截器执行的时机：

 1)preHandle():在请求被处理之前进行操作,预处理
  2)postHandle():在请求被处理之后,但结果还没有渲染前进行操作,可以改变响应结果,后处理
  3)afterCompletion:所有的请求响应结束后执行善后工作,清理对象,关闭资源 ,最终处理.

拦截器实现的两种方式：

1)继承HandlerInterceptorAdapter的父类
  2)实现HandlerInterceptor接口,实现的接口,推荐使用实现接口的方式

下面使用通用的接口方式：



**过滤器(Filter) 与 [拦截器](https://so.csdn.net/so/search?q=拦截器&spm=1001.2101.3001.7020)(Interceptor)**

 Spring MVC 的拦截器（Interceptor）与 Java Servlet 的过滤器（Filter）类似，它主要用于拦截用户的请求并做相应的处理，通常应用在权限验证、记录请求信息的日志、判断用户是否登录等功能上。



以下俩图中虚线表示过滤器和拦截器的范围： 

 filter组件实际上并不知道后续内部处理是通过Spring MVC提供的DispatcherServlet还是其他Servlet组件，因为Filter是Servlet规范定义的标准组件，它可以应用在任何基于Servlet的程序中。 

<img src="E:\建生litterNotBook\Typora_图片\1652322142860.png" alt="1652322142860" style="zoom:50%;" />

 Spring MVC开发应用程序，还可以使用Spring MVC提供的一种功能类似Filter的拦截器：Interceptor。和Filter相比，Interceptor拦截范围不是后续整个处理流程，而是仅针对Controller拦截： 



<img src="E:\建生litterNotBook\Typora_图片\1652322165533.png" alt="1652322165533" style="zoom:50%;" />



使用拦截器配置：在SpringMVC配置文件中添加

```java
<!--注册拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!--映射要拦截的请求-->
            <mvc:mapping path="/**"/>
            <!--设置放行的请求-->
            <mvc:exclude-mapping path="/showLogin"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/login"></mvc:exclude-mapping>
            <!--配置具体的拦截器实现功能的类-->
            <bean class="com.ajs.interceptor.LoginInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

继承接口类：处理拦截类 

重写preHandle() 返回值类型是boolean值

retrurn false;请求将被拦截

return ture;请求将不被拦截

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //是否登录过的判断
        if(request.getSession().getAttribute("users") == null){
            //此时就是没有登录,打回到登录页面,并给出提示
            request.setAttribute("msg","您还没有登录,请先去登录!");
            request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request,response);
            return false;
        }
        return true;//放行请求
    }
}
```



# SSM整合

