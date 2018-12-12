# Spring-Ioc笔记（siwang.hu&nbsp;&nbsp;V1.0)  
## Ioc理念  
> Ioc--(Inversion of Control)，意思是控制反转  
> + 上层模块不应该依赖于下层模块，它们共同依赖于一个抽象  
>  
> + 抽象不能依赖于具体实现，具体实现依赖于抽象  
>  
> IoC是一种设计思想，在Java开发中，IoC意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制  
> + 在传统JavaSE程序设计中，我们直接在对象内部通过new进行创建依赖的对象，是程序主动去创建依赖对象；而IoC是有专门一个容器来创建这些对象，即由IoC容器来控制对象的创建  
>  
> + 传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；对象只是被动的接受依赖对象,依赖对象的获取被反转了  
>  
> 假设类A的工作依赖类B  
> **传统方式**  
> + 创建类A  
> + 创建类B  
> + 将类B主动注入类A（构造函数或set方法）  
> + 用户使用类A  
>  
> **Ioc/DI方式**  
> + 创建类A
> + 容器查看类A是否有依赖对象；如果有，由Ioc容器自动创建对象并注入类A，由容器管理对象的生命周期  
> + 用户使用类A  
>   
> 采用IoC容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，使得对象与对象之间是松散耦合，方便对象的测试，利于功能复用，程序的整个体系结构变得异常灵活  
>  
## Ioc容器  
> org.springframework.beans 和 org.springframework.context 是 IoC 容器的基础  
>  
> 在Spring中，有两种 IoC 容器：BeanFactory和 ApplicationContext  
> + BeanFactory：Spring 实例化、配置和管理对象的最基本接口  
>  
> + ApplicationContext：BeanFactory 的子接口。它还扩展了其他一些接口，以支持更丰富的功能，如：国际化、访问资源、事件机制、更方便的支持 AOP、在 web 应用中指定应用层上下文等  
>  
> 常见ApplicationContext实现  
> + BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath.xml");  
> 从 classpath 获取配置文件；
>  
> + BeanFactory beanFactory = new FileSystemXmlApplicationContext("fileSystemConfig.xml");  
> 从文件系统获取配置文件  
## Bean定义  
> bean 是一个被实例化，组装，并通过 Spring IoC 容器所管理的对象。这些 bean 是由用容器提供的配置元数据创建的  
> 每个 bean 定义的下列属性：
> + class这个属性是强制性的，并且指定用来创建bean的bean类  
>  
> + name这个属性指定唯一的 bean 标识符。在基于 XML 的配置元数据中，你可以使用 ID 和/或 name 属性来指定 bean 标识符  
>  
> + scope这个属性指定由特定的 bean 定义创建的对象的作用域  
>  
> + constructor-arg	它是用来注入依赖关系的
>  
> + properties	它是用来注入依赖关系的  
>  
> + autowiring mode	它是用来注入依赖关系的  
>  
> + lazy-initialization mode	延迟初始化的 bean 告诉 IoC 容器在它第一次被请求时，而不是在启动时去创建一个 bean 实例  
>  
> + destruction 方法当包含该 bean 的容器被销毁时，使用回调方法  
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- A simple bean definition -->
   <bean id="..." class="...">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

   <!-- A bean definition with lazy init set on -->
   <bean id="..." class="..." lazy-init="true">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

   <!-- A bean definition with initialization method -->
   <bean id="..." class="..." init-method="...">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

   <!-- A bean definition with destruction method -->
   <bean id="..." class="..." destroy-method="...">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

   <!-- more bean definitions go here -->

</beans>
```
### Bean 的作用域
> 当在 Spring 中定义一个 时，你必须声明该 bean 的作用域的选项。例如，为了强制 Spring 在每次需要时都产生一个新的 bean 实例，你应该声明 bean 的作用域的属性为 prototype。同理，如果你想让 Spring 在每次需要时都返回同一个bean实例，你应该声明 bean 的作用域的属性为 singleton  
> + singleton	该作用域将 bean 的定义的限制在每一个 Spring IoC 容器中的一个单一实例(默认)。
> + prototype	该作用域将单一 bean 的定义限制在任意数量的对象实例。
> + request	该作用域将 bean 的定义限制为 HTTP 请求。只在 web-aware Spring ApplicationContext 的上下文中有效。
> + session	该作用域将 bean 的定义限制为 HTTP 会话。 只在web-aware Spring ApplicationContext的上下文中有效。
> + global-session	该作用域将 bean 的定义限制为全局 HTTP 会话。只在 web-aware Spring ApplicationContext 的上下文中有效
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="helloWorld" class="com.tutorialspoint.HelloWorld" 
      scope="prototype">
   </bean>

</beans>
```
### Bean 的生命周期
> 当一个 bean 被实例化时，它可能需要执行一些初始化使它转换成可用状态。同样，当 bean 不再需要，并且从容器中移除时，可能需要做一些清除工作  
### Bean 后置处理器
> 允许我们在工厂里所有的bean被加载进来后但是还没初始化前，对所有bean的属性进行修改也可以add属性值  
## Ioc容器工作步骤  
> + 配置元数据：需要配置一些元数据来告诉 Spring，你希望容器如何工作，具体来说，就是如何去初始化、配置、管理 JavaBean 对象  
>  
> + 实例化容器：由 IoC 容器解析配置的元数据。IoC 容器的 Bean Reader 读取并解析配置文件，根据定义生成 BeanDefinition 配置元数据对象，IoC 容器根据 BeanDefinition 进行实例化、配置及组装 Bean  
>  
> + 使用容器：由客户端实例化容器，获取需要的 Bean  
>  
> **配置元数据方式**  
> + 基于 xml 配置：Spring 的传统配置方式。在**beans**标签中配置元数据内容。缺点是当JavaBean过多时，产生的配置文件不易维护  
>  
> + 基于注解配置：Spring2.5引入,可以简化你的配置  
>  
> + 基于 Java 配置：可以使用 Java 类来定义 JavaBean 。
为了使用这个新特性，需要用到 @Configuration 、@Bean 、@Import 和 @DependsOn 注解  
>  
## xml配置  
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd">
  <import resource="resource1.xml" />

  <bean id="bean1" class=""></bean>
  <bean id="bean2" class=""></bean>
  <bean name="bean2" class=""></bean>

  <alias alias="bean3" name="bean2"/>
  <import resource="resource2.xml" />  
