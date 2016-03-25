---
layout: post
title: Java反射机制
category: 技术
comments: true
---
# java反射
## 前言
写这篇文章是为了给初次使用java反射或者注解的程序猿们或者程序爱好或者java初学者提供一个demo，并简单介绍一下反射。
## java反射机制
通俗的说，反射机制就是可以把一个类、类的成员（函数以及属性）当成一个对象来操作，也就是说，类以及类的成员，在程序运行的时候还可以去动态的操作它们。

定义就那么简单，接下来还是来几个demo吧：
demo1：通过java反射机制得到类的包名和类名
```java
@org.junit.Test
public void demo1() {
    try {
        /**
         * 写法1
         **/
        Class<?> clazz = Class.forName("com.qunar.flight.asdf.User");
        logger.info("class User, packageName: {}, className: {}",
                clazz.getPackage().getName(), clazz.getName());
        /**
         * 写法2
         **/
        Class<?> clzz = User.class;
        logger.info("class User, packageName: {}, className: {}",
                clzz.getPackage().getName(), clzz.getName());
    } catch (Exception e) {
        logger.error("class not found", e);
    }

}
```
运行结果：
```java
2016-03-25 14:37:39.664 INFO class User, packageName: com.qunar.flight.asdf, className: com.qunar.flight.asdf.User
2016-03-25 14:37:39.668 INFO class User, packageName: com.qunar.flight.asdf, className: com.qunar.flight.asdf.User
```
demo2：通过java反射机制，用Class创建类对象
```java
@Test
public void demo2() {
    try {
        Class<?> clazz = User.class;
        User user = (User) clazz.newInstance();
        user.setUserId(123);
        user.setUserName("miaomiao");
        user.setPassword("asdfqwer");
        logger.info("User = {}", user.toString());
    } catch (Exception e) {
        logger.error("create class error", e);
    }
}
```
运行结果：
```java
2016-03-25 14:47:31.32 INFO User = User{userName='miaomiao', password='asdfqwer', userId=123}
```
demo3：通过java反射机制得到一个类的构造函数，并实现创建带参实例对象
```java
@Test
public void demo3() {
    try {
        Class<?> clazz = User.class;
        Constructor<?>[] constructors = clazz.getConstructors();
        logger.info("the constructors size: {}", constructors.length);
        User user1 = (User) constructors[0].newInstance();
        user1.setUserId(12);
        user1.setUserName("miaomiao");
        user1.setPassword("asdf");
        logger.info("the user1: {}", user1.toString());
        User user2 = (User) constructors[1].newInstance("taotao", "asdf", 13);
        logger.info("the user2: {}", user2.toString());
    } catch (Exception e) {
        logger.error("create class error", e);
    }
}
```
运行结果：
```java
2016-03-25 14:56:29.354 INFO the constructors size: 2
2016-03-25 14:56:29.357 INFO the user1: User{userName='miaomiao', password='asdf', userId=12}
2016-03-25 14:56:29.357 INFO the user2: User{userName='taotao', password='asdf', userId=13}
```
demo4：通过java反射机制得到；饿哦的一些属性
```java
@Test
public void demo4() {
    try {
        Class<?> clazz = User.class;
        //取得父类的名称
        Class<?> superCLass = clazz.getSuperclass();
        logger.info("the super class: {}", superCLass.getName());
        //取得类的成员变量，并对其操作
        User user = (User) clazz.newInstance();
        Field[] fields = clazz.getDeclaredFields();
        for (Field field : fields) {
            logger.info("the member of the class: {}", field.getName());
            if (field.getName().equals("userName")) {
                field.setAccessible(true);
                field.set(user, "miaomiao");
            }
        }
        logger.info("User: {}", user.toString());
        //获得类的方法
        Method[] methods = clazz.getDeclaredMethods();
        for (Method method : methods) {
            logger.info("the method of the class, methodName: {}, return type: {}, modifier: {}, code: {}",
                    method.getName(), method.getReturnType(), Modifier.toString(method.getModifiers()), method);
        }
        //通过反射调用method
        Method method = clazz.getMethod("setUserName", String.class);
        method.invoke(user, "taotao");
        logger.info("new user: {}", user.toString());
    } catch (Exception e) {
        logger.error("create class error", e);
    }
}
```
运行结果：
```java
2016-03-25 15:18:14.870 INFO the super class: java.lang.Object
2016-03-25 15:18:14.874 INFO the member of the class: userName
2016-03-25 15:18:14.874 INFO the member of the class: password
2016-03-25 15:18:14.874 INFO the member of the class: userId
2016-03-25 15:18:14.875 INFO User: User{userName='miaomiao', password='null', userId=0}
2016-03-25 15:18:14.875 INFO the method of the class, methodName: toString, return type: class java.lang.String, modifier: public, code: public java.lang.String com.qunar.flight.asdf.User.toString()
2016-03-25 15:18:14.876 INFO the method of the class, methodName: getPassword, return type: class java.lang.String, modifier: public, code: public java.lang.String com.qunar.flight.asdf.User.getPassword()
2016-03-25 15:18:14.876 INFO the method of the class, methodName: getUserName, return type: class java.lang.String, modifier: public, code: public java.lang.String com.qunar.flight.asdf.User.getUserName()
2016-03-25 15:18:14.877 INFO the method of the class, methodName: setPassword, return type: void, modifier: public, code: public void com.qunar.flight.asdf.User.setPassword(java.lang.String)
2016-03-25 15:18:14.878 INFO the method of the class, methodName: setUserName, return type: void, modifier: public, code: public void com.qunar.flight.asdf.User.setUserName(java.lang.String)
2016-03-25 15:18:14.879 INFO the method of the class, methodName: setUserId, return type: void, modifier: public, code: public void com.qunar.flight.asdf.User.setUserId(int)
2016-03-25 15:18:14.880 INFO the method of the class, methodName: getUserId, return type: int, modifier: public, code: public int com.qunar.flight.asdf.User.getUserId()
2016-03-25 15:18:14.880 INFO new user: User{userName='taotao', password='null', userId=0}
```
java反射机制介绍到这里就结束了，对于它使用的地方可能大家还有些迷糊，下篇文章我将会讲java反射的一个具体的使用：java动态代理。

