# Java 泛型

> https://blog.51cto.com/u_15420855/4542734

Java中的泛型，是JDK5引入的一个新特性。它主要提供的是**编译时期**类型的**安全检测机制**。这个机制允许程序在编译时检测到非法的类型，从而进行错误提示。 这样做的好处，一方面是告诉开发者当前方法接收或返回的参数类型，另一方面是避免程序运行时的类型转换错误。

## 泛型的设计推演

首先我们来看一下ArrayList这个集合，部分代码定义如下。

```java
public class ArrayList{
   transient Object[] elementData; 
}
```

在ArrayList中，存储元素所使用的结构是一个Object[]对象数组。意味着可以存储任何类型的数据。

当我们使用这个ArrayList来做下面这个操作时。

```java
public class ArrayExample {

    public static void main(String[] args) {
        ArrayList al=new ArrayList();
        al.add("Hello World");
        al.add(1001);
        String str=(String)al.get(1);
        System.out.println(str);
    }
}
```

没有泛型可能出现的两个问题：

- 需要对类型进行强制转换
- 使用不方便，容易出错

这个问题背后的需求是：

- 要能支持不同类型的数据存储
- 还需要保证存储数据类型的统一性

对于一个数据容器中要存储什么类型的数据，其实是由开发者自己决定的，在JDK5中就引入了泛型的机制。

ArrayList，它相当于给ArrayList提供了一个类型输入的模板E，E可以是任意类型的对象

```java
public class ArrayList<E>{
   transient E[] elementData; 
}
```

所谓泛型定义，其实本质上就是一种类型模板，在实际开发中，我们把一个容器或者一个对象中需要保存的属性的类型，通过模板定义的方式，给到调用者来决定，从而保证了类型的安全性。

## 泛型的定义

### 泛型类

泛型类指的是在类名后面添加一个或多个类型参数，是用于指定一个泛型类型名称的标识符。因为他们接受一个或多个参数，这些类被称为参数化的类。类型变量的表示标记，常用的是：E(element)，T（type)、K(key)，V(value)，N(number)等，这只是一个表示符号，可以是任何字符，没有强制要求。

```java
public class Response <T>{

    private T data;

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
```

### 泛型方法

泛型方法是指指定方法级别的类型参数，这个方法在调用时可以接收不同的参数类型，根据传递给泛型方法的参数类型，编译器适当地处理每一个方法调用。

下面的代码表示泛型方法的定义，用到了JDK提供的反射机制，来生成动态代理类。

```java
public interface IHelloWorld {

    String say();
}
```

定义getProxy方法，它用来生成动态代理对象，但是传递的参数类型是T，也就是说，这个方法可以完成任意接口的动态代理实例的构建。

定义getProxy方法，它用来生成动态代理对象，但是传递的参数类型是T，也就是说，这个方法可以完成任意接口的动态代理实例的构建。

在这里，我们针对IHelloWorld这个接口，构建了动态代理实例，代码如下。

```java
public class ArrayExample implements InvocationHandler {

    public <T> T getProxy(Class<T> clazz){
        // clazz 不是接口不能使用JDK动态代理
        return (T) Proxy.newProxyInstance(clazz.getClassLoader(), new Class<?>[]{ clazz }, ArrayExample.this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        return "Hello World";
    }

    public static void main(String[] args) {
        IHelloWorld hw=new ArrayExample().getProxy(IHelloWorld.class);
        System.out.println(hw.say());
    }
}
```

所有泛型方法的定义，都有一个用<>表示的类型参数声明，这个类型参数声明部分在方法返回类型之前。

每一个类型参数声明部分包含一个或多个类型参数，参数间用逗号隔开。一个泛型参数，也被称为一个类型变量，是用于指定一个泛型类型名称的标识符。

类型参数能被用来声明返回值类型，并且能作为泛型方法得到的参数类型的占位符

泛型方法体的声明和其他方法一样。注意类型参数只能代表引用型类型，不能是原始类型

## 有界类型参数

我们希望传递的参数类型属于某种类型范围，比如，一个操作数字的方法可能只希望接受Number或者Number子类的实例。

### 泛型通配符上边界

上边界，代表类型变量的范围有限，只能传入某种类型，或者它的子类。

我们可以在泛型参数上，增加一个extends关键字，表示该泛型参数类型，必须是派生自某个实现类，示例代码如下。

```java
public class TypeExample<T extends Number> {
    private T t;

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }

    public static void main(String[] args) {
        TypeExample<String> t=new TypeExample<>();
    }
}
```

### 泛型通配符下边界

下边界，代表类型变量的范围有限，只能传入某种类型，或者它的父类。

