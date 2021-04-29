# Java新特性

## 1、Java8

### 1.1、 Lambda

#### 1.1.1、概念

Java8 中引入了一个新的操作符"->"，该操作符称为箭头操作符或 Lambda 操作符。箭头操作符将 Lamdba 表达式拆成两部分：

- 左侧 Lambda 表达式的参数列表
- 右侧 Lambda 表达式中所需执行的功能，即 Lambda 体

#### 1.1.2、前提条件

使用 Lambda 表达式需要“函数式接口”的支持，“函数式接口” 就是只有一个抽象方法的接口。可以使用注解@FunctionalInterface  修饰，来检查是否是函数式接口。==Lamdba 表达式的本质：作为函数式接口的实例==

```java
// 定义一个函数式接口
@FunctionalInterface
interface haha {
	void ha();
}
```

#### 1.1.3、Java 内置四大核心函数式接口

| 函数式接口                    | 参数类型 | 返回类型 | 用途                                                         |
| ----------------------------- | -------- | -------- | ------------------------------------------------------------ |
| Consumer<T><br />消费型接口   | T        | void     | 对类型为T的对象应用操作，包含方法：<br />void accept(T t)    |
| Supplier<T><br />供给型接口   | 无       | T        | 返回类型为T的对象，包含方法：<br />T get()                   |
| Function<T,R><br />函数型接口 | T        | R        | 对类型为T的对象应用操作，并且返回结果。结果是R类型的对象。包含方法：<br />R apply(T t) |
| Predicate<T><br />断定型接口  | T        | boolean  | 确定类型为T的对象是否满足约束，并返回boolean 值。包含方法：<br />boolean test(T t) |

#### 1.1.4、什么时候需要使用 Lamdba 表达式？

当需要对一个函数式接口实例化的时候，不想用匿名内部类就可以使用 Lamdba 表达式。

#### 1.1.5、什么时候需要使用jdk内置的函数式接口？

如果我们开发中需要定义一个函数式接口，首先看看在已有的JDK提供的函数式接口中是否提供了能满足需求的函数式接口。如果有，则直接调用即可，不满足再自定义。

#### 1.1.6、基本使用

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

#### 1.1.7、方法引用

若 Lambda 体中的内容有方法已经实现了，我们可以使用“方法引用”。“方法引用”可以理解是 Lamdba 表达式的另外一种表现形式。

主要有三种语法格式：

- 对象::实例方法
- 类::静态方法名
- 类::实例方法名

前提条件：

引用的方法，要和抽象方法的参数和返回值类型一样。

> 语法格式一：对象::实例方法名

```java
// 普通 Lambda 写法
Consumer<String> consumer = (x) -> System.out.println(x);
consumer.accept("hello");

// 方法引用 Lambda 写法
Consumer<String> consumer2 = System.out::println;
consumer2.accept("hello");
```

> 语法格式二：类::静态方法名

```java
// 普通 Lambda 写法
Comparator<Integer> com = (x, y) -> Integer.compare(x, y);

// 方法引用 Lambda 写法
Comparator<Integer> com2 = Integer::compare;
```

> 语法格式三：类::实例方法名

前提条件：

若 Lamdba 参数列表中的第一个参数是 实例方法的调用者，而第二个参数是实例方法的参数时，可以使用

类::实例方法名 这种写法

```java
// 普通 Lambda 写法
BiPredicate<String, String> biPredicate = (x, y) -> x.equals(y);

// 方法引用 Lambda 写法
BiPredicate<String, String> biPredicate2 = String::equals;
```

#### 1.1.8、构造器引用

格式：

类名::new

前提条件:

需要调用的构造器的参数列表要与函数式接口中抽象方法的参数列表一致

```java
// 例子1
// 普通 Lambda 写法
Supplier<Object> supplier = () -> new Object();
Object o = supplier.get();

// 构造器引用 Lambda 写法
Supplier<Object> supplier2 = Object::new;
Object o2 = supplier2.get();

// 例子2

class Person {
	private String name;
	private int age;

	public Person() {
	}

	public Person(String name) {
		this.name = name;
	}

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public String toString() {
		return "Person{" +
		"name='" + name + '\'' +
		", age=" + age +
		'}';
    }
}
// 普通 Lambda 写法,调用一个参数的构造方法
Function<String, Person> function1 = (x) -> new Person(x);
Person person = function1.apply("zhangsan");
System.out.println(person);

// 构造器引用 Lambda 写法，自动调用一个参数的构造方法
Function<String, Person> function2 = Person::new;
Person person2 = function2.apply("zhangsan");
System.out.println(person2);

// 普通 Lambda 写法,调用两个参数的构造方法
BiFunction<String, Integer, Person> biFunction1 = (x, y) -> new Person(x, y);
Person person = biFunction1.apply("zhangsan", 18);
System.out.println(person);

// 构造器引用 Lambda 写法，自动调用两个参数的构造方法
BiFunction<String, Integer, Person> biFunction2 = Person::new;
Person person2 = biFunction2.apply("zhangsan", 19);
System.out.println(person2);

```

#### 1.1.9、数组引用

格式:

Type[]::new

```java
// 普通 Lambda 写法
Function<Integer, String[]> function = (x) -> new String[x];
String[] strings = function.apply(10);
System.out.println(strings.length);

// 数组引用 Lambda 写法，
Function<Integer, String[]> function2 = String[]::new;
String[] strings2 = function2.apply(10);
System.out.println(strings2.length);
```



### 1.2、 stream

#### 1.2.1、概念

stream 是Java8 提供的一套API。使用这套 API 可以对内存中的数据进行过滤、排序、映射、归约等操作。

#### 1.2.2、注意点：

- stream 自己不会存储元素
- stream 不会改变源对象。相反，他们会返回一个持有结果的新的stream
- stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行

#### 1.2.3、使用流程：

1. stream 的实例化
2. 一系列的中间操作（过滤、映射、。。。）
3. 终止操作

#### 1.2.4、使用流程的注意点：

- 一个中间操作链，对数据源的数据进行处理

- 一旦执行终止操作，就执行中间操作链，并产生结果。之后，不会再被使用

#### 1.2.5、创建 stream 的几种方式

```java
// 1.通过 Collection 系列集合提供的 stream() 或者 parallelStream()
ArrayList<String> list = new ArrayList<>();
Stream<String> stream1 = list.stream();

// 2.通过 Arrays 中的静态方法 stream()
String[] array = new String[10];
Stream<String> stream2 = Arrays.stream(array);

// 3.通过 Stream 类中的静态方法 of()
Stream<String> stream3 = Stream.of("a", "b", "c");

// 4.创建无限流
Stream<Integer> stream4 = Stream.iterate(0, (x) -> x + 2);
stream4.limit(10).forEach(System.out::println);

// 5.生成
Stream<Double> stream5 = Stream.generate(() -> Math.random());
stream5.limit(10).forEach(System.out::println);
```

#### 1.2.6、中间操作

> 筛选与切片

filter 接受 Lambda，从流中排除某些元素

limit 截断流，使其元素不超过给定的数量

skip(n) 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流，与 limit(n) 互补

distinct 筛选，通过流所生产元素的 hashCode() 和 equals() 去除重复元素

```java




```















