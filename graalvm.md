# 一、GraalVM是什么

GraalVM是Oracle开源的、基于HotSpot增强的、高性能的可嵌入式多语言虚拟机，官方称之为Universal GraalVM。它打通了不同语言之间的鸿沟，让我们可以进行混合式多语言编程。 在GraalVM之上，我们可以编写Java、Python、Ruby、R、Scala、Kotlin，甚至是C、C++语言。

在即时编译器方面，GraalVM把HotSpot内置的C2替换成了Graal。



# 二、GraalVM的特点

1. 高性能
2. AOT(Ahead-of-Time)编译
3. 多语言编程



## 1. 高性能



## 2. AOT(Ahead-of-Time)编译

GraalVM通过SubstrateVM框架来支持AOT(Ahead-of-Time)编译。 AOT其实就是提前编译，是与即时编译(JIT)相对立的概念， AOT是在程序运行之前，将字节码转换为机器码，然后直接编译成可独立运行的执行文件(本地镜像)或共享库。

SubstrateVM 的设计初衷是提供一个高启动性能、低内存开销，并且能够无缝衔接 C 代码的 Java 运行时。SubtrateVM完全脱离了HotSpot虚拟机，并拥有独立的运行时，包含异常处理，同步，线程管理，内存管理（垃圾回收）和 JNI 等组件。

SubstrateVM可划分为两部分：本地镜像生成器(native image generator)和SubstrateVM运行时。



### 2.1 本地镜像生成器

本地镜像生成器主要实现AOT编译逻辑，通过Graal编译器把Java类文件编译为可执行文件或者动态链接库。

在进行编译之前，本地镜像生成器采用指针分析(points-to analysis)，从用户提供的程序入口出发，探索所有可达的代码。在探索的同时，它还将执行初始化代码，并在最终生成可执行文件时，将已初始化的堆保存至一个堆快照之中。这样一来，SubstrateVM 将直接从目标程序开始运行，而无须重复进行 Java 虚拟机的初始化。



### 2.2 SubstrateVM的启动时间与内存开销

SubstrateVM的启动时间和内存开销非常少，主要得益于SubstrateVM在编译阶段所保存的堆快照，已经无需额外初始化和直接执行目标代码的特性。

启动时间提升50倍，内存消耗减少5倍。

![faster](/Users/apple/Documents/graalvm/faster.jpg)



![lower](/Users/apple/Documents/graalvm/lower.png)

### 2.3 SubstrateVM的劣势

1. Java 语言的反射机制，使得在编译期生成可执行文件很困难。因为通过反射机制可以在运行期间动态调用API接口，这些在编译期是无法感知的。除非放弃反射机制，或者在编译时提供配置文件供反射调用。

2. ASM、CGLIB、Javassist字节码库会在运行时生成、修改字节码，这些也没法通过 AOT 编译成原生代码。比如 Spring 的依赖注入就使用了 CGLIB 增强。Spring 已经在新版本中适配了 GraalVM，可以关闭 CGLIB。

3. 启动时间、内存使用确实有大幅度优化，但是对于长时间运行的大型应用，未必有 HotSpot 的 Java 应用程序速度快。

   

详细信息参考: [SubstrateVM的限制](https://github.com/oracle/graal/blob/master/substratevm/LIMITATIONS.md)



## 3. 支持多语言编程

### 3.1 Truffle是什么

Truffle是GraalVM中的语言实现框架，为GraalVM提供运行多语言的的能力。

![truffle](/Users/apple/Documents/graalvm/truffle.png)

### 3.2 Partial Evaluation

假设有一段程序P，它将一系列输入I转换成输出O（即P: I -> O）。而这些输入又可以进一步划分为编译时已知的常量IS，和编译时未知的ID。

那么，我们可以将程序P: I -> O转换为等价的另一段程序P’: ID -> O。这个新程序P’便是P的特化（Specialization），而从P转换到P’的这个过程便是所谓的 Partial Evaluation。

回到 Truffle 这边，我们可以将 Truffle 语言的解释执行器当成P，将某段用 Truffle 语言写就的程序当作IS，并通过 Partial Evaluation 特化为P’。由于 Truffle 语言的解释执行器是用 Java 写的，因此我们可以利用 Graal 编译器将P’编译为二进制码。

### 3.3 节点重写(Node rewriting)

在动态语言中，许多变量的类型是在运行过程中方能确定的。以加法符号+为例，它既可以表示整数加法，还可以表示浮点数加法，甚至可以表示字符串加法。

如果是静态语言，我们可以通过推断加法的两个操作数的具体类型，来确定该加法的类型。但对于动态语言来说，我们需要在运行时动态确定操作数的具体类型，并据此选择对应的加法操作。这种在运行时选择语义的节点，会十分不利于即时编译，从而严重影响到程序的性能。

Truffle 语言解释器会收集每个 AST 节点所代表的操作的类型，并且在即时编译时，作出针对所收集得到的类型 profile 的特化（specialization）。

还是以加法操作为例，如果所收集的类型 profile 显示这是一个整数加法操作，那么在即时编译时我们会将对应的 AST 节点当成整数加法；如果是一个字符串加法操作，那么我们会将对应的 AST 节点当成字符串加法。

当然，如果该加法操作既有可能是整数加法也可能是字符串加法，那么我们只好在运行过程中判断具体的操作类型，并选择相应的加法操作。

![profile](/Users/apple/Documents/graalvm/profile.png)

这种基于类型 profile 的优化，与我们以前介绍过的 Java 虚拟机中解释执行器以及三层 C1 编译代码十分类似，它们背后的核心都是基于假设的投机性优化，以及在假设失败时的去优化。

在即时编译过后，如果运行过程中发现 AST 节点的实际类型和所假设的类型不同，Truffle 会主动调用 Graal 编译器提供的去优化 API，返回至解释执行 AST 节点的状态，并且重新收集 AST 节点的类型信息。之后，Truffle 会再次利用 Graal 编译器进行新一轮的即时编译。

### 3.4 夸语言调用(Polyglot)

Truffle 语言实现框架支持 Polyglot，允许在同一段代码中混用不同的编程语言，从而使得开发人员能够自由地选择合适的语言来实现子组件。

与其他 Polyglot 框架不同的是，Truffle 语言之间能够共用对象。也就是说，在不对某个语言中的对象进行复制或者序列化反序列化的情况下，Truffle 可以无缝地将该对象传递给另一门语言。因此，Truffle 的 Polyglot 在切换语言时，性能开销非常小，甚至经常能够达到零开销。

Truffle 的 Polyglot 特性是通过 Polyglot API 来实现的。每个实现了 Polyglot API 的 Truffle 语言，其对象都能够被其他 Truffle 语言通过 Polyglot API 解析。实际上，当通过 Polyglot API 解析外来对象时，我们并不需要了解对方语言，便能够识别其数据结构，访问其中的数据，并进行进一步的计算。



