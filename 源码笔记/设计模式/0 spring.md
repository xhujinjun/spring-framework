https://www.ibm.com/developerworks/cn/java/j-lo-spring-principle/
#Spring 框架的设计理念与设计模式分析


# Spring的骨骼架构
Spring总共有十几个组件,但是真正核心的组件只有几个,下面是Spring框架的总体架构图:
!(Spring架构图)[image/spring-overview.png]
从上图中可以看出 Spring 框架中的核心组件只有三个：Core、Context 和 Beans。它们构建起了整个 Spring 的骨骼架构。
没有它们就不可能有 AOP、Web 等上层的特性功能。下面也将主要从这三个组件入手分析 Spring。

# Spring的设计理念
  前面介绍了 Spring 的三个核心组件，如果再在它们三个中选出核心的话，那就非 Beans 组件莫属了，为何这样说，
其实 Spring 就是面向 Bean 的编程（BOP,Bean Oriented Programming），Bean 在 Spring 中才是真正的主角。

  Bean 在 Spring 中作用就像 Object 对 OOP 的意义一样，没有对象的概念就像没有面向对象编程，Spring 中没有 Bean 也就没有 Spring 存在的意义。
  就像一次演出舞台都准备好了但是却没有演员一样。为什么要 Bean 这种角色 Bean 或者为何在 Spring 如此重要，这由 Spring 框架的设计目标决定，
  Spring 为何如此流行，我们用 Spring 的原因是什么，想想你会发现原来 Spring 解决了一个非常关键的问题他可以让你把对象之间的依赖关系转而用配置文件来管理，
  也就是他的依赖注入机制。而这个注入关系在一个叫 Ioc 容器中管理，那 Ioc 容器中有又是什么就是被 Bean 包裹的对象。
  Spring 正是通过把对象包装在 Bean 中而达到对这些对象管理以及一些列额外操作的目的。

  它这种设计策略完全类似于 Java 实现 OOP 的设计理念，当然了 Java 本身的设计要比 Spring 复杂太多太多，但是都是构建一个数据结构，然后根据这个数据结构设计他的生存环境，
  并让它在这个环境中按照一定的规律在不停的运动，在它们的不停运动中设计一系列与环境或者与其他个体完成信息交换。
  这样想来回过头想想我们用到的其他框架都是大慨类似的设计理念。

  # 核心组件如何协同工作
  前面说 Bean 是 Spring 中关键因素，那 Context 和 Core 又有何作用呢？前面吧 Bean 比作一场演出中的演员的话，那 Context 就是这场演出的舞台背景，而 Core 应该就是演出的道具了。只有他们在一起才能具备能演出一场好戏的最基本的条件。当然有最基本的条件还不能使这场演出脱颖而出，还要他表演的节目足够的精彩，这些节目就是 Spring 能提供的特色功能了。
我们知道 Bean 包装的是 Object，而 Object 必然有数据，如何给这些数据提供生存环境就是 Context 要解决的问题，对 Context 来说他就是要发现每个 Bean 之间的关系，为它们建立这种关系并且要维护好这种关系。所以 Context 就是一个 Bean 关系的集合，这个关系集合又叫 Ioc 容器，一旦建立起这个 Ioc 容器后 Spring 就可以为你工作了。那 Core 组件又有什么用武之地呢？其实 Core 就是发现、建立和维护每个 Bean 之间的关系所需要的一些列的工具，从这个角度看来，Core 这个组件叫 Util 更能让你理解。

它们之间的关系可以用下图来表示:
!(三个组件关系)[三个组件关系.gif]
# 核心组件详解
这里将详细介绍每个组件内部类的层次关系，以及它们在运行时的时序顺序。我们在使用 Spring 是应该注意的地方。
## Bean组件
Bean组件是Spring最核心的一个组件.下面看看Bean这个组件是怎么设计的。Bean 组件在 Spring 的 org.springframework.beans 包下。
这个包下的所有类主要解决了三件事:Bean的定义、Bean的创建已经对Bean的解析。对 Spring 的使用者来说唯一需要关心的就是 Bean 的创建，其他两个由 Spring 在内部帮你完成了，对你来说是透明的。

### Bean的定义
Bean的定义主要有BeanDefinition描述,如下图说明了这些类的层次关系:
!(Bean的定义)[Bean的定义.png]
Bean的定义就是完整的描述了在Spring的配置文件中你定义的<bean/>节点中所有的信息,包括各种子节点。
当 Spring 成功解析你定义的一个 <bean/> 节点后，在 Spring 的内部他就被转化成 BeanDefinition 对象。以后所有的操作都是对这个对象完成的。