我们可以在泛型参数上，增加一个super关键字，可以设定泛型通配符的上边界。实例代码如下。

```java
public class TypeExample<T> {
    private T t;

    public T getT() {
        return t;
    }
    public void setT(T t) {
        this.t = t;
    }
    public static void say(TypeExample<? super Number> te){
        System.out.println("say: "+te.getT());
    }
    public static void main(String[] args) {
        TypeExample<Number> te=new TypeExample<>();
        TypeExample<Integer> te2=new TypeExample<>();
        say(te);
        say(te2);
    }
}
```

在say方法上声明TypeExample<? super Number> te，表示传入的TypeExample的泛型类型，必须是Number以及Number的父类类型。

![字节面试官：说说你对泛型的理解?_类型参数_02](https://s6.51cto.com/images/blog/202111/05134646_6184c5465d0244142.webp)

### 类型通配符

类型通配符一般是使用？替代具体的类型参数。例如List<?>在逻辑上是List，List等所有List<具体类型实参>的父类。

来看下面这段代码的定义，在say方法中，接受一个TypeExample类型的参数，并且泛型类型是<?>，代表接收任何类型的泛型类型参数。

```java
public class TypeExample<T> {
    private T t;

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }
    public static void say(TypeExample<?> te){
        System.out.println("say: "+te.getT());
    }
    public static void main(String[] args) {
        TypeExample<Integer> te1=new TypeExample<>();
        te1.setT(1111);
        TypeExample<String> te2=new TypeExample<>();
        te2.setT("Hello World");
        say(te1);
        say(te2);
    }
}
```

同样，类型通配符的参数，也可以通过extends来做限定，比如：

```java
public class TypeExample<T> {
    private T t;

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }
    public static void say(TypeExample<? extends Number> te){ //修改，增加extends
        System.out.println("say: "+te.getT());
    }
    public static void main(String[] args) {
        TypeExample<Integer> te1=new TypeExample<>();
        te1.setT(1111);
        TypeExample<String> te2=new TypeExample<>();
        te2.setT("Hello World");
        say(te1);
        say(te2);
    }
}
```

由于say方法中的参数TypeExample，在泛型类型定义中使用了<? extends Number>，所以后续在传递参数时，泛型类型必须是Number的子类型。

因此上述代码运行时，会提示如下错误：

```
java: 不兼容的类型: org.example.cl06.TypeExample<java.lang.String>无法转换为org.example.cl06.TypeExample<? extends java.lang.Number>
复制代码
```

### 泛型的继承

泛型类型参数的定义，是允许被继承的，比如下面这种写法。表示子类SayResponse和父类Response使用同一种泛型类型。

```java
public class SayResponse<T> extends Response<T>{
    private T ox;
}
```

## JVM是如何实现泛型的？

在JVM中，采用了类型擦除（Type erasure generics）的方式来实现泛型，简单来说，就是泛型只存在.java源码文件中，一旦编译后就会把泛型擦除.

```java
public class ArrayExample implements InvocationHandler {

    public <T> T getProxy(Class<T> clazz){
        // clazz 不是接口不能使用JDK动态代理
        return (T) Proxy.newProxyInstance(clazz.getClassLoader(), new Class<?>[]{ clazz }, ArrayExample.this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        return "Hello World";
    }

    public static void main(String[] args) {
        IHelloWorld hw=new ArrayExample().getProxy(IHelloWorld.class);
        System.out.println(hw.say());
    }
}
```

可以看到，getProxy在编译之后，泛型T已经被擦除了，参数类型替换成了java.lang.Object

编译器还会在这里插入一个类型转换的机制

## 泛型类型擦除实现带来的缺陷

- ### 不支持基本类型

  保存基本类型的数据时，又会涉及到装箱和拆箱

- ### 运行期间无法获取泛型实际类型

由于编译之后，泛型就被擦除，所以在代码运行期间，Java 虚拟机无法获取泛型的实际类型。

下面这段代码，从源码上两个 List 看起来是不同类型的集合，但是经过泛型擦除之后，集合都变为 ArrayList。所以 if语句中代码将会被执行。

```java
public static void main(String[] args) {
  ArrayList<Integer> li = new ArrayList<>();
  ArrayList<Float> lf = new ArrayList<>();
  if (li.getClass() == lf.getClass()) { // 泛型擦除，两个 List 类型是一样的
    System.out.println("类型相同");
  }
}
```

这就使得，我们在做方法重载时，无法根据泛型类型来定义重写方法。

```java
public void say(List<Integer> a){}
public void say(List<String> b){}
```

