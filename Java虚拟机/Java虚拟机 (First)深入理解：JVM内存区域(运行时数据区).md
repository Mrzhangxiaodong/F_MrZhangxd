# 一、 运行数据区
![Java虚拟机](https://github.com/Mrzhangxiaodong/F_MrZhangxd/blob/master/Java%E8%99%9A%E6%8B%9F%E6%9C%BA/images/20191004001.PNG)
## 👒线程私有的内存区域：
  程序计数器、虚拟机栈、本地方法栈
## 🌂线程共享的内存区域：
  方法区、Java堆
  
# 二、线程私有的内存区域
## 🍛2.1 程序计数器
    Program Counter，简称 PC，用于存放 下一条 指令所在单元的地址，是线程所执行的字节码的行号指示器。因为JVM的多线程是通过轮流切换来分配CPU的执行时间（时间片轮询），当切换到下一条线程的时候，线程要能知道当前要执行的字节码位置，这就要求每条线程都要有一个自己的程序计数器，独立存储待执行的虚拟机字节码指令的地址。
<pre><code>
Java自带反编译命令：
  1. javac 文件名.java
  2. java 文件名
  3. javap -c 文件名.class > 文件名.txt
  
  Compiled from "Blog.java"
  public class Blog {
    public Blog();
      Code:
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return

    public static void main(java.lang.String[]);
      Code:
         0: bipush        10
         2: istore_1
         3: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
         6: iload_1
         7: invokevirtual #3                  // Method java/io/PrintStream.println:(I)V
        10: return
  }
</code></pre>
<span>备注：上面的数字就是程序计数器</span>
## 🍮2.2 虚拟机栈
![](https://github.com/Mrzhangxiaodong/F_MrZhangxd/blob/master/Java%E8%99%9A%E6%8B%9F%E6%9C%BA/images/20191004002.PNG)<br>
  **虚拟机栈**生命周期同线程,所以不必担心垃圾回收问题。虚拟机栈(VM Stack)这个内存区域对应的是每个线程class方法执行的内存模型（不仅Java，像Kotlin、Scala、Groovy等于都可以编译成class文件并允许在JVM上）。线程中的每个方法在执行的同时都会创建一个 栈帧（Stack Frame）。每个class方法从被调用到执行完成的过程，都对应着一个栈帧在虚拟机栈从入栈到出栈的过程。<br>
  **栈帧**是用来存储 （方法内的）局部变量表、（计算时的）操作数栈、（引用的）动态链接、（进入或退出方法时的）方法出入口等。<br>
  * **局部变量表：** 存放 编译期 可知的数据类型，包括 基本数据类型（boolean、byte、char、short、int、long、float、double）、对象引用（reference类型）、returnAddress类型（指向一条字节码指令的地址）。每个局部变量空间（slot）的长度是 32位，而 long、double类型的每个变量占用 2个局部变量空间，也就是会占用64位空间。局部变量表所需的内存大小在编译期就已经确定，并记录在class文件中。<br>
  * **操作数栈：** 栈是一个先进后出的线性表，操作数栈是逻辑计算的数据容器，比如下面的计算：
    <pre>
    <code>
      int a = 10;
      int b = 20;
      int c = 10 + 20;
      return c;
    </code>
    </pre>
## 🍈2.3本地方法栈
    本地方法栈和虚拟机栈相似，只不过虚拟机栈是用于线程中执行Java方法（字节码，因为还有Scala、Kotlin、Groovy等其他运行在虚拟机上的编程语言），本地方法栈是用于执行本地的 native方法 。
# 三、线程共享的内存区域
## 🍊3.1 堆
    堆Heap又称 Java堆，这个内存区域是JVM线程共享的区域，也是JVM管理的最大一块内存区域。堆内存区域创建于JVM启动时。

    堆的 唯一 目的就是存放 实例对象，反之不成立，也就是说有少量的对象实例并不在Java堆中存放，也可以在栈上分配，Java堆仅存放绝大量的对象实例。

    Java堆 不像 栈 那样，随线程而生、随线程而亡。Java堆中的实例对象可能被多个线程中的变量所引用，一个线程死亡，而其他线程还可能正使用此实例对象，所以 Java堆 成为垃圾收集器收集垃圾一个重要的区域（宏观来讲是垃圾收集器管理的主要区域）。

    现在主流的垃圾收集器采用 分代收集算法，堆内存又被细分为新生代(Young Generation) 和 老年代(Old Generation)(默认比: 1:2)。新生代 再细分为：1个Eden空间和2个Survivor空间（默认比例8:1:1）,2个Survivor空间其中一个是From Survivor空间和一个To Survivor 空间。分代分空间的唯一理由就是优化GC性能。
  常量池
  * 静态常量池：即*.class文件中的常量池，class文件中的常量池不仅仅包含字符串(数值)字面量，还包含类、方法的信息，占用class文件绝大部分空间；<br>
  * 运行时常量池：则是JVM虚拟机在完成 类装载 操作后，将class文件中的常量池载入到内存中，并保存在 方法区 中，我们常说的 常量池，就是指方法区中的运行时常量池，存放在字面量和符号引用。<br>
## 🍓3.2方法区
  **方法区**用来存储类信息（类的版本、接口描述）、常量、静态变量、方法（数据与编译后代码）等数据，也有一些方法区的数据生命周期长，进而将方法区称为“永久代（Permanent Generation）”，永久存在不被GC回收，但本质上并不相等。方法区在逻辑上是和Java堆相连的，该区域一般GC很少参与，偶尔会存在对不使用的常量进行内存回收，对于一些卸载的类进行资源回收，因为这些数据占用内存本来就比较少，所以GC的回收效果也非常的一般。
  
  * JDK1.6的永久代（PermGen）
  * JDK1.7的永久代（PermGen）（过度阶段）
  * JDK1.8的元空间（Metaspace）（直接内存）

  用jdk1.6运行后会报错，永久代这个区域内存溢出会报： Exception in thread “main” java.lang.OutOfMemoryError:PermGen space的内存溢出异常，表示永久代内存溢出。

  在Java7之前，HotSpot虚拟机中将GC分代收集扩展到了方法区，使用永久代来实现了方法区。这个区域的内存回收目标主要是 针对常量池的回收和对类型的卸载。但是在之后的HotSpot虚拟机实现中，逐渐开始将方法区从永久代移除。Java7中已经将运行时常量池从永久代移除，在Java 堆（Heap）中开辟了一块区域存放运行时常量池。而在Java8中，已经彻底没有了永久代，将方法区直接放在一个与堆不相连的本地内存区域，这个区域被叫做元空间（MetaSpace）。

  使用jdk1.7后 验证如下：执行代码和上面相同 设置参数：-Xmx20m -Xms20m -XX:-UseGCOverheadLimit，这里的-XX:-UseGCOverheadLimit是关闭GC占用时间过长时会报的异常，然后限制堆的大小，运行程序，果然，一会后报异常：

  Exception in thread “main” java.lang.OutOfMemoryError: Java heap space 从上面的异常可以知道我们测试增加的常量都放到了堆中，所以限制堆内存以后，不断增加常量，堆内存会溢出。

  总结：jdk1.6常量池放在方法区，jdk1.7常量池放在堆内存，jdk1.8放在元空间里面，和堆相独立。所以导致String的intern()方法因为以上变化在不同版本会有不同表现。
