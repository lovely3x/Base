
### 1.以下代码有问题吗，是什么问题？
1.1
```java
List<Object> obj = new ArrayList<Long>();
obj.add("I love Android!");
```

1.2
```java
Object[] objArray = new Long[1];
objArray[0] = "I love Android!";
```

结果:   
1.1 编译出错   
1.2 编译可以，运行出错

原因：   
1.1 List\<Object\>和ArrayList\<Long\>没有继承关系
1.2 数组是在运行时类型检查

考察点：泛型、数组

### 2.以下代码运行结果？
2.1
```java
Long l1 = 128L;
Long l2 = 128L;
System.out.print(l1 == l2);
System.out.print(l1 == 128L);
```

2.2
```java
Long l1 = 127L;
Long l2 = 127L;
System.out.print(l1 == l2);
System.out.print(l1 == 127L);
```

结果：   
2.1 false  true   
2.2 true  true

原因：   
2.1 l1和l2是两个对象，==比较的是对象的地址，所以false。Long l1== 128L中l1转值比较，所以true。   
2.2 -128 - 127之间的值，Java维护在一个常量池中，所以l1和l2引用同一个对象。

考察点：常量池、==、自动装箱拆箱

### 3.程序可以编译运行吗，结果是神马？
```java
List<String>[] list = new List<String>[3];
```

结果：	
不可以运行，编译报错。

原因：	
创建泛型、参数化类型或者类型参数的数组是非法的，都会导致编译错误。

考察点：Java默认是没有泛型数组的。

### 4.是否存在使 i>j || i<=j 结果为false的数吗？
结果：	
存在。
  
原因：	
数值NaN代表not a number，无法用于比较，例如即使是 i = Double.NaN; j = i; 最后i == j的结果依旧为false。
  
考察点：Java基础知识点NaN。

### 5.程序运行结果？
```java
public class Main {

    public static void main(String[] args) {
        TxtThread txtThread = new TxtThread();

        new Thread(txtThread).start();
        new Thread(txtThread).start();
        new Thread(txtThread).start();

    }

    static class TxtThread implements Runnable {

        int num = 100;
        String str = new String();

        @Override
        public void run() {
            synchronized (str) {
                while (num > 0) {
                    try {
                        Thread.sleep(1);
                    } catch (InterruptedException ex) {
                        Logger.getLogger(TxtThread.class.getName()).log(Level.SEVERE, null, ex);
                    }
                    System.out.println(Thread.currentThread().getName() + " this is " + num--);
                }
            }
        }
    }

}
```
结果：	
Thread-0 this is 100	
......	
......	
Thread-0 this is 1	

原因：	
三个线程共享对一个Runnable对象，所以同步锁中其他两个线程没有执行机会。

考察点：	
Thread,Runnable,同步。

### 6.Java的垃圾回收面试题
6.1
```java
 1．fobj = new Object ( ) ;   
 2．fobj. Method ( ) ;   
 3．fobj = new Object ( ) ;   
 4．fobj. Method ( ) ;   
```
问：这段代码中，第几行的fobj 符合垃圾收集器的收集标准？	
 
答：第3行。因为第3行的fobj被赋了新值，产生了一个新的对象，即换了一块新的内存空间，也相当于为第1行中的fobj赋了null值。这种类型的题是最简单的。 

6.2
```java
1．Object sobj = new Object ( ) ;   
2．Object sobj = null ;   
3．Object sobj = new Object ( ) ;   
4．sobj = new Object ( ) ;   
```
问：这段代码中，第几行的内存空间符合垃圾收集器的收集标准？ 	

答：第2行和第4行。因为第2行为sobj赋值为null，所以在此第1行的sobj符合垃圾收集器的收集标准。而第4行相当于为sobj赋值为null，所以在此第3行的sobj也符合垃圾收集器的收集标准。

6.3
```java
1．Object aobj = new Object ( ) ;   
2．Object bobj = new Object ( ) ;   
3．Object cobj = new Object ( ) ;   
4．aobj = bobj;   
5．aobj = cobj;   
6．cobj = null;   
7．aobj = null; 
```
问：这段代码中，第几行的内存空间符合垃圾收集器的收集标准？ 

答：第4，7行。注意这类题型是认证考试中可能遇到的最难题型了。 
行1-3：分别创建了Object类的三个对象：aobj，bobj，cobj。
行4：此时对象aobj的句柄指向bobj，原来aojb指向的对象已经没有任何引用或变量指向，这时，就符合回收标准。
行5：此时对象aobj的句柄指向cobj，所以该行的执行不能使aobj符合垃圾收集器的收集标准。 
行6：此时仍没有任何一个对象符合垃圾收集器的收集标准。 
行7：对象cobj符合了垃圾收集器的收集标准，因为cobj的句柄指向单一的地址空间。在第6行的时候，cobj已经被赋值为null，但由cobj同时还指向了aobj（第5行），所以此时cobj并不符合垃圾收集器的收集标准。而在第7行，aobj所指向的地址空间也被赋予了空值null，这就说明了，由cobj所指向的地址空间已经被完全地赋予了空值。所以此时cobj最终符合了垃圾收集器的收集标准。 但对于aobj和bobj，仍然无法判断其是否符合收集标准。总之，在Java语言中，判断一块内存空间是否符合垃圾收集器收集的标准只有两个： 
1．给对象赋予了空值null，以下再没有调用过。 
2．给对象赋予了新值，既重新分配了内存空间。 

