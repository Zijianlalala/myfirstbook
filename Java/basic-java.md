# Java基础
Java基础相关的内容

## 关键字

### `final`关键字
可以应用的场景
1. 修饰变量
2. 修饰方法
3. 修饰类

#### 修饰变量
作用是使变量不可变，或者声明常量。

变量类型又分为两种：
1. 基本类型
2. 引用类型

当修饰引用类型时，表示该变量不能被重新赋值，但是引用对象的内部状态可以修改。

**注意**：全局常量可以声明且不赋初值，它可以在“静态初始化块”、“实例初始化块”以及“构造函数”中被赋初值；但是局部常量只能在声明时赋初值。


#### 修饰方法
作用：
1. 被`final`修饰的方法不能被重写/重载
#### 修饰类
> `String`是被`final`修饰的常量类

作用是防止被继承，不能拥有自己的子类。

### `override`关键字
#### 重写（override）
子类对父类的允许访问的方法实现过程进行重编写，**外壳不变，核心重写**

#### 重载（overload)
是在同一个类中，有多个方法，它们的方法名相同，但是参数不同，返回类型可以不同。常用的是构造方法的重载。

### `native`关键字
被该关键字修饰表示该方法调用非`java`代码。

### `instanceof`关键字
他不但匹配指定类型，还匹配指定类型的子类；`==`可以精确判断指定类型，但不能作为子类比较。通常情况下使用`instanceof`判断数据类型，因为面向抽象编程时，我们不关心具体的子类型。


## 反射
**反射**是指在程序运行期可以拿到一个对象的所有信息。它更多地是给工具或者底层框架来使用，目的是在不知道目标实例任何信息的情况下，获取特定字段的值。

### `Class`类
每加载一种`class`，JVM就为其创建一个`Class`类型的实例，并关联起来。一个`Class`实例包括了该`class`对应的所有完整信息（类名、包名、方法、字段等）。

通过`Class`实例获得该`class`信息的方法叫做反射。

获取`Class`实例的方法：
1. 通过`class`的静态变量`class`获取：`Class cls = String.class;`
2. 通过实例变量的`getClass()`方法获取。
3. 通过`class`的完整类名调用`Class.forName("java.lang.String")`获取。

### 访问字段
1. 获取字段的信息
2. 获取字段的值
3. 修改字段的值
### 调用方法
1. 获取方法的信息
2. 调用方法

### 调用构造方法
1. 无参的:`String.class.newInstance()`
2. 有参的:`Constructor`

### 获取继承关系

### 动态代理
可以在运行期动态创建某个接口的实例。
`InvocationHandler`用来负责实现接口的方法调用。通过`Proxy.newProxyInstance()`来创建接口实例。

首先理解什么是静态代理。静态代理有点代理模式的味道。不修改原实现类，从而达到对原实现类方法的增强。比如有这么一个需求，要求在实现类的每个方法前后打印日志。这时不需要修改原实现类，新建一个代理类，在代理类的方法中增强原实现类的方法。这样的弊处是麻烦。

动态代理的步骤
1. 获取接口的类信息
2. 新建一个代理Class对象拥有该接口的所有信息且有构造器
3. 通过这个代理Class对象来创建代理对象
4. 至于实例方法的实现是通过`InvocationHandler`来实现





## 注解
> 注解是放在Java源码的类、方法、字段、参数前的一种特殊“注释”
> **注释**会被编译器直接忽视；**注解**可以被编译器打包进入class文件，因此它是一种用作标注的“元数据”。

### 类型
1. 由编译器使用的注解`@Override`，这类注解不会被编译进`.class`文件。
2. 由工具处理`.class`文件使用的注解，会被编译进class文件但是不会存在于内存中。
3. 在程序运行期能够读取的注解，他们在加载后一直存在与JVM中。

### 定义注解
1. 使用`@interface`来创建定义注解。
2. 设置参数
3. 使用元注解修饰注解。
   1. `@Target` 定义注解能够被应用的位置
   2. `@Retention`定义注解的生命周期
   3. `@Repeatable`定义是否可重复使用
   4. `@Inherited`定义子类是否可以继承父类定义的注解
### 处理注解
对于`RUNTIME`类型的注解，要编写代码进行处理。例子：对JavaBean的属性值按规则进行检查。

## 泛型
> 泛型是编写模板代码来适应任意类型的。

### 应用
1. 集合类中
2. 在接口中也有使用，比如`Comparable<T>`接口。

### 擦拭法
Java的泛型实现方法是**擦拭法**——即虚拟机对泛型一无所知，所有工作是编译器做的。

编译器把类型`<T>`视为`Object`，根据`<T>`进行安全的强制转换。

于是泛型不能是基本类型的原因就是`Object`无法持有基本类型。

### extends通配符的作用
1. 在方法参数中限制集合时，在方法内部只能对集合进行读操作，不能进行写操作。
2. 可以限定T类型

### super通配符
作用与extends相反，是限定父类，而不是限定子类。以及允许调用写方法，不允许调用读方法。

例子是`Collections.copy()`方法。

### PECS原则
Producer Extends Consumer Super。

### 无限定通配符`<?>`
方法参数被`<?>`修饰后，在方法内既不能读也不能写，只能做一些`null`操作。且无限定通配符`<?>`很少使用，可以用`<T>`替换，同时它是所有`<T>`类型的超类。
