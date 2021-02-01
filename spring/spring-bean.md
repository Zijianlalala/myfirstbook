#Spring Bean

## 定义
> `Spring Bean`是一个被实例化，组装，并通过Spring IoC容器所管理的对象。这些`Bean是由用容器提供的配置元数据创建的。

## 作用域
1. `Singleton`（默认）：单例
2. `prototype`：每次从容器中调用都返回一个新的实例。
3. 其他不大用到

## 生命周期
1. 定义
2. 初始化
3. 使用
4. 销毁

## 定义继承
可以定义一个父`bean`的定义作为模板，其他的子Bean可以从父Bean中继承所需要的配置。

```
<bean id="xxx" class="xxx" parent="xx">
    xxx
</bean>

```
