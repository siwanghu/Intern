# Spring-Aop笔记（siwang.hu&nbsp;&nbsp;V1.0)  
> + AOP(Aspect-Oriented Programming)-----面向切面编程  
>  
> + OOP(Object-Oriented Programming)-----面向对象编程以类(class)作为我们的基本单元, 而 AOP 中的基本单元是 Aspect(切面)  
## AOP概念  
> ### **Aspect(切面)**  
> aspect由pointcount和advice组成, 它既包含了横切逻辑的定义, 也包括了连接点的定义；可以简单地认为, 使用@Aspect注解的类就是切面。Spring AOP就是负责实施切面的框架, 它将切面所定义的横切逻辑织入到切面所指定的连接点中. AOP的工作重心在于如何将增强织入目标对象的连接点上  
>  
> ### **advice(增强)**  
> 由 aspect 添加到特定的 join point(即满足 point cut 规则的 join point) 的一段代码. 许多 AOP 框架, 包括 Spring AOP, 会将 advice 模拟为一个拦截器(interceptor), 并且在 join point 上维护多个 advice, 进行层层拦截  
>  
> ###  **连接点(join point)**  
> 程序运行中的一些时间点, 例如一个方法的执行, 或者是一个异常的处理. 在 Spring AOP 中, join point 总是方法的执行点, 即只有方法连接点  
>  
> ### **切点(point cut)**  
> 这是一组一个或多个连接点，通知应该被执行  
>  
> *** 
> Advice 是和特定的point cut关联的, 并且在point cut相匹配的join point中执行. 在Spring中, 所有的方法都可以认为是 joinpoint, 但是我们并不希望在所有的方法上都添加Advice, 而 pointcut的作用就是提供一组规则(使用 AspectJ pointcut expression language 来描述) 来匹配joinpoint, 给满足规则的 joinpoint添加Advice  
> ***  
> 在 Spring AOP 中, 所有的方法执行都是 join point. 而 point cut 是一个描述信息, 它修饰的是 join point, 通过 point cut, 我们就可以确定哪些 join point 可以被织入 Advice.   
> ***  
> advice 是在 join point 上执行的, 而 point cut 规定了哪些 join point 可以执行哪些 advice
## xml配置Aop  
> Logging.java 它定义了在各个点调用的方法  
```
package com.tutorialspoint;
public class Logging {
   /** 
    * This is the method which I would like to execute
    * before a selected method execution.
    */
   public void beforeAdvice(){
      System.out.println("Going to setup student profile.");
   }
   /** 
    * This is the method which I would like to execute
    * after a selected method execution.
    */
   public void afterAdvice(){
      System.out.println("Student profile has been setup.");
   }
   /** 
    * This is the method which I would like to execute
    * when any method returns.
    */
   public void afterReturningAdvice(Object retVal){
      System.out.println("Returning:" + retVal.toString() );
   }
   /**
    * This is the method which I would like to execute
    * if there is an exception raised.
    */
   public void AfterThrowingAdvice(IllegalArgumentException ex){
      System.out.println("There has been an exception: " + ex.toString());   
   }  
}
```
> 下面是 Student.java 文件的内容
```
package com.tutorialspoint;
public class Student {
   private Integer age;
   private String name;
   public void setAge(Integer age) {
      this.age = age;
   }
   public Integer getAge() {
      System.out.println("Age : " + age );
      return age;
   }
   public void setName(String name) {
      this.name = name;
   }
   public String getName() {
      System.out.println("Name : " + name );
      return name;
   }  
   public void printThrowException(){
       System.out.println("Exception raised");
       throw new IllegalArgumentException();
   }
}
```
> 下面是 MainApp.java 文件的内容：
```
package com.tutorialspoint;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = 
             new ClassPathXmlApplicationContext("Beans.xml");
      Student student = (Student) context.getBean("student");
      student.getName();
      student.getAge();      
      student.printThrowException();
   }
}
```
> xml配置
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd ">

   <aop:config>
      <aop:aspect id="log" ref="logging">
         <aop:pointcut id="selectAll" 
         expression="execution(* com.tutorialspoint.*.*(..))"/>
         <aop:before pointcut-ref="selectAll" method="beforeAdvice"/>
         <aop:after pointcut-ref="selectAll" method="afterAdvice"/>
         <aop:after-returning pointcut-ref="selectAll" 
                              returning="retVal"
                              method="afterReturningAdvice"/>
         <aop:after-throwing pointcut-ref="selectAll" 
                             throwing="ex"
                             method="AfterThrowingAdvice"/>
      </aop:aspect>
   </aop:config>

   <!-- Definition for student bean -->
   <bean id="student" class="com.tutorialspoint.Student">
      <property name="name"  value="Zara" />
      <property name="age"  value="11"/>      
   </bean>

   <!-- Definition for logging aspect -->
   <bean id="logging" class="com.tutorialspoint.Logging"/> 

</beans>
```
## 注解实现AOP  
> @AspectJ是一种使用 Java 注解来实现 AOP 的编码风格。 @AspectJ 风格的 AOP 是 AspectJ Project 在 AspectJ 5 中引入的, 并且 Spring 也支持 @AspectJ 的AOP风格  
> + 使用Java Configuration 方式使能@AspectJ  
```
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {

}
```
> + 使用XML方式使能@AspectJ
```
<aop:aspectj-autoproxy/>
```
### 使用步骤  
> **1.定义aspect切面**  
```
@Component
@Aspect
public class MyTest {

}
```
> 使用注解@Aspect标注一个Bean后, 那么Spring框架会自动收集这些Bean, 并添加到Spring AOP中  
> **2.声明pointcut**  
> pointcut 的声明由两部分组成:  
> + 一个方法签名, 包括方法名和相关参数  
> + 个 pointcut 表达式, 用来指定哪些方法执行是我们感兴趣的(即因此可以织入 advice)  
```
@Pointcut("execution(* com.xys.service.UserService.*(..))") // 切点表达式
private void dataAccessOperation() {} // 切点前面
```
## 切点表达式  
```
// 匹配指定包中的所有的方法
execution(* com.xys.service.*(..))

// 匹配当前包中的指定类的所有方法
execution(* UserService.*(..))

// 匹配指定包中的所有 public 方法
execution(public * com.xys.service.*(..))

// 匹配指定包中的所有 public 方法, 并且返回值是 int 类型的方法
execution(public int com.xys.service.*(..))

/ 匹配指定包中的所有 public 方法, 并且第一个参数是 String, 返回值是 int 类型的方法
execution(public int com.xys.service.*(String name, ..))
```
> 