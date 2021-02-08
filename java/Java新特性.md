# Java新特性

## 1、Java8

### 1.1、 Lambda

#### 1.1.1、基础语法

Java8 中引入了一个新的操作符"->"，该操作符称为箭头操作符或 Lambda 操作符。箭头操作符将 Lamdba 表达式拆成两部分：

- 左侧 Lambda 表达式的参数列表
- 右侧 Lambda 表达式中所需执行的功能，即 Lambda 体

#### 1.1.2、前提条件

使用 Lambda 表达式需要“函数式接口”的支持，“函数式接口” 就是只有一个抽象方法的接口。可以使用注解@FunctionalInterface  修饰，来检查是否是函数式接口

```java
// 定义一个函数式接口
@FunctionalInterface
interface haha {
	void ha();
}
```

> 语法格式一：无参数，无返回值

```java
// 语法
() -> System.out.println("hello");

// 普通写法
Runnable r1 = new Runnable() {
	@Override
    public void run() {
        System.out.println("hello");
    }
};
new Thread(r1).start();

// Lambda 表达式的写法：
Runnable r2 = () -> System.out.println("hello");
new Thread(r2).start();
```

> 语法格式二：有一个参数，无返回值

```java
// 语法
(x) -> System.out.println("hello");

// 普通写法
Consumer<String> consumer = new Consumer<String>() {
	@Override
	public void accept(String s) {
		System.out.println(s);
	}
};
consumer.accept("hello");

// Lambda 表达式的写法：
Consumer<String> consumer2 = (x) -> System.out.println("hello");
consumer2.accept("hello");
```

> 语法格式三：有一个参数，无返回值，小括号可以不写

```java
// 语法
x -> System.out.println("hello");
// 普通写法
Consumer<String> consumer = new Consumer<String>() {
	@Override
	public void accept(String s) {
		System.out.println(s);
	}
};
consumer.accept("hello");

// Lambda 表达式的写法：
Consumer<String> consumer2 = x -> System.out.println("hello");
consumer2.accept("hello");
```

> 语法格式四：有两个以上的参数，有返回值，并且 Lambda 体中有多条语句

```java
// 语法
(x,y) -> {
 	语句1;
    语句2;
};

// 普通写法
Comparator<Integer> comparator = new Comparator<Integer>() {
	@Override
	public int compare(Integer x, Integer y) {
		return Integer.compare(x, y);
	}
};

// Lambda 表达式的写法：
Comparator<Integer> comparator2 = (x, y) -> {
	System.out.println("hello");
	return Integer.compare(x, y);
};
```

> 语法格式五：若 Lambda 体中只有一条语句，return 和大括号都可以省略不写

```java
// 语法
(x,y) -> System.out.println("hello");

// 普通写法
Comparator<Integer> comparator = new Comparator<Integer>() {
	@Override
	public int compare(Integer x, Integer y) {
		return Integer.compare(x, y);
	}
};

// Lambda 表达式的写法：
Comparator<Integer> comparator2 = (x, y) -> Integer.compare(x, y);
```

> 语法格式六：Lambda 表达式的参数列表的数据类型可以省略不写

```java
// 语法
(x,y) -> System.out.println("hello");

// 普通写法
Comparator<Integer> comparator = new Comparator<Integer>() {
	@Override
	public int compare(Integer x, Integer y) {
		return Integer.compare(x, y);
	}
};

// Lambda 表达式的写法：
Comparator<Integer> comparator2 = (x, y) -> Integer.compare(x, y);
```



### 1.2、 stream