### 7.以下程序运行的结果是神马？
```java
String s1 = "abc";
StringBuffer s2 = new StringBuffer(s1);
System.out.println(s1.equals(s2));
```
结果：	
false。

原因:	
考String的toString方式，toString方法进行了instance of判断。


### 8.下面的题目结果是什么
```java
public class TestFool {

    public static String foo(){
        System.out.println("Test foo called");
        return "";
    }

    public static void main(String[] args) {
        TestFool obj = null;
            System.out.println(obj.foo());

    }
}
```
结果：	
Test foo called。

原因:	
jvm内存里有栈区、堆区。栈区主要用来存放基础类型数据和局部变量，堆区主要存放new出来的对象。在堆区又有一个叫做方法区的内存区域用来存放常量、static变量和static方法，还有类的信息。static的变量和方法不依赖对象，即使对象没有创建，在类加载的时候已经存在信息了，jvm识别出是static方法就直接调用了在方法区内存里的方法，没有报空指针异常。


### 9.以下继承关系， 运行结果是？
```java
	public class Base {
        public Base(){
            test();
        }
        
        public void test(){
        }
    }
    
    public class Child extends Base {
        private int a = 123;
        
        public Child(){
        }
        
        public void test(){
            System.out.println(a);
        }
    }
    
    public static void main(String[] args){
        Child c = new Child();
        c.test();
    };
```
结果：
0 123

原因：
 Child直接继承于Base，默认构造函数不显示调用super也会直接父类的默认构造函数， 所以首先调用Base.java-->test()方法。这时Child类还没有构造完毕，a基本数据类型还没有赋予值，a又为成员变量默认值为0； 

### 10.以下继承关系， 运行结果是？
```java
	public class Base {
		public static String s = "static_base";
		public String m = "base";
		
		public static void staticTest(){
			System.out.println("base static: "+s);
		}
	}
	public class Child extends Base {
		public static String s = "child_base";
		public String m = "child";
		
		public static void staticTest(){
			System.out.println("child static: "+s);
		}
	}
	public static void main(String[] args) {
		Child c = new Child();
		Base b = c;
		
		System.out.println(b.s);
		System.out.println(b.m);
		b.staticTest();
		
		System.out.println(c.s);
		System.out.println(c.m);
		c.staticTest();
	}
```
结果：
static_base
base
base static: static_base
child_base
child
child static: child_base

原因：~~~

### 11.如果出现以下代码， 在编译阶段会出现什么现象？
```java
	public class Base {
		protected void protect(){
		}
		
		public void open(){        
		}
	}
	public class Child extends Base {
		//1放开会有啥现象
	//    private void protect(){
	//    }
		
		//2放开会有啥现象
	//    protected void open(){        
	//    }
		//3放开会有啥现象
		public void protect(){        
		}
	}
```

现象:

如果1放开， 会在编译阶段报错；

如果2放开， 会在编译阶段报错；

如果3放开， 编译正常；

原因： 
重写方法不能比被重写方法限制有更严格的访问级别。

### 12.以下继承方式， 运行时会如何打印
```java
	public class Base {
		public static int s;
		private int a;

		static {
			System.out.println("基类静态代码块, s:" + s);
			s = 1;
		}

		{
			System.out.println("基类实例代码块, a:" + a);
			a = 1;
		}

		public Base() {
			System.out.println("基类构造方法, a:" + a);
			a = 2;
		}

		protected void step() {
			System.out.println("base s: " + s + " , a : " + a);
		}

		public void action() {
			System.out.println("start");
			step();
			System.out.println("end");
		}
	}


	public class Child extends Base {

		public static int s;
		private int a;
		static {
			System.out.println("子类静态代码块， s:"+s);
			s = 10;
		}

		{
			System.out.println("子类实例代码块， a:"+a);
			a = 10;
		}

		public Child(){
			System.out.println("子类构造方法，a："+a);
			a = 20;
		}

		protected  void  step(){
			System.out.println("child s: "+s+" , a: "+a);
		}

	}

	public static void main(String[] args) {
        System.out.println("------ new Child()");
        Child c = new Child();
        System.out.println("\n------ c.action()");
        c.action();

        Base b = c;
        System.out.println("\n-------b.action()");
        b.action();

        System.out.println("\n ------- b.s: "+b.s);
        System.out.println("\n ------- c.s: "+c.s);
    }
```

