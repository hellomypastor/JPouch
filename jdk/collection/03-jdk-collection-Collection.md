### Collection

Collection 继承了 Iterable接口，包含了集合的基本操作。

### 接口定义

```java
public interface Collection<E> extends Iterable<E> {
    // 返回当前集合中元素的数量
    int size();
	// 判断集合是否为空
    boolean isEmpty();
    // 查找集合中是否包含某个对象
    boolean contains(Object o);
	// 返回迭代器
    Iterator<E> iterator();
    // 转化为数组
    Object[] toArray();
    // 转化为数组，与toArray()不同的是要先申请好数组大小
    <T> T[] toArray(T[] a);
    // 添加对象到集合
    boolean add(E e);
    // 删除指定对象
    boolean remove(Object o);
	// 查找集合中是否有集合c中的元素
    boolean containsAll(Collection<?> c);
	// 将集合c中的所有元素添加到该集合
    boolean addAll(Collection<? extends E> c);
	// 从集合中删除c集合中也有的元素
    boolean removeAll(Collection<?> c);
	// 从集合中删除集合c中不包含的元素
    boolean retainAll(Collection<?> c);
    // 清空集合
    void clear();
    
    boolean equals(Object o);
    int hashCode();
}
```

