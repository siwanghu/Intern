# Java基础（siwang.hu&nbsp;&nbsp;V1.0）  
## 泛型
> 泛型：JDK5引入了泛型机制，泛型实现了参数化类型的概念，使代码可以应用于多种类型；由编译器来保证类型的正确性。  
>  
> *基本类型无法作为泛型中的类型参数*  
> **泛型的优点:**  
> + 类型安全；泛型的主要目标是提高 Java 程序的类型安全  
> + 消除强制类型转换；消除源代码中的许多强制类型转换  
> + 潜在的性能收益  
>  
> **类型参数命令约定**  
> + K ----键，比如映射的键  
> + V ----值，比如 List 和 Set 的内容，或者 Map 中的值  
> + E ----异常类  
> + T ----泛型  
>  
> ## **类型擦除**  
> Java 泛型是使用擦除来实现的，使用泛型时，任何具体的类型信息都被擦除了。ArrayList<String> 和 ArrayList<Integer> 在运行时，JVM 将它们视为同一类型  
```
public class ErasedTypeEquivalence {
    public static void main(String[] args) {
        Class c1 = new ArrayList<String>().getClass();
        Class c2 = new ArrayList<Integer>().getClass();
        System.out.println(c1 == c2);
    }
}
```  
> 输出结果是 true  
## 序列化  
> + 序列化：将对象转换为字节流  
> + 反序列化：将字节流转换为对象  
>  
> Java通过对象输入输出流来实现序列化和反序列化  
> + java.io.ObjectOutputStream 类的 writeObject() 方法可以实现序列化  
> + java.io.ObjectInputStream 类的 readObject() 方法用于实现反序列化  
>  
> *被序列化的类必须属于 Enum、Array 和 Serializable类型其中的任何一种*  
>  
> **1.serialVersionUID**  
> serialVersionUID 是Java为每个序列化类产生的版本标识  
> serialVersionUID 字段必须是static final long类型  
>  
> **2.默认序列化机制**  
> &nbsp;&nbsp;&nbsp;&nbsp;如果仅仅只是让某个类实现 Serializable接口，而没有其它任何处理的话，那么就是使用默认序列化机制。使用默认机制，在序列化对象时，不仅会序列化当前对象本身，还会对其父类的字段以及该对象引用的其它对象也进行序列化。同样地，这些其它对象引用的另外对象也将被序列化，以此类推。所以，如果一个对象包含的成员变量是容器类对象，而这些容器所含有的元素也是容器类对象，那么这个序列化的过程就会较复杂，开销也较大。  
> *这里的父类和引用对象既然要进行序列化，那么它们当然也要满足序列化要求*  
>  
> **3.非默认序列化机制**  
```
public class SerializeDemo02 {
    static class Person implements Serializable {
        transient private Integer age = null;
        // 其他内容
    }
    // 其他内容
}
```  
> 当某个字段被声明为 transient 后，默认序列化机制就会忽略该字段,不会对其序列化  
>  
> ![图片](./data/xlh.png)  
>  
