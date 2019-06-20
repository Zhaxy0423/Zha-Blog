---
layout:     post
title:      SpringBoot注解及核心配置
subtitle:   注解的工作机制及核心配置详解
date:       2019-06-20
author:     BY
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - 注解
    - 配置
    - 框架
    - SpringBoot
    
---

# 一、注解的工作机制
### 1.注解定义：
注解就是元数据，即一种描述数据的数据，也可以说注解就是源代码的元数据。
```java
@Override
public String toString() {
    return "This is String Representation of current object.";
}
```
（1）@Override注解告诉编译器这个方法是一个重写方法（描述方法的元数据），如果父类中不存在该方法，编译器会报错，提示该方法没有重写父类中的方法。

（2）Annotation是一种应用于类、方法、参数、变量、构造器及包声明中的特殊修饰符。它是一种由JSR-175标准选择用来描述元数据的一种工具。
### 2.引入注解的原因：
>弱耦合的XML配置在某些情况下代码和配置是完全分离的，维护性较差。
### 3.Annotation：
```java
@Documented - 注解是否将包含在JavaDoc中
@Retention - 什么时候使用该注解
@Target - 注解用于什么地方
@Inherited - 是否允许子类继承该注解
```
@Documented —— 一个简单的Annotations标记注解，表示是否将注解信息添加在java文档中。

@Retention —— 定义该注解的生命周期。

>(1)RetentionPolicy.SOURCE —— 在编译阶段丢弃。这些注解在编译结束之后就不再有任何意义，所以它们不会写入字节码。@Override, @SuppressWarnings都属于这类注解。

>(2)RetentionPolicy.CLASS —— 在类加载的时候丢弃。在字节码文件的处理中有用。注解默认使用这种方式。

>(3)RetentionPolicy.RUNTIME—— 始终不会丢弃，运行期也保留该注解，因此可以使用反射机制读取该注解的信息。我们自定义的注解通常使用这种方式。

@Target —— 表示该注解用于什么地方。如果不明确指出，该注解可以放在任何地方。以下是一些可用的参数。需要说明的是：属性的注解是兼容的，如果你想给7个属性都添加注解，仅仅排除一个属性，那么你需要在定义target包含所有的属性。如ElementType.TYPE:用于描述类、接口或enum声明。

@Inherited – 定义该注释和子类的关系
### 4.自定义注解：
Annotations只支持基本类型、String及枚举类型。注释中所有的属性被定义成方法，并允许提供默认值。
```java
//自定义注解
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@interface Todo {
public enum Priority {LOW, MEDIUM, HIGH}
public enum Status {STARTED, NOT_STARTED}
　　String author() default "Yash";
　　Priority priority() default Priority.LOW;
　　Status status() default Status.NOT_STARTED;
}
//使用注解
@Todo(priority = Todo.Priority.MEDIUM, author = "Yashwant", status = Todo.Status.STARTED)
public void incompleteMethod1() {
    //业务逻辑
}
```
在一个用户程序调用自定义注解，需要使用反射机制反射可以提供类名、方法和实例变量对象。所有这些对象都有getAnnotation()这个方法用来返回注解信息，则需要把这个对象转换为我们自定义的注释(使用instanceOf()检查之后)，同时也可以调用自定义注释里面的方法。
```java
Class businessLogicClass = BusinessLogic.class;
for(Method method : businessLogicClass.getMethods()) {
    //返回注解信息
　　Todo todoAnnotation = (Todo)method.getAnnotation(Todo.class);
　　if(todoAnnotation != null) {
　　　　System.out.println(" Method Name : " + method.getName());
　　　　System.out.println(" Author : " + todoAnnotation.author());
　　　　System.out.println(" Priority : " + todoAnnotation.priority());
　　　　System.out.println(" Status : " + todoAnnotation.status());
　　}
}
```
# 二、SpringBoot核心配置文件详解
SpringBoot的两种配置文件
```java
bootstrap (.yml 或者 .properties)
application (.yml 或者 .properties)
```
### 1.bootstrap/application的区别
>SpringCloud构建于SpringBoot之上，在SpringBoot中有两种上下文，一种是bootstrap, 另外一种是application, bootstrap 是应用程序的父ApplicationContext，也就是说bootstrap加载优先于applicaton。

>bootstrap主要用于从额外的资源来加载配置信息，还可以在本地外部配置文件中解密属性。application配置文件这个容易理解，主要用于SpringBoot 项目的自动化配置。这两个上下文共用一个环境，它是任何Spring应用程序的外部属性的来源。

>bootstrap里面的属性会优先加载，它们默认也不能被本地相同配置覆盖。
### 2.bootstrap的应用场景
>使用 SpringCloud Config配置中心时，这时需要在 bootstrap配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；

>一些固定的不能被覆盖的属性;

>一些加密/解密的场景；
