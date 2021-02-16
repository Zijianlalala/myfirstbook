# 注解
几个重要的注解
1. `@Required`
2. `@Autowired`
3. `@Qualifier`

首先要在配置文件的中增加有关注解的命名空间之类
```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:annotation-config/>

</beans>
```

## `@Required`
应用与`bean`属性的`Setter`方法，它表明受影响的`bean`属性在配置时必须放在`XML`配置文件中。

## `@Autowired`
为我们注入一个定义好的`bean`。

可以应用到：
1. 构造函数
2. `setter`方法
3. 具有任意名称和多个参数的方法
4. 字段（最常用）

### 注释在setter方法上

即便有注解，还是要在配置文件中声明`bean`的