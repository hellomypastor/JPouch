### 迭代器（Iterator）

------

迭代器是一种设计模式，它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。

Java 中的 Iterator 功能比较简单，并且只能单向移动：

+ 使用方法 iterator() 要求容器返回一个 Iterator。第一次调用 Iterator 的 next() 方法时，它返回序列的第一个元素。注意：iterator() 方法是 java.lang.Iterable 接口,被 Collection 继承。
+ 使用 next() 获得序列中的下一个元素。
+ 使用 hasNext() 检查序列中是否还有元素。
+ 使用 remove() 将迭代器新返回的元素删除。

Iterator 是 Java 迭代器最简单的实现，为 List 设计的 ListIterator 具有更多的功能，它可以从两个方向遍历 List，也可以从 List 中插入和删除元素。

### 接口定义

```java
public interface Iterator<E> {
    // 判断容器内是否还有可供访问的元素
    boolean hasNext();
    // 返回迭代器刚越过的元素的引用，返回值是Object，需要强制转换成自己需要的类型
    E next();
    // 删除迭代器刚越过的元素
    void remove();
}
```

### 使用示例

```java
for(Iterator it = c.iterator(); it.hasNext(); ) {  
　　Object o = it.next();  
　　 // do something  
}  
```

