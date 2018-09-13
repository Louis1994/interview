# Java虚拟机内部体系结构

**JVM(Java虚拟机)是：**

1. 指定Java虚拟机的工作的规范。 但实现提供程序是独立的选择算法。 其实现是由Sun和其他公司提供。
2. 它的实现被称为JRE(Java运行时环境)。
3. 运行时实例只要在命令提示符上编写java命令来运行java类，就会创建JVM的实例。

JVM执行以下操作：

- 加载代码
- 验证代码
- 执行代码
- 提供运行时环境

JVM提供了以下定义：

- 内存区
- 类文件格式
- 寄存器集合
- 垃圾收集堆
- 致命错误报告等



## JVM(Java虚拟机)内部体系结构

下面让我们来了解JVM的内部架构。它包含类加载器，内存区域，执行引擎等。 

![img](http://www.yiibai.com/uploads/images/201703/0103/839180303_84772.jpg) 

**1)类加载器**

`Classloader`是JVM的一个子系统，用于加载类文件。

**2)类(方法)区域**

类(方法)区域存储每个类结构，例如运行时常量池，字段和方法数据，方法的代码。

**3)堆**

它是分配对象的运行时数据区。

**4)堆栈**
Java堆栈存储帧。它保存局部变量和部分结果，并在方法调用和返回中起作用。
每个线程都有一个私有JVM堆栈，同时创建线程。每次调用方法时都会创建一个新的框架。 框架在其方法调用完成时被销毁。

**5)程序计数器寄存器**

PC(程序计数器)寄存器。 它包含当前正在执行的Java虚拟机指令的地址。

**6)本地方法堆栈**

它包含应用程序中使用的所有本地方法。

**7)执行引擎**

执行引擎包含：

1. **虚拟处理器**
2. **解释器**：读取字节码流，然后执行指令。
3. **即时(JIT)编译器**：它用于提高性能，JIT编译的同时有类似字节代码部分的功能，从而减少编译所需的时间。**编译器**是指从Java虚拟机(JVM)的指令集到特定CPU的指令集的转换器。



# 基本概念之 JVM 结构

用 Java 编写的代码是按照以下流程执行的。

