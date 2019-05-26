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

    Singleton <.. Client
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

    private final static Singleton2 singleton2;

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

    private final static Singleton3 singleton3;

    private Singleton3() {
    }

    public synchronized static Singleton3 getInstance() {

        if (singleton3 == null)
            singleton3 = new Singleton3();
        return singleton3;

    }

}
~~~
4) 静态代码块式
特点：线程安全，类主动加载时才初始化实例，实现了懒加载策略，且线程安全。
~~~
public class Singleton4 {

    private final static Singleton4 singleton4;

    private Singleton4() {
    }

    static {
        singleton4 = new Singleton4();
    }

    public static Singleton4 getInstance() {
        return singleton4;
    }

}
~~~
5) 双重校验锁式
特点：线程安全，且实现了懒加载策略，同时保证了线程同步时的效率。但是volatile强制当前线程每次读操作进行时，保证所有其他的线程的写操作已完成。volatile使得JVM内部的编译器舍弃了编译时优化，对于性能有一定的影响。
~~~
public class Singleton5 {

    private static volatile Singleton5 singleton5;

    private Singleton5() {
    }

    public static Singleton5 getInstance() {

        if (singleton5 == null) {
            synchronized (Singleton5.class) {
                if (singleton5 == null) {
                    singleton5 = new Singleton5();
                }
            }
        }
        return singleton5;
    }
}
~~~
6) 静态内部类式【推荐】
特点：线程安全，不存在线程同步问题，且单例对象在程序第一次 getInstance() 时主动加载 SingletonHolder 和其 静态成员 INSTANCE，因而实现了懒加载策略。
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
特点：线程安全，不存在线程同步问题，且单例对象在枚举类型 INSTANCE 第一次引用时通过枚举的 构造函数 初始化，因而实现了懒加载策略。
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

    public static void main(String[] args) {
        IntStream.rangeClosed(0, 100).forEach(i -> new Thread() {
            public void run() {
                out.println(Singleton7.getInstance());
            };
        }.start());
    }
}
~~~
## 优缺点分析
优点：节约系统资源，对于一些需要频繁创建和销毁的对象单例模式无疑可以提高系统的性能,例如，数据库连接；
缺点：单例类的职责过重，里面的代码可能会过于复杂，在一定程度上违背了“单一职责原则”
## 应用场景
- Spring里的@Service，@Component等注释的bean，默认都是单例
    - 编程的时候一定要注意到这一点，一些新手容易忽视这一点，容易在单例类中加一些私有变量，在单例模式下，这些私有变量都是共享资源，多线程环境下使用时容易出问题
    - 尽量把业务实现尽量做成“无状态”的，这样容易做负载均衡，支持更高的并发
