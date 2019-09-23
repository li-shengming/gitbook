---
title: 单例设计模式
layout: post
toc: true
date: 2019-05-06
categories: 
- 设计模式
tags: 
- 创建型
- 单例模式
---
## 简介
单例模式 (Singleton) 是一种创建型模式，指某个类采用Singleton模式，则在这个类被创建后，只可能产生一个实例供外部访问，并且提供一个全局的访问点。
<!--more-->
## UML图
{% plantuml %}
package "class Singleton(单例模式)" {

    class Singleton
    {
    +getInstance():Singleton
    -Singleton()
    }
    note right: 单例类
}
{% endplantuml %}

## 具体实现
简单点说，就是一个应用程序中，某个类的实例对象只有一个，你没有办法去new，因为构造器是被private修饰的，一般通过getInstance()的方法来获取它们的实例。getInstance()的返回值是一个同一个对象的引用，并不是一个新的实例

1) 饿汉式
特点：线程安全，无法实现实例懒加载策略。
~~~
public class Singleton1 {

    private final static Singleton1 singleton1 = new Singleton1();

    private Singleton1() {
    }

    public static Singleton1 getInstance() {
        return singleton1;
    }

}
~~~
2) 懒汉式
特点：线程不安全，实现了实例懒加载策略。
~~~
public class Singleton2 {

    private static Singleton2 singleton2;

    private Singleton2() {
    }

    public static Singleton2 getInstance() {

        if (singleton2 == null)
            singleton2 = new Singleton2();
        return singleton2;

    }

}
~~~
3) 全局锁式
特点：线程安全，且实现了懒加载策略，但是线程同步时效率不高。
~~~
public class Singleton3 {

    private static Singleton3 singleton3;

    private Singleton3() {
    }

    public synchronized static Singleton3 getInstance() {

        if (singleton3 == null)
            singleton3 = new Singleton3();
        return singleton3;

    }

}
~~~
4) 双重校验锁式
   特点：线程安全，且实现了懒加载策略，同时保证了线程同步时的效率；实现复杂，且 volatile 需要在JDK1.5之后的版本才能确保安全。
~~~
   public class Singleton4 {
   
       private static volatile Singleton4 singleton4;
   
       private Singleton4() {
       }
   
       public static Singleton4 getInstance() {
   
           if (singleton4 == null) {
               synchronized (Singleton4.class) {
                   if (singleton4 == null) {
                       singleton4 = new Singleton4();
                   }
               }
           }
           return singleton4;
       }
   }
~~~
### 为什么加volatile修饰？
> 多线程场景下，如果不增加volatile，还是可能存在工作线程里的副本已经初始化了实例，但是主线程的实例还是空，此时如果有其他线程恰好同时请求该实例，还是会再次初始化。
  
5) 静态代码块式
   特点：线程安全，类主动加载时才初始化实例，实现了懒加载策略，且线程安全。
~~~
   public class Singleton5 {
   
       private static Singleton5 singleton5;
   
       private Singleton5() {
       }
   
       static {
           singleton5 = new Singleton5();
       }
   
       public static Singleton5 getInstance() {
           return singleton5;
       }
   
   }
~~~
6) 静态内部类式
特点：线程安全，不存在线程同步问题，且单例对象在程序第一次 getInstance() 时主动加载 SingletonHolder 和其 静态成员 INSTANCE，因而实现了懒加载策略；多创建了一个类；
~~~
public class Singleton6 {

    private Singleton6() {
    }

    private static class SingletonHolder {
        private static final Singleton6 INSTANCE = new Singleton6();
    }

    public static Singleton6 getInstance() {
        return Singleton6.SingletonHolder.INSTANCE;
    }

}
~~~
7) 枚举方式
特点：线程安全，不存在线程同步问题，且单例对象在枚举类型 INSTANCE 第一次引用时通过枚举的 构造函数 初始化，因而实现了懒加载策略；可以防止反序列化；
~~~
public class Singleton7 {

    private Singleton7() {

    }

    enum SingletonEnum {

        INSTANCE;

        private final Singleton7 singleton7;

        private SingletonEnum() {
            singleton7 = new Singleton7();
        }
    }

    public static Singleton7 getInstance() {
        return SingletonEnum.INSTANCE.singleton7;
    }
}
~~~
### 推荐使用哪一种？
这个问题我觉得没有标准答案，需要根据实际的业务场景进行分析。
- 如果这个单例类初始化开销很低，那么第一种最好，代码简单清晰，线程安全；
- 如果这个单例类初始化开销很大，要求一定要在实际使用时才初始化
    - 如果当前应用环境不是多线程环境，那么第二种最好，代码简单，而且效率高；
    - 如果是多线程环境，并且单例类中只有这一个方法需要枷锁处理，那么第三种最好；
    - 如果这个单例类中，很多方法都要加锁处理，那么方法级的加锁，会影响效率，所以第四、五、六种选择也不错；
    - 如果单例模式的使用场景更加复杂，如还可能通过反射或者反序列化获取单例实例，那么采用第七种，或者四、五、六在加一部分防止反射和反序列化的代码

~~~
private Singleton() {
    // 防止反射获取多个对象的漏洞
    if (null != instance) {
         throw new RuntimeException();
    }
 }
~~~
~~~
// 防止反序列化获取多个对象的漏洞。
// 无论是实现Serializable接口，或是Externalizable接口，当从I/O流中读取对象时，readResolve()方法都会被调用到。
// 实际上就是用readResolve()中返回的对象直接替换在反序列化过程中创建的对象。
private Object readResolve() throws ObjectStreamException {  
	return instance;
}
~~~
## 优缺点分析
优点
- 单例模式在内存中只有一个实例，减少了内存开支，尤其是频繁的创建和销毁实例。
- 由于只生成一个实例，所以减少了系统的性能开销。
- 避免对资源的多重占用，例如写文件操作。
- 单例模式可以在系统设置全局的访问点，优化和共享资源访问

缺点
- 单例模式对测试不利。
- 单例模式与单一职责原则有冲突，一个类应该只实现一个逻辑，而不用关心它是否是单例的。
## 应用场景
- Spring里的@Service，@Component等注释的bean，默认都是单例
    - 编程的时候一定要注意到这一点，一些新手容易忽视这一点，容易在单例类中加一些私有变量，在单例模式下，这些私有变量都是共享资源，多线程环境下使用时容易出问题
    - 尽量把业务实现尽量做成“无状态”的，这样容易做负载均衡，支持更高的并发