![image](http://note.youdao.com/yws/api/personal/file/WEBc531ccdb6d6724fe773f600d3de30de3?method=download&shareKey=86fc881bfe66aeb4368f09851a74bee0) 

类装载器将编译好的 Java 字节码（Java Bytecode）加载到运行时数据区（Runtime Data Areas），执行引擎运行 Java 字节码。

#### 类装载器（Class Loader）

Java 提供了动态加载特性；它在运行时，而不是编译时第一次使用类时对类进行装载和链接处理。JVM 类装载器执行动态加载。Java 类装载器的特性如下：

- **层次化结构（Hierarchical Structure）**：Java 里的类装载器以父子关系的层次结构来组织。启动类装载器（Bootstrap Class Loader）是所有类装载器的父装载器。
- **代理模式（Delegation Mode）**：基于这个层次结构，装载在不同类装载器之间进行代理。当一个类在装载时，它的父装载器会检查并确定这个类是否在父装载器中。如果上层的装载器里有这个类，那么这个类就会被加载，如果没有，那么类装载器会请求加载这个类。
- **可见受限（Visibility Limit）**：子的类装载器可以在它的父装载器内找到类（即，父装载器中的类对子装载器可见），而子装载器中的类对父装载器是不可见的。
- **严禁卸载（Unload is Not Allowed）**：类装载器可以装载一个类，但是不能卸载它。不过，可以删除当前的类装载器，并创建一个新的。

每个类装载器有它的命名空间，存储着装载的类。当类装载器装载一个类时，它基于存储在命名空间下完整有效的类名称（FQCN - Fully Qualified Class Name）来检查判断一个类是否以及被装载。即使类有完全相同的 FQCN 但命名空间不同，那么它也会被认为是一个不同的类。不同的命名空间表明类是通过另一个类装载器加载的。

下图展示了一个类装载器的代理模型。

![image](http://note.youdao.com/yws/api/personal/file/WEB7a7aefc383757b42fd9d6d894a3a6055?method=download&shareKey=fac525a054a90b0f4611fbf8f74a092c) 

当类装载器接受一个类加载的请求的时候，它会按以下顺序检查：这个类是否存在于类装载器的缓存中，是否在父装载器中，是否已被自己加载。简而言之，它会先检查这个类是否已经被加载到装载器的内存中，如果没有，它会检查父类装载器。如果这个类无法在启动类装载器中找到，那么被请求的类装载器会从文件系统查找这个类。

- **启动类装载器（Bootstrap class loader）**：它在 JVM 启动时被创建。它加载 Java API，包括对象类。与其他的装载器不同，它是用原生代码实现的，不是 Java 。
- **扩展类装载器（Extension class loader）**：它用来加载基本 Java API 以外的扩展类。它同时也加载各种安全相关的扩展功能。
- **系统类装载器（System class loader）**：如果启动类装载器和扩展类装载器加载的是 JVM 组件，那么系统类装载器就加载应用程序类。它加载 $CLASSPATH 上用户指定的类。
- **用户定义的类装载器（User-defined class loader）**：这个是由应用用户直接使用代码创建的类装载器。

像 Web 应用程序服务器（WAS - Web application server）这样的框架让 Web 应用和企业应用得以独立运行。换句话说，它可以保证程序可以通过类装载器的代理模式独立运行。WAS 类装载器同样使用层次结构，但不同 WAS 供应商的实现又有些许不同。

如果类装载器发现一个类未加载，这个类会通过以下流程进行加载和链接。

![image](http://note.youdao.com/yws/api/personal/file/WEBbed57cebdb0472bcfee26a36a1bbbd45?method=download&shareKey=ab39edf3a3d9a2a2a035a6ebee9fa5d5) 

每个阶段具体工作的描述如下：

- **加载（Loading）**：类是从文件获取的并加载到 JVM 内存中。
- **验证（Verifying）**：检查读取的类是否按照 Java 语言规范和 JVM 规范。这是类加载过程中最复杂的一个测试过程，消耗很长的时间也是最多的。大多数 JVM TCK 测试用例都是来测试在加载一个错误类时，验证过程是否会抛出错误。
- **准备（Preparing）**：准备类及其字段、方法和接口所需指定内存的存储结构。
- **解析（Resolving）**：将类常量池内所有标识符更改成直接引用。
- **初始化（Initializing）**：将类变量以合适的值进行初始化。执行静态初始器，初始化静态字段。

JVM 规范定义了任务。然而，它对执行时间的却要求比较灵活。

#### 运行时数据区（Runtime Data Areas）

![image](http://note.youdao.com/yws/api/personal/file/WEB35088ebfbdffef939bd7b365d8e6014f?method=download&shareKey=6e82d06d7c00f7240ca183e106ce570e) 

运行时数据区是 JVM 在 OS 上运行是分配的内存区域。运行时数据区可以分为 6 个部分。其中，程序计数寄存器（PC Register）、JVM 栈（JVM Stack）以及原生方法栈（Native Method Stack）是线程独有的。堆（Heap）、方法区（Method Area）、运行时常量池（Runtime Constant Pool）由所有线程共有。

- **程序计数寄存器（PC register）**：一个程序计数（Program Counter）寄存器存在于线程中，它在线程开始时创建。程序计数（Program Counter）寄存器有当前正在执行 JVM 指令的地址。
- **JVM 栈（JVM Stack）**：JVM 栈存在于线程中，也是在线程开始时创建。这个栈内的存储结构是栈帧（Stack Frame）为单位。JVM 只是对 JVM 栈进行入栈和出栈操作。如果有错误出现，栈跟踪的每行内容都会通过 printStackTrace() 将一个栈桢作为输出。

![image](http://note.youdao.com/yws/api/personal/file/WEBaf94bbffb7874c454213706e1a7de0c6?method=download&shareKey=cb1682dd3fd3006af3cc32ee0de7cff5) 

- 栈桢（Stack Frame）：栈桢是在方法执行期间创建的，并加入到线程的 JVM 栈中。当方法结束时，栈桢被移除。每个栈桢都有方法在类里执行时，对的本地变量列表（Local Variable Array）、操作数栈（Operand Stack）和运行时常量池（Runtime Constant Pool）的引用。本地变量列表（Local Variable Array）的大小以及操作数栈都是在编译时决定好的。因此，栈桢的大小根据方法的不同是固定的。
  - 本地变量列表（Local Variable Array）：它的索引从 0 开始。0 位置是方法所属实例的引用。从 1 开始，保存这方法的参数。在方法参数之后，保存的是方法的本地变量。
  - 操作数栈（Operand Stack）：方法真实的工作区。每个方法都在操作数栈与本地变量列表之间进行数据交换，对其他方法调用的结果进行入栈和出栈操作。操作数栈空间的必要大小在编译时决定。因此，操作数栈的大小也可以在编译时决定。
- **原地方法栈（Native method stack）**：一个供 Java 以外的本地语言代码使用的栈。换句话说，这个栈通过 Java 本地接口（JNI - Java Native Interface）执行 C/C++ 代码。会根据语言不同，分别创建 C 语言栈和 C++ 语言栈。
- **方法区（Method area）**：方法区是由所有线程共享的，它在 JVM 启动时创建。它存储了运行时常量池（runtime constant pool），字段（field）和方法（method）信息，静态变量（static variable），每个类与接口的方法字节，供 JVM 读取。方法区可以有多种不同的实现方式。Oracle Hotspot JVM 将其叫做永久区或永久代（PermGen）。方法区的垃圾收集对于 JVM 的提供商来说是可选实现。
- **运行时常量池（Runtime constant pool）**：与类文件格式中常量池表（constant_pool table）对应的区域。因此，JVM 规范特别强调了它的重要性。除了包括每个类与每个接口的常量，它还包括所有方法和字段的引用。简而言之，当方法和字段被引用时，JVM 会通过使用运行时常量池（runtime constant pool）搜索方法或字段在内存中的真实地址。
- **堆（Heap）**：存储实例或对象的空间，垃圾收集的目标区域。在我们讨论 JVM 性能问题时，会经常提及这个区域。JVM 提供商可以自行决定堆的配置方式，是否需要进行垃圾回收。

回头看看之前讨论过的反编译后的字节码 

```java
public void add(java.lang.String);
  Code:
   0:   aload_0
   1:   getfield        #15; //Field admin:Lcom/nhn/user/UserAdmin;
   4:   aload_1
   5:   invokevirtual   #23; //Method com/nhn/user/UserAdmin.addUser:(Ljava/lang/String;)Lcom/nhn/user/User;
   8:   pop
   9:   return
```

比较反编译之后的代码与 x86 架构下的汇编语言的操作码，两者有相似的格式；但是，Java 字节码不同的是它没有寄存器名，内存寻址器或操作数的偏移量。如之前描述的那样，JVM 使用栈，因此它不使用寄存器，取而代之的是使用如 15 和 23 这样的索引数字代替内存地址。因为它是自行管理内存的。15 和 23 是当前类常量池的索引（这里，`UserService.class`）。简言之，JVM 为每个类创建常量池，常量池中保存了引用的真实目标。

`UserService.class` 代码

```java
// UserService.java
…
public void add(String userName) {
    admin.addUser(userName);
}
```

逐行解释反编译代码如下：

- **aload_0**：将索引为 #0 的本地变量列表加到操作数栈下。#0 索引的本地变量列表永远是 this ，当前类实例的引用。
- **getfield #15**： 在当前类的常量池下，将 #15 索引加入到操作数栈中。UserAdmin admin 字段被增加。因为 admin 字段是类的实例，因此加入的是它的引用。
- **aload_1**：将 #1 本地变量列表的索引加入到操作数栈中。本地变量列表的 #1 索引是方法的参数。因此，当调用 add() 时，字符串 userName 的引用被加入到栈中。
- **invokevirtual #23**：调用当前类常量池中对应 #23 索引的方法。此时，引用是通过使用 getfield 增加的，参数是通过使用 aload_1 将其送入被调用的方法的。当调用结束时，返回值被加入到操作数栈中。
- **pop**：使用 invokevirtual 对调用方法的返回值进行出栈操作。可以看到通过之前库编译的代码没有返回值。正因为如此，也无须对做出栈操作获取返回值。
- **return**：完成方法。

下图将有助于理解上述过程。

![image](http://note.youdao.com/yws/api/personal/file/WEB94dcec8f4c9723bd64ee7be1c5d5fd93?method=download&shareKey=6aa986212ebdbd7b845a9e918097bad3) 

在本例中，本地变量列表并没有发生改变。所以上图只显示了操作数栈发生的改变。但是，在大多数情况下，本地变量列表也同样会发生变化。本地变量列表与操作数栈之间的数据传输通过很多装载命令（aload，iload）以及存储指令（astore，istore）来完成。

在本场景中，我们对运行时常量池和 JVM 栈有了一个简单的描述。当 JVM 运行时，每个类实例都会存储在堆上，而包括 User、UserAdmin、UserService 和 String 的类信息会存储在方法区。

#### 执行引擎（Execution Engine）

通过类装载器加载到 JVM 运行时数据区的字节码是通过执行引擎来运行的。执行引擎以单元指令的形式读取 Java 字节码（Java Bytecode）。这就和 CPU 逐行执行机器命令一样。每条字节码命令都包括 1 字节的操作码以及操作数。执行引擎获取一个操作码，再通过操作数执行任务，然后执行下一条操作码。

Java 字节码是人可以理解的语言，这和机器直接执行的语言还不一样。因此，执行引擎必须在 JVM 中将字节码语言转换成机器可执行的语言。字节码可以通过以下的两种方式将自身转换成合适的语言。

- **解释器（Interpreter）**：逐行读取、解释以及执行字节码。正因为它逐行解释和执行命令，它可以快速的解释字节码，但是执行解释的结果就会比较慢。这是解释型语言的缺点。字节码“语言”基本上是以解释器的方式运行的。
- **JIT（Just-In-Time）编译器**：JIT 编译器的引入是为了弥补解释器的缺陷。执行引擎在合适的时间先执行解释器，JIT 编译器编译整个字节码并将其转换成本地代码（native code）。在此之后，执行引擎不再解释改方法，而是直接执行本地代码。以本地代码的方式来执行要比逐行解释指令要快得多。因为本地代码是存于缓存中的，所以编译的代码得以快速执行。

不过，JIT 编译器编译代码的时间要比解释器逐行解释代码的时间长。因此，如果代码只是执行一次，那么解释是优于编译的。正因为这样，JVM 用 JIT 编译器从内部检查方法执行的频次，只对那些超过一定执行频次的方法才进行编译。

![image](http://note.youdao.com/yws/api/personal/file/WEB11f83f5d923964706e54a6ea1a04fb18?method=download&shareKey=e6bf84ee1334fad36773be6012dc981a) 

JVM 规范并没有规定执行引擎具体的运行方式。因此，JVM 厂商会用各种不同的技术来提升执行引擎的性能，同时也引入了各种不同类型的 JIT 编译器。

大多数编译器按下图方式运行：

![image](http://note.youdao.com/yws/api/personal/file/WEBe30dc21d273d48b50bf3c94e973331af?method=download&shareKey=42022a0b7d690f31960b6bce0df2562e) 

JIT 编译器将字节码转换成中间层表达式，中间表现形式（IR - Intermediate Representation），执行优化，然后将表达式转换成本地代码（native code）。

Oracle Hotspot VM 使用 JIT 编译器被称为 Hotspot 编译器。之所以被称为 Hotspot 是因为 Hotspot 编译器通过性能分析搜索处于最高优先级的待编译的 “Hotspot”，然后将 Hotspot 编译成为本地代码（native code）。如果被编译字节码方法不再被频繁调用，换句话说也就是如果方法不再是 Hotspot ，Hotspot VM 会将本地代码从缓存中移除并以解释模式运行。Hotspot VM 被分为服务端 VM（Server VM）和客户端 VM（Client VM），这两个 VM 使用不同的 JIT 编译器。

![image](http://note.youdao.com/yws/api/personal/file/WEB031e401f881ed7c949accf333858b833?method=download&shareKey=c3e857e6db5b5213114b77542569533d) 

客户端 VM 和服务器 VM 使用着相同的运行时；但是，它们使用着不同的 JIT 编译器，如上图所示。高级动态优化编译器（Advanced Dynamic Optimizing Compiler）是在服务端的 VM 中使用的，它应用了更为复杂和更为多样的性能优化技术。

IBM JVM 从 IBM JDK 6 开始引入了 AOT（Ahead-Of-Time）编译器以及 JIT 编译器。这意味着很多 JVM 会通过共享缓存共享已编译好的本地代码（native code）。简而言之，已经被 AOT 编译器编译过的代码可以直接在另外一个 JVM 中使用不需要重新编译。除此之外，IBM JVM 还用 AOT 编译器将预编译代码编译成 JXE（Java EXecutable）文件格式以提供更快的执行方式。

大多数 Java 性能的提升是通过提升执行引擎的能力获得的。与 JIT 编译器一样，由于引入了各式各样的性能优化技术，JVM 的性能也得以长足的提升。初期 JVM 与最近的 JVM 之间的最大差异之处就在于执行引擎。

Hotspot 编译器是自 Oracle Hotspot VM 的 1.3 版本引入的，Dalvik VM 引入 JIT 编译器是从 Android 2.2 开始的。

> 注意
>
> 引入如字节码这样中间语言的技术后，VM 执行字节码，JIT 编译器提高 JVM 性能的这种方式也通常应用于其他的语言。例如 Microsoft 的 .Net ，CLR（Common Language Runtime）也是一种虚拟机，执行某种称为 CIL（Common Intermediate Language）的字节码。CLR 同时提供 AOT 编译器和 JIT 编译器。因此，如果源代码是用 C# 或 VB.NET 编译的，编译器创建 CIL 并通过 JIT 编译器在 CLR 执行创建的 CIL 。CLR 也会使用垃圾回收策略，同时与 JVM 一样，它也是以堆栈结构机器的模式运行。



# 深入理解JVM内部结构

这篇文章主要是解释java虚拟机（JVM）的内部结构。下图显示了符合Java SE 7 版本的Java虚拟机规范的一个典型JVM中的关键内部组件。 

![](C:\Users\Louis\Pictures\jvm内部结构.jpg)

图中显示的组件将会在下面两部分中进行逐一的解释。第一部分涉及JVM为每一个线程都会创建的组件；第二部分则是独立于线程进行创建的组件。 

\1. Thread

​     Thread是一个程序中的一个执行线程。JVM允许一个应用程序有多个执行线程并发运行。在Sun的Hotspot JVM中，Java线程与本地操作系统线程间存在一个直接的一一映射。JVM先为Java线程准备好所有的状态，如线程局部存储、分配缓冲区、同步对象、栈和程序计数器，之后对应的本地线程被创建。Java线程终止以后其本地线程将会被回收利用，由操作系统负责调度所有的线程并将它们分派到可用的CPU。一旦本地线程已经初始化完成，则会调用Java线程的run()方法，当run()方法返回时，未捕获的异常将被处理，之后本地线程判断JVM是否需要终止运行（如此线程是最后一个非守护线程）。当线程终止以后，本地线程及Java线程占用的所有资源都会被释放。

1.1 JVM系统线程

​    如果你曾使用jconsole或其它的一些调试工具，也许你会注意到JVM中有许多的线程运行在后台。这些运行的后台线程，除了main线程以外，都是作为调用public static void main(String [])的一部分被创建，此外，还有一些是被main线程所创建。以Sun的Hotspot JVM为例，主要的后台系统线程有：

(1) VM Thread 

​     此线程用于等待请求JVM到达一个“安全点”的操作的出现。之所以所有这类操作需要在一个单独的线程——VM Thread中进行，是因为它们都要求JVM处于“安全点”，当JVM位于“安全点”时，不能对heap执行修改操作。VM Thread执行的操作类型是会“stop-the-world”的垃圾回收、线程堆栈转储、线程挂起和偏向锁撤销。

注：stop-the-world，也即当执行此操作时，Java虚拟机需要停止运行。

(2) Periodic task thread（周期任务线程）

​     此线程负责用于调度周期性操作的执行的定时事件。

(3) GC threads（垃圾回收线程）

​      这些线程用于支持JVM中进行的不同类型的垃圾回收活动。

(4) Compiler threads

​      这些线程在运行时将字节码编译成本地代码。

(5) Signal dispatcher thread（信号分发线程）

​      此线程负责接收发送给JVM进程的信号，处理信号，根据系统设置调用合适的JVM方法。

1.2 Per Thread

​     每一个执行的线程都具有下列组件：

(1) 程序计数器（program Counter，PC）

​    除非是本地代码，否则PC指向当前指令（也即opcode）的地址。如果当前方法是本地的，则PC是未定义的。所有的CPU都有一个PC，一般情况下，PC会在每一个指令之后进行递增，以保存下一个执行指令的地址。JVM使用PC来记录当前指令执行的位置，PC实际上指向了方法区的一个内存地址。

(2) 栈（Stack）

   每个线程都有自己的栈，栈为线程中执行的每个方法保存了一个对应的帧（Frame）。栈是后进先出（LIFO）的数据结构，因此当前执行的方法总是位于栈的顶端。当调用一个方法时，为其创建一个新的帧并压栈，当方法正常返回或者是由于执行期间抛出了未捕获的异常而退出时，其对应的帧将被删除（也即出栈）。除了执行帧的压栈与出栈，无法对栈进行其它的直接操作，因此帧对象也许是在堆（Heap）中进行内存分配，它们的内存也就无需是连续的。

(3) 本地栈（Native Stack）

​    并非所有的JVM都支持本地方法，然而，那些支持的JVM一般会为每个线程创建本地方法栈。如果JVM是使用“C链接”模型实现Java本地调用（JNI）的，则本地栈将是一个C栈。在这种情况下，本地栈中参数及返回值的顺序将与典型的C程序是一致的。一个本地方法一般会调回JVM，并调用Java方法，此类从本地方法到Java方法调用发生于正常的Java栈中，线程会离开本地栈，并在Java栈中为方法创建一个新的帧。

(4)栈的限制（Stack Restrictions）

栈可以是动态的，也可以是固定大小的。如果一个线程要求超过允许的大小的栈，则JVM会抛出StackOverflowError。如果线程要求创建一个新的帧，但是系统没有足够内存进行分配，则JVM会抛出OutOfMemoryError。

(5) 帧（Frame）

原文内容与栈部分大致一致，不冗述。

每个帧中包含有：1. 局部变量数组；2. 返回值；3. 操作对象栈；4. 到方法所属类的运行时常量池的引用。

(6) 局部变量数组（Loca Variables Array）

   局部变量数组中包含了方法运行期间使用到的所有变量，包括到this变量的引用、所有的方法参数及其它方法中定义的局部变量。对于类方法（静态方法），方法参数从“零”开始，而对于实例方法，“零”位置预留给this变量。

一个局部变量可以是：

- boolean
- byte
- char
- long
- short
- int
- float
- double
- reference
- returnAddress

除了long和double，所有的类型都在局部变量数组中占据一个位置，long及double则占用两个连续的位置（slot），由于它们是双倍的宽度，也即64位大小。

(7) 操作对象栈

​    操作对象栈在字节码指令执行期间使用，作用类似于CPU使用的一般用途的寄存器。大多数的JVM操纵操作对象栈的方式有：压栈、出栈、复制、交换、通过执行某一操作产生或消耗成数值。因此，在字节码中，在局部变量数组与操作对象栈之间移动数值的指令出现的次数很多。例如，一个简单的变量就需要两条与操作对象栈进行交互的字节码。

```java
  int i;
```

  编译以后的字节码如下： 

```java
   0: iconst_0	// Push 0 to top of the operand stack
   1: istore_1	// Pop value from top of operand stack and store as local variable 1
```

(8) 动态链接

​       每个帧包含了一个到运行时常量池的引用。此引用指向当前执行的方法对应的类的常量池，用于辅助支持动态链接功能。

​     当一个类被编译以后，所有到变量及方法的引用都被存储到类的常量池中作为一个符号引用。一个符号引用是一个逻辑引用而不是直接指向物理内存地址的引用。JVM的实现可以自由选择何时解析符号引用，可能的选择有：类文件被验证时亦或是被加载以后，这类方案被称为是急切的（eager）或静态的（static）解析；还有一种被称为懒加载或延时加载方案，它是在符号引用第一次被使用时进行解析。然而，JVM必须要表现得像是当引用第一次被使用时才进行解析，并且此时需要抛出任何的解析错误。

​    绑定是由符号引用标识的域、方法和类被直接引用替换的过程，此过程只发生一次。如果指向类的符号引用还没有被解析则此类会被加载。每一个直接引用都被存储为一个相对偏移值，以与变量或方法的运行时位置关联的存储结构作为基准。

1.3 Shared Between Threads

(1) 堆（Heap）

  堆用于运行时分配类实例及数组。数组及对象无法存储在栈中，因为帧在创建以后，其大小就无法再改变。帧中只能存储指向堆中对象或数组的引用。与简单变量与在局部变量中的引用不同，数组对象总是存储在堆中，因此当方法退出时，它们没有被移除，对象只能被垃圾收集器移除。

​     为了支持垃圾回收，堆被分为三个部分：

- Young Generation（年轻代），通常被分为Eden和Survivor
- Old Generation（老年代，也叫Tenured Generation）
- Permanent Generation

(2) 内存管理

​      对象及数组不会被显示释放，垃圾回收器会自动回收它们。

此过程如下：

1. 新的对象和数组被创建并放到年轻代中；
2. 一次小的垃圾收集会在年轻代上执行，如果对象存活下来，则它们将被从eden空间移动到survivor空间；
3. 主垃圾收集（Major）执行，它会引起应用线程停止运行，也即stop-the-world，同时会在不同的“代”中移动对象。经过此次垃圾回收后，如果对象仍然存活，则它们会被从年轻代移动到老年代。
4. 在每次老年代被回收时永久代也被回收，当任何一个空间满时，它们都会被回收。

(3) 非堆内存

   从逻辑上考虑，被认为是JVM结构的一部分的对象将不会在堆中进行创建。非堆内存包括有：1.包含方法区及interned字符串的永久代；2. 代码缓存，用于编辑与存储已经被JIT编译器编译为本地代码的方法。

(4) 即时编译

Java字节码采用解释执行方式，因此它没法像直接在JVM所在的主机上执行本地代码那么快。为了提高性能，Oracle Hotspot VM寻找字节码中被周期地执行的“热区域”，并将它们编译为本地代码。本地代码被存储到非堆内存的代码缓存中，通过这样的方式，Hotspot VM尝试选择最合适的方式来权衡编译代码花费的额外时间与执行解释代码花费的额外时间。

(5) 方法区（Method Area）

方法区中存储了每个类的元信息，如：

- 类加载器引用
- 运行时常量池
  - 数值常量
  - 字段引用
  - 方法引用
  - 属性值
- 字段数据
  - 每个字段
    - 名称
    - 类型
    - Modifiers
    - 属性值
- 方法数据
  - 每个方法
    - 名称
    - 返回类型
    - 参数类型
    - Modifiers
    - 属性值
- 方法代码
  - 每个方法
    - 字节码
    - 操作对象栈大小
    - 局部变量大小
    - 局部变量表
    - 异常表
      - 每个异常处理器
        - 起始点
        - 结束点
        - 处理器代码的PC偏移值
        - 捕抓的异常类的常量池索引

   所有的线程共享相同的方法区，因此方法区数据的存取及动态链接的过程必须是线程安全的。如果两个线程都企图存取同一个类中的字段或方法，但是，此类还没有被加载，则它必须只被加载一次，并且线程必须等到类被加载完成以后才能继续执行。

(6) 类文件结构

​       一个编译完成的类文件包含以下的结构：

```java
ClassFile {
    u4			magic;
    u2			minor_version;
    u2			major_version;
    u2			constant_pool_count;
    cp_info		contant_pool[constant_pool_count – 1];
    u2			access_flags;
    u2			this_class;
    u2			super_class;
    u2			interfaces_count;
    u2			interfaces[interfaces_count];
    u2			fields_count;
    field_info		fields[fields_count];
    u2			methods_count;
    method_info		methods[methods_count];
    u2			attributes_count;
    attribute_info	attributes[attributes_count];
}
```

如果你想看一个编译完成的类文件的字节码，可以使用命令行工具javap，参考：[java那点事——StringBuffer与StringBuilder原理与区别](http://blog.csdn.net/shi1122/article/details/8053680)

 