### Bean的解析
Bean 的解析过程非常复杂，功能被分的很细，因为这里需要被扩展的地方很多，必须保证有足够的灵活性，以应对可能的变化。
Bean 的解析主要就是对 Spring 配置文件的解析。这个解析过程主要通过下图中的类完成：
!(Bean的解析)[Bean的解析.png]
当然还有具体对 tag 的解析这里并没有列出。
### Bean的创建
Spring Bean 的创建时典型的工厂模式，他的顶级接口是 BeanFactory，下图是这个工厂的继承层次关系：
!(Bean的创建)[Bean的创建.png]
BeanFactory 有三个子类：ListableBeanFactory、HierarchicalBeanFactory 和 AutowireCapableBeanFactory。但是从上图中我们可以发现最终的默认实现类是 DefaultListableBeanFactory，他实现了所有的接口。那为何要定义这么多层次的接口呢？查阅这些接口的源码和说明发现，每个接口都有他使用的场合，它主要是为了区分在 Spring 内部在操作过程中对象的传递和转化过程中，对对象的数据访问所做的限制。例如 ListableBeanFactory 接口表示这些 Bean 是可列表的，而 HierarchicalBeanFactory 表示的是这些 Bean 是有继承关系的，也就是每个 Bean 有可能有父 Bean。AutowireCapableBeanFactory 接口定义 Bean 的自动装配规则。这四个接口共同定义了 Bean 的集合、Bean 之间的关系、以及 Bean 行为。

## Context组件
Context 在 Spring 的 org.springframework.context 包下，前面已经讲解了 Context 组件在 Spring 中的作用，他实际上就是给 Spring 提供一个运行时的环境，
用以保存各个对象的状态。下面看一下这个环境是如何构建的。
ApplicationContext是Context的顶级父类,他除了能标识一个应用环境的基本信息外,还继承了五个接口，这五个接口主要是扩展了 Context 的功能。下面是 Context 的类结构图：
!(context)[context.png]
从上图中可以看出 ApplicationContext 继承了 BeanFactory，这也说明了 Spring 容器中运行的主体对象是 Bean，另外 ApplicationContext 继承了 ResourceLoader 接口，使得 ApplicationContext 可以访问到任何外部资源，这将在 Core 中详细说明。
ApplicationContext 的子类主要包含两个方面：
ConfigurableApplicationContext 表示该 Context 是可修改的，也就是在构建 Context 中用户可以动态添加或修改已有的配置信息，它下面又有多个子类，其中最经常使用的是可更新的 Context，即 AbstractRefreshableApplicationContext 类。
WebApplicationContext 顾名思义，就是为 web 准备的 Context 他可以直接访问到 ServletContext，通常情况下，这个接口使用的少。
再往下分就是按照构建 Context 的文件类型，接着就是访问 Context 的方式。这样一级一级构成了完整的 Context 等级层次。
总体来说 ApplicationContext 必须要完成以下几件事：
标识一个应用环境
利用 BeanFactory 创建 Bean 对象
保存对象关系表
能够捕获各种事件
Context 作为 Spring 的 Ioc 容器，基本上整合了 Spring 的大部分功能，或者说是大部分功能的基础。

## Core组件
Core 组件作为 Spring 的核心组件，他其中包含了很多的关键类，其中一个重要组成部分就是定义了资源的访问方式。这种把所有资源都抽象成一个接口的方式很值得在以后的设计中拿来学习。下面就重要看一下这个部分在 Spring 的作用。

下图是 Resource 相关的类结构图：
!(core)3[core.png]
从上图可以看出 Resource 接口封装了各种可能的资源类型，也就是对使用者来说屏蔽了文件类型的不同。对资源的提供者来说，如何把资源包装起来交给其他人用这也是一个问题，我们看到 Resource 接口继承了 InputStreamSource 接口，这个接口中有个 getInputStream 方法，返回的是 InputStream 类。这样所有的资源都被可以通过 InputStream 这个类来获取，所以也屏蔽了资源的提供者。另外还有一个问题就是加载资源的问题，也就是资源的加载者要统一，从上图中可以看出这个任务是由 ResourceLoader 接口完成，他屏蔽了所有的资源加载者的差异，只需要实现这个接口就可以加载所有的资源，他的默认实现是 DefaultResourceLoader。

下面看一下 Context 和 Resource 是如何建立关系的？首先看一下他们的类关系图：
!(context和Resource)[context和Resource.png]
从上图可以看出，Context 是把资源的加载、解析和描述工作委托给了 ResourcePatternResolver 类来完成，他相当于一个接头人，他把资源的加载、解析和资源的定义整合在一起便于其他组件使用。Core 组件中还有很多类似的方式。


# IOC容器如何工作
