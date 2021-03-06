# JVM

## Java内存区域

### 运行时内存区域

#### 堆

存放对象实例、是GC管理的主要区域。

#### 方法区

存放类变量，常量，类元信息，运行时常量池

#### JAVA虚拟机栈

线程私有，是对方法的一个抽象，一个方法对应一个栈帧，当前正在运行的方法，是栈顶部的栈帧。

##### 栈帧

存储了方法的

1. 局部变量表：存放方法参数和方法内部的局部变量（基本类型+引用类型+返回地址类型）
2. 操作数栈
3. 动态连接：在运行期间转化为直接引用。
4. 方法返回地址

##### 方法调用

1. 静态解析
2. 动态分派

#### 本地方法栈

与虚拟机栈类似，对应是为Native方法服务。

#### 程序计数器

### 对象

#### 对象创建过程

1. 虚拟机遇到一条`new`指令时，检查能否在常量池中找到这个类的符号引用
2. 检查这个符号引用代表的类是否已被加载
3. 类加载检查通过后，为对象分配内存
   1. 指针碰撞
   2. 空闲列表
4. 将内存空间初始化为0值，设置对象头。
5. 执行`<init>`方法

`<init>`方法执行顺序：

1. 静态变量
2. 静态代码块
3. 变量
4. 代码块
5. 构造函数

#### 对象内存布局

1. 对象头：哈希吗，GC年龄，锁信息
   1. Mark Word
   2. Klass Pointer
2. 实例数据
3. 对其填充

#### 对象的访问定位

1. 使用句柄访问，reference中存放的是句柄地址，句柄包含
   1. 对象实例数据的指针（因此对象地址的修改不会影响句柄地址）
   2. 对象类型数据的指针
2. 使用直接指针访问，reference中存放的是对象地址
   1. 对象地址

## 垃圾回收

### 判断对象是否存活

判断对象是否存活都与**引用**有关。

#### 引用计数法

**算法思路**：给对象添加一个引用计数器，被引用时，将其加1；当引用失效时，将其减1；

**问题**：循环引用会导致对象不会被回收。

##### 再谈引用

1. 强引用：程序中普遍存在的引用，比如`Object obj = new Object()`这种，GC不会回收强引用对象。
2. 软引用(`SoftReference`)：有用但并非必需的对象。在系统发生内存溢出之前会将其列入回收范围中的第二次回收。
3. 弱引用(`WeakReference`)：描述非必需的对象。GC时，会将其回收。
4. 虚引用(`PhantomReference`)：作用是在这个对象被GC时收到一个系统通知。

#### 可达性分析

算法思路：通过一系列被称为`GC Roots`的对象作为起始点，向下搜索节点，搜索走过的路径称为引用链，若一个对象到`GC Roots`没有一条引用链相连时，该对象是不可用的。

##### 可作为GC Roots的对象

1. 虚拟机栈（栈帧中的局部变量表）中引用的对象
2. 方法区中类静态属性引用的对象
3. 方法区中常量引用的对象
4. 本地方法栈中JNI（Native方法）引用的对象

### 垃圾回收算法

#### 标记-清除

算法思路

1. 标记出所有要回收的对象
2. 在标记完成后统一回收

缺点：

造成内存碎片

#### 复制

将内存区域分成大小相等的2块，每次只是用其中一块，当其中一块已满时，将存活的对象复制到另一半当中，然后把已使用的一块全部清理。解决了存在内存碎片的问题，但是会造成内存空间实际可用减半。

实际商业的JVM都采用这种算法，不过不是分成两块1：1的内存，而是分成三块8：1：1的内存，分别是Eden区和两块较小的Survivor区。当GC时，将Eden区和一块S区上存活的对象复制到另一块S区上。

**分配担保**机制：当一块S区没有足够的空间存活的对象时，这些对象会通过分配担保机制进入老年代。

#### 标记-整理

1. 标记出所有要回收的对象
2. 让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存。

#### 分代收集

将Java堆分成新生代和老年代，根据给年代的特点采用合适的回收算法

**新生代**：大量对象被回收，少量存活的情况可以用复制算法。   v'v

