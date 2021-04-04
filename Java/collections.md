# Java集合框架

Collection

1. List
2. Set
3. Queue

## ArrayList

顺序表，支持随机访问。

### 内部结构

数组`Object[] elementData`

### 扩容

默认容量是10。

当数组元素个数超过数组长度时，将数组容量扩大为之前的1.5倍

```
int newCapacity = oldCapacity + (oldCapacity >> 1);//扩大
elementData = Arrays.copyOf(elementData, newCapacity);//复制
```

### add

```
1. 判断是否要扩容
2. elementData[size++] = e;//存入对象
```

### remove

```
1. 将待删除的元素后的元素向前移动1位（通过复制来实现）
2. elementData[--size]=null;
```

### Vector

与ArrayList相似，在操作数组的方法上使用`synchronized`关键字修饰。

## LinkedList

### 内部结构

双向链表实现，维护头结点`first`和尾结点`last`

```
class Node {
	E item;
	Node next;
	Node prev;
}
```



## HashMap

### 内部结构

哈希数组+链表+红黑树。

通过拉链法解决哈希冲突。其他方法还有开放定址法等。

```
Node<K, V>[] table;//哈希数组

class Node<K,V> implements Map.Entry<K,V> {
	final int hash;
	final K key;
	V value;
	Node<K,V> next;
	
	// 重写了equals&hashCode方法
	final int hashCode(){return Objects.hashCode(key)^Objects.hashCode(value);}//键和值的哈希值异或运算
	equals//判断两个对象的键的哈希码和值的哈希码是否相等
}
```

### hash算法

```
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);//键哈希值的高16位和低16位进行异或运算，使高位参与进来，减少冲突。
    }
```

### 扩容

默认容量是16；

几个重要参数

1. `int threshold`，`threshold = length * loadFactor`

2. `final float loadFactor` 默认是0.75

3. `size`

   

当`size > threshold`的时候，进行`resize()`操作。

1. 设置新的容量，`newCap = oldCap << 1`扩大两倍
2. 创建新数组，将旧数组每个index上的元素转移到新数组中
   1. index上只有一个节点`newTab[e.hash & (newCap-1)] = e`
   2. index上是红黑树，`split`
   3. index上是链表，将链表根据`e.hash & oldCap`是否等于0分成两个链表
      1. 等于0，则是`loHead`，将其插入到新数组的原位置`index`上
      2. 不等于0，则 `hiHead`，并将`hiHead`插到新数组的`index+oldCap`的位置上

### put操作

逻辑：

1. 判断哈希数组是否初始化
2. 判断元素对应数组下标是否为空
   1. 是，直接插入
   2. 否
      1. 若第一个元素是待插入元素，返回当前第一个元素
      2. 若第一个元素是树节点，返回插入后的元素
      3. 若第一个元素是链表，遍历链表上的元素
         1. 若链表上没有该插入元素，将其插入到链表尾部，并判断是否要树化
         2. 若链表上有插入元素，则返回之
      4. 如果返回的元素不为空，则更新它的value值
3. 判断是否要扩容

### 优化

#### 结构优化

当链表元素过长时，大于8个结点时，将其转化为红黑树

#### 运算优化

容量始终是2的整数次幂的好处

1. 计算数组对应下标时，通过位运算替代了模运算，`index = e.hash & (capacity-1)`
2. 计算扩容后的数组下标时, `newIndex = oldIndex + oldCap`，节省了重新计算哈希的时间。

## ConcurrentHashMap

### 内部结构

### 如何保证线程安全



## LinkedHashMap

### 内部结构

继承了`HashMap`

1. 双向链表：维护插入顺序或者LRU顺序
2. 哈希数组

### 特别方法

`afterNodeAccess`节点被访问时调用，将结点移动尾部

`afterNodeInsertion`插入节点时调用，维护链表，可能会删除最旧的结点





