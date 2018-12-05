# Java代理笔记（siwang.hu&nbsp;&nbsp;V1.0)  
> + 代理是一种常见的设计模式  
>  
> + 对实际对象提供一个代理以控制对这个对象的访问  
>  
> + 装饰模式是为对象新增行为，而代理模式是控制对象的访问（虽然也可以增加对象行为）  
>  
> + 在目标对象实现的基础上,增强额外的功能操作,即扩展目标对象的功能  
>  
> + 不要随意去修改别人已经写好的代码或者方法,如果需改修改,可以通过代理的方式来扩展该方法  
## 静态代理  
> + 静态代理在使用时,需要定义接口或者父类,被代理对象与代理对象一起实现相同的接口或者是继承相同父类  
>  
> + 在编译期确定代理对象，在程序运行前代理类的.class文件就已经存在了  
>  
> **示例说明**  
>  
> ![图片](./data/po2.gif)  
>  
> Subject是一个业务接口；RealSubject实现Subject业务接口，提供业务功能；Proxy代理RealSubject业务类  
>  
> **业务接口类**
```
public interface Subject {
    void Request();
}
```
> **业务实现类**  
```
public class RealSubject implements Subject {
    public void Request() {
        System.out.println("----提供业务----");
    }
}
```
> **业务代理类**  
```
public class Proxy implements Subject{
    //接收实际代理的对象
    private RealSubject realSubject;

    public Proxy(Subject target){
        this.realSubject=target;
    }

    public void Request() {
        System.out.println("开始事务...");
        target.Request();//执行目标对象的方法
        System.out.println("提交事务...");
    }
}
```
> **测试类**  
```
public class App {
    public static void main(String[] args) {
        //目标对象
        RealSubject target = new RealSubject();

        //代理对象,把目标对象传给代理对象,建立代理关系
        Proxy proxy = new Proxy(target);

        proxy.Request();//执行的是代理的方法
    }
}
```
> ### 静态代理总结  
> + 可以做到在不修改目标对象的功能前提下,对目标功能扩展  
>  
> + 代理类和委托类实现相同的接口，同时要实现相同的方法。这样就出现了大量的代码重复。如果接口增加一个方法，除了所有实现类需要实现这个方法外，所有代理类也需要实现此方法。增加了代码维护的复杂度  
>  
## 动态代理  
> + 动态代理叫做:JDK代理,接口代理  
>  
> + 在运行期，通过反射机制创建一个实现了一组给定接口的新类  
>  
> + 在运行时生成的class，必须提供一组interface给它，然后该class就宣称它实现了这些 interface。该class的实例可以当作这些interface中的任何一个来用。但是这个Dynamic Proxy其实就是一个Proxy， 它不会替你作实质性的工作，在生成它的实例时你必须提供一个handler，由它接管实际的工作  
>  
> + 接口中声明的所有方法都被转移到调用处理器一个集中的方法中处理（InvocationHandler.invoke）。这样，在接口方法数量比较多的时候，我们可以进行灵活处理，而不需要像静态代理那样每一个方法进行中转。而且动态代理的应用使我们的类职责更加单一，复用性更强  
>  
> + 代理对象不需要实现接口,但是目标对象一定要实现接口,否则不能用动态代理  
>  
> ### java.lang.reflect.Proxy
> JDK实现代理需要使用java.lang.reflect.Proxy类中的静态方法newProxyInstance方法,但是该方法需要接收三个参数,完整的写法是:  
```
static Object newProxyInstance(ClassLoader loader, 
                Class [] interfaces, InvocationHandler handler)
```
> + ClassLoader loader:指定当前目标对象使用类加载器,用null表示默认类加载器  
>  
> + Class [] interfaces:需要实现的接口数组  
>  
> + InvocationHandler handler:调用处理器,执行目标对象的方法时,会触发调用处理器的方法,从而把当前执行目标对象的方法作为参数传入  
>   
>  
> ### java.lang.reflect.InvocationHandler  
> 这是调用处理器接口，它自定义了一个 invoke 方法，用于集中处理在动态代理类对象上的方法调用，通常在该方法中实现对委托类的代理访问  
```
// 该方法负责集中处理动态代理类上的所有方法调用
// 第一个参数既是代理类实例，第二个参数是被调用的方法对象
// 第三个方法是调用参数
Object invoke(Object proxy, Method method, Object[] args)
```  
>  
> ### 创建动态代理对象 
```
/**
 * 创建动态代理对象
 * 动态代理不需要实现接口,但是需要指定接口类型
 */
public class ProxyFactory{
    //维护一个目标对象
    private Object target;

    public ProxyFactory(Object target){
        this.target=target;
    }

    //给目标对象生成代理对象
    public Object getProxyInstance(){
        return Proxy.newProxyInstance(
            target.getClass().getClassLoader(),
            target.getClass().getInterfaces(),
            new InvocationHandler() {
                @Override
                public Object invoke(Object proxy, Method method, 
                            Object[] args) throws Throwable {
                    System.out.println("开始事务");
                    //执行目标对象方法
                    Object returnValue = method.invoke(target, args);
                    System.out.println("提交事务");
                    return returnValue;
                }
            }
        );
    }
}
```
> ### 测试类  
```
public class App {
    public static void main(String[] args) {
        // 目标对象
        RealSubject target = new RealSubject();
        System.out.println(target.getClass());

        // 给目标对象，创建代理对象
        Subject proxy = (Subject) new ProxyFactory(target).getProxyInstance();
        //内存中动态生成的代理对象
        System.out.println(proxy.getClass());

        // 执行代理对象的方法   
        proxy.Request();
    }
}
```
## Cglib代理  
> 静态代理和动态代理模式都是要求目标对象实现一个或多个接口,但是有时候目标对象只是一个单独的对象,并没有实现任何的接口,这个时候就可以使用构建目标对象子类的方式实现代理,这种方法就叫做:Cglib代理  
> + Cglib是一个强大的高性能的代码生成包,可以在运行期扩展java类与实现java接口.广泛的被许多AOP的框架使用,例如Spring AOP,提供方法的interception(拦截)  
>  
> + Cglib包的底层是通过使用字节码处理框架ASM来转换字节码并生成新的子类  
>  
> + 代理的类不能为final,否则报错；目标对象的方法如果为final/static,那么就不会被拦截,即不会执行目标对象额外的业务方法  
> **目标对象**
```
/**
 * 目标对象,没有实现任何接口
 */
public class Subject {
    public void request() {
        System.out.println("----提供业务----");
    }
}
```
> **Cglib代理工厂**  
```
/**
 * Cglib子类代理工厂
* 对Subject在内存中动态构建一个子类对象
*/
public class ProxyFactory implements MethodInterceptor{
     //维护目标对象
    private Object target;

    public ProxyFactory(Object target) {
        this.target = target;
    }

   //给目标对象创建一个代理对象
    public Object getProxyInstance(){
        //1.工具类
        Enhancer en = new Enhancer();
       //2.设置父类
        en.setSuperclass(target.getClass());
        //3.设置回调函数
        en.setCallback(this);
        //4.创建子类(代理对象)
        return en.create();
    }

    @Override
    public Object intercept(Object obj, Method method, Object[] args, 
                        MethodProxy proxy) throws Throwable {
        System.out.println("开始事务...");
        //执行目标对象的方法
        Object returnValue = method.invoke(target, args);
        System.out.println("提交事务...");
        return returnValue;
    }
}
```
> **测试类**  
```
/**
 * 测试类
 */
public class App {

    @Test
    public void test(){
        //目标对象
        Subject target = new Subject();

        //代理对象
        Subject proxy = (Subject)new ProxyFactory(target).getProxyInstance();

        //执行代理对象的方法
        proxy.save();
    }
}
```
## AOP  
> AOP&nbsp;---&nbsp;面向切面编程  
> 将日志记录，性能统计，安全控制，事务处理，异常处理等代码从业务逻辑代码中划分出来，通过对这些行为的分离，希望可以将它们独立到非业务逻辑的方法中，进而改变这些行为的时候不影响业务逻辑的代码---解耦
> 在Spring的AOP编程中:  
> + 如果加入容器的目标对象有实现接口,用JDK代理  
>  
> + 如果目标对象没有实现接口,用Cglib代理