</beans>
```
> + **beans** 是 Spring 配置文件的根节点  
> + **bean**  用来定义一个JavaBean；id属性是它的标识，在文件中必须唯一；class 属性是它关联的类  
> + **alias** 用来定义 Bean 的别名  
> + **import** 用来导入其他配置文件的 Bean 定义。这是为了加载多个配置文件，当然也可以把这些配置文件构造为一个数组（new String[] {“config1.xml”, config2.xml}）传给 ApplicationContext 实现类进行加载多个配置文件  
## 如何使用容器  
```
// create and configure beans
ApplicationContext context =
new ClassPathXmlApplicationContext(new String[] {"services.xml", "daos.xml"});
// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);
// use configured instance
List<String> userList = service.getUsernameList();
```
> 使用容器的方式就是通过getBean获取IoC容器中的JavaBean  
## Java配置  
> + 基于Java配置 Spring IoC 容器，实际上是Spring 允许用户定义一个类，在这个类中去管理 IoC 容器的配置  
>  
> + 需要用到两个注解：@Configuration和@Bean  
>  
> + @Configuration等价于xml配置中的beans标签；将@Bean等价于xml配置中的bean标签  
>  
> + @Bean 的修饰目标只能是方法或注解  
>  
> + @Bean 只能定义在@Configuration或@Component注解修饰的类中  
>  
```
@Configuration
public class AnnotationConfiguration {
    private static final Logger log = LoggerFactory.getLogger(JavaComponentScan.class);

    @Bean
    public Job getPolice() {
        return new Police();
    }

    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(AnnotationConfiguration.class);
        ctx.scan("org.zp.notes.spring.beans");
        ctx.refresh();
        Job job = (Job) ctx.getBean("police");
        log.debug("job: {}, work: {}", job.getClass(), job.work());
    }
}

public interface Job {
    String work();
}

@Component("police")
public class Police implements Job {
    @Override
    public String work() {
        return "抓罪犯";
    }
}
```
> 等价于xml配置  
```
<beans>
    <bean id="police" class="org.zp.notes.spring.ioc.sample.job.Police"/>
</beans>
```
> @Bean 注解用来表明一个方法实例化、配置合初始化一个被 Spring IoC 容器管理的新对象。如果你熟悉 Spring 的 xml 配置，你可以将@Bean 视为等价于beans标签。@Bean 注解可以用于任何的 Spring @Component bean，然而，通常被用于@Configuration bean  
>  
## 注解配置  
> + 减少了配置，并且可以使配置更加精细——类，方法，字段都可以用注解去标记  
>  
> + 使用注解，不可避免产生了侵入式编程，你需要将注解加入你的源码并编译它,注解往往比较分散，不易管控  
> + Spring 默认是不启用注解的。如果想使用注解，需要先在 xml 中启动注解。 启动方式：在 xml 中加入一个标签  
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">

   <context:annotation-config/>
   <!-- bean definitions go here -->

</beans>
```
> 一旦被配置后，你就可以开始注解你的代码，表明 Spring 应该自动连接值到属性，方法和构造函数
> + @Required 注解应用于 bean 属性的 setter 方法  
> + @Autowired 注解可以应用到 bean 属性的 setter 方法，非 setter 方法，构造函数和属性
> + @Qualifier通过指定确切的将被连线的 bean，@Autowired 和 @Qualifier 注解可以用来删除混乱  
> + JSR-250 Annotations Spring 支持 JSR-250 的基础的注解，其中包括了 @Resource，@PostConstruct和@PreDestroy注解  
>  
> **重点区分问题**
> + **所谓的注解配置的意思是：在xml配置文件中，我们只要声明这个bean即可，使用bean标签；bean之间的依赖在代码中使用注解去配置,而不是在xml文件中写明各个类之间的依赖关系**  
> + **所谓Java配置也是在xml配置文件中声明这个bean即可，依赖在代码中写明，常用与系统的配置**  