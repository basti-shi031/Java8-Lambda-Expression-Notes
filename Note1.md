# Java8 lambda表达式

## 前言
RxJava的逻辑清晰的特点在Java8 lambda表达式下更加突出，学好RxJava必须熟悉lambda表达式。
## 本质
lambda表达式的本质是一个匿名方法
### 举例
问题：求两个int型数据的和，通常的写法是
```java
public int add(int x,int y){
  return x+y;
}
```

转成lambda以后
```java
(int x,int y)->x+y;
```
或者，省略参数类型，java编译器会根据上下文推断
```java
(x,y)->x+y;
```
或者,可以显示的指明返回值
```java
(x,y)->{return x+y;}
```
###小结
lambda表达式由三部分组成：参数列表，箭头，表达式/语句块、

无参数的情况
```java
()->{Log.i("TAG","Message");}
```
如果只有一个参数且可以被java推断出类型，那么，参数列表的括号也可以省略：
c->{return c.size;}

## 类型
lambda表达式的目标类型是“函数接口”，定义是：一个接口，如果只有一个显示声明的抽象方法，那么它就是一个函数接口，最简单的：
```java
public interface MyInterface{
  int getInt();
}
```
或者
```java
public interface MyInterface{
  void doSomething();
}
```
特殊情况
```java
public interface Comparator<T>{
  int compare(T o1,T o2);
  boolean equals(Object obj);
}
```
注意接口Comparator，虽然声明了两个方法，貌似与定义不符（只有一个声明的抽象方法），但它的确是一个函数接口，这是因为equals方法是Object的。事实上所有的接口都会声明Object的public方法（大多是隐式的）。

## 赋值
```java
MyInterface myInterface = ()->{Log.i("Tag","Message");}
```
然后再赋值给一个Object:
```java
Object obj = myInterface;
```

但是不能这样赋值
```java
Object obj = ()->{Log.i("Tag","Message");}
```
必须显示的转型成为一个函数接口才可以
```java
Object obj = (myInterface)()->{Log.i("Tag","Message");}
```
## 使用
### lambda表达式用在何处
举例：未使用Lambda
```java
Thread t = new Thread( new Runnable () {
  @Override
  public void run() {
      Log.i("Tag","Message");
  }
});
```
使用lambda
```java
Thread t = new Thread{()->{Log.i("Tag","Message");}};
```
注意第二个线程里的lambda表达式，并不需要将其转成Runnable,因为Java能够根据上下文自动推断，
一个Thread的构造函数接受一个Runnable参数，且传入的Lambda表达式正好符合其run()函数，所以java编译器可以推断为Runnable。

### 集合批处理操作
```java
for (Object o:list ) {
  Log.i("TAG",o);
}
```
可转换为
```java
list.forEach(o->{Log.i("TAG",o)};)
```

### 流
一个流通常以一个集合类实例为其数据源，然后在其上定义各种操作。流的API设计使用了管道模式，对流的一次操作为返回另一个流，从而不同的操作可以串起来。
```java
List<Shape> shapes = ...;
shape.stream()
  .filter(s->s.getColor() == BLUE)
  .forEach(s->s.setColor(RED));
```
首先调用stream()方法，以集合类对象shapes里面的元素为数据源，生成一个流，然后在这个流上调用filter方法，跳出蓝色的，返回一个流，最后调用forEach方法将这些蓝色的shape换成红色。
