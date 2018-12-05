# Java反射笔记（siwang.hu&nbsp;&nbsp;V1.0)  
> + Java反射是指在程序运行时，对于任意一个类(**.class文件**)，能够通过反射知道这个类的所有属性和方法；对于任意一个对象（**实际new的对象**），都能够调用它的任意一个方法和属性（**包括私有的**）。这种动态的获取类信息以及动态调用对象的方法的功能称为java反射机制  
>  
> + 通过Java的反射机制使得我们可以在程序运行时动态的加载、探索以及使用编译期间完全未知的 .class文件。Java程序可以加载一个运行时才得知名称的.class 文件，然后获悉其完整构造，并生成其对象实体、或对其 fields（变量）设值、或调用其 methods（方法）  
>  
> + Java的反射机制的实现要借助于4个类：Class，Constructor，Field，Method，Array;
## Class类  
> + 在Java中，通过Class类获取到任何一个类中完整的信息  
>  
> + 在Java中，Object是所有类的超类，所有类的对象实际上也是java.lang.Class类的实例；因此，所有的对象都可以转变成Class类型表示  
>  
> ### 创建Class类对象的方式  
> + 对象.getClass()  
>  
> + 类名.Class  
>  
> + Class.forName(类名)  
> *类名是指完整的类名称，包括类的包名*  
>  
> ### Class类的常用API  
> + forName(): 传入完整的”包.类”名称实例化Class对象  
>  
> + getConstructors() : 得到一个类中的全部构造方法  
>  
> + getConstructor(Class<?>...parameterTypes)：获取到指定参数类型的(public修饰的)构造方法  
>  
> + getDeclaredConstructor(Class<?>... parameterTypes)：获取到指定参数类型的构造方法，包含private修饰和public修饰  
>  
> + getDeclaredFields(): 获得某个类的单独定义全部的字段（即包括public、private和proteced等修饰符的全部属性），但是不包括父类的申明字段或者实现接口中的字段  
>  
> + getFields(): 获得某个类的所有的公共（public）的字段，包括父类中的公共字段或者实现接口的公共属性和类本身定义的公共属性  
>  
> + getMethods() : 获取到本类中全部public修饰的方法，包含父类中公共方法和覆盖重写的方法和自己本身定义的公共方法  
>  
> + getMethod(String name,Class...parameterType): 获取到指定方法名字，指定方法参数类型的公共方法  
>  
> + getSuperclass(): 获取到父类的Class  
>  
> + getInterfaces() : 获取（实现的全部接口对应的）Class数组  
>  
> + newInstance(): 实例化Class中定义类型的实例化对象  
>  
> + getComponentType(): 获取数组类型的Class  
>  
> + isArray(): 判断此Class是否是一个数组  
>  
> + getName() : 获取到一个类完整”包.类”名称  
>  
## 通过反射获取类的信息  
> ### 为方便演示，自定义Father类与Son类，Son类继承Father类  
```
public class Father{
    public String FatherName;
    public int FatherAge;

    public void printFather(){
        System.out.println("from father message!")
    }
}


public class Son extends Father{
    private String SonName;
    protected int SonAge;
    public String SonBirthday;

    public void printSon(){
        System.out.println("from son message!");
    }

    private void setSonName(String name){
        SonName = name;
    }
}
```
>  
> ### 获取类的所有变量信息  
```
private static void printFields(){
    //1.获取并输出类的名称
    Class mClass = Son.class;
    System.out.println("类的名称：" + mClass.getName());

    //2.1 获取所有 public 访问权限的变量
    // 包括本类声明的和从父类继承的
    Field[] fields = mClass.getFields();

    //2.2 获取所有本类(不包括父类的)声明的变量（不问访问权限）
    //Field[] fields = mClass.getDeclaredFields();

    //3. 遍历变量并输出变量信息
    for (Field field : fields) {
        //获取访问权限并输出
        int modifiers = field.getModifiers();
        System.out.print(Modifier.toString(modifiers) + " ");
        //输出变量的类型及变量名
        System.out.println(field.getType().getName()+ " " + field.getName());
    }
}
```
>  
> ### 获取类的所有方法信息  
```
private static void printMethods(){
    //1.获取并输出类的名称
    Class mClass = Son.class;
    System.out.println("类的名称：" + mClass.getName());

    //2.1 获取所有 public 访问权限的方法
    //包括自己声明和从父类继承的
    Method[] mMethods = mClass.getMethods();

    //2.2 获取所有本类(不包括父类)的方法（不问访问权限）
    //Method[] mMethods = mClass.getDeclaredMethods();

    //3.遍历所有方法
    for (Method method : mMethods) {
        //获取并输出方法的访问权限（Modifiers：修饰符）
        int modifiers = method.getModifiers();
        System.out.print(Modifier.toString(modifiers) + " ");

        //获取并输出方法的返回值类型
        Class returnType = method.getReturnType();
        System.out.println(returnType.getName() + " "+ method.getName());

        //获取并输出方法的所有参数
        Parameter[] parameters = method.getParameters();
        for (Parameter parameter: parameters) {
            System.out.println(parameter.getType().getName()
                    + " " + parameter.getName());
        }

        //获取并输出方法抛出的异常
        Class[] exceptionTypes = method.getExceptionTypes();
        if (exceptionTypes.length == 0){
            System.out.println("方法无异常抛出");
        }else {
            for (Class c : exceptionTypes) {
                System.out.println("throws "+ c.getName());
            }
        }
    }
}
```
>  
> ### 访问对象私有方法与私有属性  
```
public class Test{
    private String msg = "hello world!";

    private void privateMethod(String name , int age){
        System.out.print(name + age);
    }

    public String getMsg(){
        return msg;
    }
}


private static void getPrivateMethod() throws Exception{
    //1. 获取 Class 类实例
    Test test = new Test();
    Class mClass = testClass.getClass();

    //2. 获取私有方法
    //第一个参数为要获取的私有方法的名称
    //第二个为要获取方法的参数的类型，参数为 Class...，没有参数就是null
    //方法参数也可这么写 ：new Class[]{String.class , int.class}
    Method privateMethod =
            mClass.getDeclaredMethod("privateMethod", String.class, int.class);

    //3. 开始操作方法
    if (privateMethod != null) {
        //获取私有方法的访问权
        //只是临时获取访问权，并不是修改实际权限
        privateMethod.setAccessible(true);

        //使用 invoke 反射调用私有方法
        //privateMethod 是获取到的私有方法
        //testClass 要操作的对象
        //后面两个参数传实参
        privateMethod.invoke(test, "Java Reflect", 666);
    }
}

private static void modifyPrivateFiled() throws Exception {
    //1. 获取 Class 类实例
    TestClass test = new TestClass();
    Class mClass = testClass.getClass();

    //2. 获取私有变量
    Field privateField = mClass.getDeclaredField("msg");

    //3. 操作私有变量
    if (privateField != null) {
        //临时获取私有变量的访问权
        privateField.setAccessible(true);

        //修改私有变量，并输出以测试
        System.out.println("Before Modify：msg = " + test.getMsg());

        //调用 set(object , value) 修改变量的值
        //privateField 是获取到的私有变量
        //testClass 要操作的对象
        //"Modified" 为要修改成的值
        privateField.set(test, "Modified");
        System.out.println("After Modify：MSG = " + test.getMsg());
    }
}
```
>  
> ### 通过反射操作数组  
```
public static void testArray() throws Exception {
    Class cls = Class.forName("java.lang.String");
    Object array = Array.newInstance(cls,25);
    //往数组里添加内容
    Array.set(array,0,"hello");
    Array.set(array,1,"Java");
    Array.set(array,2,"fuck");
    Array.set(array,3,"Scala");
    Array.set(array,4,"Clojure");
    //获取某一项的内容
    System.out.println(Array.get(array,3));

    
}
```