**老年代**：标记-清理；标记-整理

### 垃圾回收器

#### Serial

#### CMS（Concurrent Mark Sweep)

以获取**最短回收停顿时间**为目标的收集器。

**步骤**

1. 初始标记：**Stop the world**，标记GC Roots能直接关联的对象
2. 并发标记：GC Roots搜索的过程
3. 重新标记：**Stop the world**，修正并发标记阶段因用户程序继续运行而导致标记产生变动的对象的记录
4. 并发清除

**缺点**

1. 对CPU资源非常敏感
2. 无法处理浮动垃圾
3. 产生内存碎片

#### G1（Garbage-First）

**特点**

1. 并行与并发
2. 分代收集
3. 空间整合（不会产生内存碎片）整体：标记整理，局部：复制
4. 可预测的停顿

**可预测停顿**：有计划的避免全区域的垃圾收集，它会跟踪各个Region里面的垃圾堆积的价值大小。后台维护一个优先列表，每次根据允许的回收时间，优先回收价值最大的Region（Garbage-First）

步骤

1. 初始标记：标记一下GC Roots能直接关联到的对象
2. 并发标记：从GC Roots开始对堆中对象进行可达性分析，找出存活的对象
3. 最终标记：修正在并发标记时因用户程序运行而导致标记产生变动的对象的记录。用户线程停顿，GC线程可以并行执行
4. 筛选回收

#### TODO垃圾回收器的选择

### 内存分配策略

普遍的**内存分配规则**

1. 对象优先在Eden区分配。当Eden区域不够分配时，会进行Minor GC
2. **大对象**直接进入老年代。
   1. 需要大量连续内存空间的Java对象，很长的字符串和数组。
   2. 经常出现大对象容易导致内存还有不少空间时就提前触发GC以获取足够的**连续**空间来存放他们
3. 长期存活的对象进入老年代。
   1. 对象每经过一次MinorGC并存活，则GC年龄+1，增加到15（默认）会晋升到老年代
   2. **动态对象年龄判断**：当S空间中的所有相同年龄的对象大小总和大于S空间的一半，年龄大于等于该年龄的对象晋升到老年代中。

在发生Minor GC前，JVM会先检查**老年代最大可用连续空间**是否大于**新生代所有对象总空间**

1. 大于->Minor GC是安全的
2. 不大于->查看是否允许担保失败
   1. 允许，查看**老年代最大可用连续空间**是否大于**历次晋升到老年代对象的平均大小**
      1. 大于->尝试Minor GC
      2. 小于->Full GC
   2. 不允许->Full GC

## JVM类加载机制

### 类加载过程

1. 加载
   1. 通过一个类的全限定名来获取定义此类的二进制字节流
   2. 将这个字节流所代表的的静态存储结构转为方法区的运行时数据结构
   3. 在内存中（方法区）生成该类的Class对象
2. 验证：确保Class文件的字节流中包含的信息是否符合JVM要求
3. 准备：为类变量分配内存，并初始化0值（方法区）
4. 解析：将常量池中的符号引用转为直接引用（包括方法以及字段）
5. 初始化：执行类构造器`<clinit>`（父类的类构造器先于子类的类构造器执行）
   1. 类变量赋值
   2. 静态块

### 类加载器

功能：根据一个类的全限定名来获取定义此类的二进制字节流。

#### 分类

1. 启动类加载器（加载lib底下的类，无法被Java程序直接引用，是虚拟机自身的一部分）
2. 扩展类加载器（加载/lib/ext底下的类）
3. 应用程序类加载器（加载用户路径上的类）

#### 双亲委派机制

**工作过程：**当一个类加载器收到类加载请求，他首先不会自己加载，而是委派给父类加载器加载，直到加载请求传到启动类加载器，只有当父类加载器无法加载时，子类加载器才会尝试加载。

**好处：**

1. 使得类加载器有了带优先级的层次关系（越基础的类越由上层加载器加载）
2. 保证Java程序的稳定运作

破坏双亲委派机制

1. JNDI使用线程上下文加载器请求子类加载器完成加载任务。
2. OSGi环境下，类加载器不再是树状模型，而是网状模型



