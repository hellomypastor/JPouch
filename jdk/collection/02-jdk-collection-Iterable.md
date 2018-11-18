### Iterable

Iterable 接口是 Java 集合框架的顶级接口，实现此接口使集合对象可以通过迭代器 Iterator 遍历自身元素，Iterable 最早出现在 JDK 1.5。

### 接口定义

```java
public interface Iterable<T> {
    Iterator<T> iterator();
}
```

### 使用示例

```java
public class IterableTest {
	public static void main(String[] args) {
        iteratorCase();
    }

    public static void iteratorCase() {
        List<Integer> list = new ArrayList<Integer>();
        list.add(1);
        list.add(2);

        // 获取ArrayList内部迭代器
        Iterator<Integer> iterator = list.iterator();
        // hasNext()方法判断是否还有元素
        while (iterator.hasNext()) {
            // next()返回当前元素,并且将指针移向下个元素
            System.out.println(iterator.next());
        }
    }
}
```

此外我们还可以使用 for-each loop 形式进行遍历，增强 for 形式在 Java 中只是一个语法糖，实际编译的时候，还是会转换为迭代器形式，上面方法体可以改成：

```java
for (Integer integer : list) {
	System.out.println(integer);
}
```

进行迭代遍历的时候我们需要注意这种情况，就是在遍历的过程中，如果我们对元素进行添加删除，那么会造成并行修改异常 ConcurrentModificationException，如下：

```java
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
	Integer i = iterator.next();
    if (i == 2) {
    	list.remove(i);
    }
}
```

Iterator 是工作在一个独立的线程中，并且拥有一个 mutex 锁。 Iterator 被创建之后会建立一个指向原来对象的单链索引表，当原来的对象数量发生变化时，这个索引表的内容不会同步改变，所以当索引指针往后移动的时候就找不到要迭代的对象，所以按照 fail-fast 原则 Iterator 会马上抛出 java.util.ConcurrentModificationException 异常。

所以 Iterator 在工作的时候是不允许被迭代的对象被改变的。对于这种情况，我们应当使用迭代器 Iterator 内部的 remove() 方法，而不是使用集合 list 直接删除元素，正确写法为：

```java
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
	Integer i = iterator.next();
    if (i == 2) {
        // 使用迭代器进行删除元素,注意这里remove()没有参数,它是直接删除当前迭代的元素
    	iterator.remove();
    }
}
```

还有一点需要注意，我们在用迭代删除 Iterator.remove() 时，可能会因为没有执行 next() ， 抛出java.lang.IllegalStateException 异常，原因是通过 Iterator 来删除集合中某一个元素时，首先需要使用 next 方法迭代出集合中的元素 ，然后才能调用 remove 方法，否则集合可能会因为对同一个 Iterator remove 了多次而抛出 java.lang.IllegalStateException 异常，如下：

```java
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
    // 第二次会抛java.lang.IllegalStateException异常
    iterator.remove();
}
```

