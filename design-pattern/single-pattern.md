# 单例模式
单例模式，主要区分单线程下和多线程下的实现。

## 单线程
```
public class Singleton {
    private static Singleton instance = null;
    // 私有构造函数
    private Singleton(){
    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

```

## 多线程

### 实现1
```
public class Singleton{
    private static Singleton instance = null;
    private Singleton(){}
    // 加锁（非常耗时的操作）
    public synchronized static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
### 实现1的优化
加同步锁前后两次判断实例是否已存在
```
public class Singleton{
    private static Singleton instance = null;
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            // 不需要每次都进入了
           synchronized(this) {
                if (instance == null) {
                    instance = new Singleton();
                }
           }
        }
        return instance;
    }
}
```
### 私有构造函数
缺点是类加载时就初始化，浪费内存。
```
public class Singleton {
    private Singleton() {}

    private static Singleton instance = new Singleton();

    public static Singleton getInstance() {
        return instance;
    }
}
```
### 静态内部类
```
public class Singleton {
    private Singleton() {}

    private static class SingletonHolder {
         private static final Singleton instance = new Singleton();
    } 

    public static final Singleton getInstance() {
        return SingletonHolder.instance;
    }
}
```

### 枚举
```
public enum Singleton {
    INSTANCE;
    public void whateverMethod() {}
}
```