结果:
------ new Child()
基类静态代码块, s:0
子类静态代码块， s:0
基类实例代码块, a:0
基类构造方法, a:1
子类实例代码块， a:0
子类构造方法，a：10

------ c.action()
start
child s: 10 , a: 20
end

-------b.action()
start
child s: 10 , a: 20
end

 ------- b.s: 1

 ------- c.s: 10
 
原因:

 lueluelue....

### 13.内部类你知多少？
java 内部类分为几种，各种自己有哪些特性？
 
答案：
四中内部类： 静态内部类, 成员内部类, 方法内部类, 匿名内部类.

匿名内部类访问外部类的局部变量，局部变量需要final修饰，1.8之后该关键字被省略

方法内部类只能在定义该内部类的方法内实例化，不可以在此方法外对其实例化。

静态内部类与其他成员相似，可以用static修饰内部类，这样的类称为静态内部类。静态内部类与静态内部方法相似，只能访问外部类的static成员，不能直接访问外部类的实例变量，与实例方法，只有通过对象引用才能访问。

成员内部类：成员内部类没有用static修饰且定义在在外部类类体中。

### 14.枚举比较来了。
```java
	public enum Size {
		SMALL, MEDIUM, LARGE
	}

	Size size = Size.SMALL;
	System.out.println(size==Size.SMALL);
	System.out.println(size.equals(Size.SMALL));
	System.out.println(size==Size.MEDIUM);
	System.out.println(size.toString());
	System.out.println(size.name());
```

答案：
true
true
false
SMALL
SMALL

原因：

==与equals()比较的一样， 枚举的toString就是它的名字；

### 14. try{}catch与return的玩法/ 描述这些方法调用执行流程和最终返回值！
```java
	public static int test(){
		int ret = 0;
		try{
			return ret;
		}finally{
			ret = 2;
		}
	}

	public static int test(){
		int ret = 0;
		try{
			int a = 5/0;
			return ret;
		}finally{
			return 2;
		}
	}


	public static void test(){
		try{
			int a = 5/0;
		}finally{
			throw new RuntimeException("hello");
		}
	}
```

结果：

1、返回0，执行到try的return ret;语句前，会先将返回值ret保存在一个临时变量中，然后才执行finally语句，最后try再返回那个临时变量，finally中对ret的修改不会被返回。

2、返回2，5/0触发ArithmeticException，但是finally中有return语句，finally中有return不仅会覆盖try和catch内的返回值，还会掩盖try和catch内的异常，就像异常没有发生一样，所以这个方法就会返回2，而不再向上传递异常了。

3、运行抛出hello 异常，因为如果finally中抛出了异常，则原异常就会被掩盖。

总结： 为避免混淆，应该避免在finally中使用return语句或者抛出异常，如果调用的其他代码可能抛出异常，则应该捕获异常并进行处理。

### 15.java1.5开始的自动装箱和开箱机制是编译器特性还是虚拟机运行时特性？分别是怎么实现的？
```java
Integer i1 = 100;
        Integer i2 = 100;
        Integer i3 = 200;
        Integer i4 = 200;
        System.out.println(i1 == i2);
        System.out.println(i3 == i4);

        Double d1 = 100.0;
        Double d2 = 100.0;
        Double d3 = 200.0;
        Double d4 = 200.0;
        System.out.println(d1 == d2);
        System.out.println(d3 == d4);

        Boolean b1 = false;
        Boolean b2 = false;
        Boolean b3 = true;
        Boolean b4 = true;
        System.out.println(b1 == b2);
        System.out.println(b3 == b4);

        Integer a = 1;
        Integer b = 2;
        Integer c = 3;
        Integer d = 3;
        Integer e = 321;
        Integer f = 321;
        Long g = 3L;
        Long h = 2L;
        System.out.println(c == d);
        System.out.println(e == f);
        System.out.println(c == (a + b));
        System.out.println(c.equals(a + b));
        System.out.println(g == (a + b));
        System.out.println(g.equals(a + b));
        System.out.println(g.equals(a + h));
```

打印结果：
true
false
false
false
true
true
true
false
true
true
true
false
true

原因:

Integer类型： -128~127之前会从常量池中直接获取， 当我们执行Integer i2 = 100;的时候， 实际上会最终调用Interget.valueOf(100)方法， 这个方法的实现就是判断实例出来的数值是否在-128到127之间， 如果在的话， 就直接从常量池获取， 而不new对象

Double类型： 与Integer类型一样， 都会调用到Double的valueOf方法。  Double的区别在于， 不管传入的数值是多少， 都会new创建一个对象来表达该数值。d1与d2都new了对象出来，对象无重复，自然就不相等；（打印false）

Boolean类型： 与上面的类型一样，都会调用到Boolean的valueOf方法。 而Boolean变量内部不会new对象来保存，Boolean内部维护这两个不可变更的 public static final Boolean TRUE/false = new Boolean(true/false);变量。



