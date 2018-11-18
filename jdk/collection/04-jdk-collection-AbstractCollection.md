### AbstractCollection

AbstractCollection 是 Java 集合框架中 Collection 接口的一个直接实现类， Collection 下的大多数子类都继承 AbstractCollection ，比如 List 的实现类，Set 的实现类。

它实现了一些方法，也定义了几个抽象方法留给子类实现，因此它是一个抽象类。

### 抽象方法

```java
public abstract Iterator<E> iterator();
public abstract int size();
```

子类必须以自己的方式实现这两个方法。除此外，AbstractCollection 中默认**不支持添加单个元素**，如果直接调用 `add(E)` 方法，会报错：

```java
public boolean add(E e) {
	throw new UnsupportedOperationException();
}
```

### 实现的方法

+ addAll() 添加一个集合内的全部元素：

```java
public boolean addAll(Collection<? extends E> c) {
	boolean modified = false;
    // 获取待添加对象的迭代器
	Iterator<? extends E> e = c.iterator();
	while (e.hasNext()) {
        // 挨个遍历，调用 add() 方法添加，因此如果没有实现 add(E) 方法，addAll() 也不能用
        if (add(e.next())) {
            modified = true;
        }
	}
	return modified;
}
```

+ clear 删除所有元素

```java
public void clear() {
    // 获取子类实现的迭代器，挨个遍历，删除
	Iterator<E> e = iterator();
	while (e.hasNext()) {
	    e.next();
        // 单线程使用迭代器的 remove() 方法不会导致 fail-fast
	    e.remove();
	}
}
```

+ contains() 是否包含某个元素：

```java
public boolean contains(Object o) {
    // 获取子类实现的迭代器，挨个遍历，比较
	Iterator<E> e = iterator();
    // 目标元素是空也能查找，说明 AbstractCollection 默认是支持元素为 null 的
	if (o==null) {
        while (e.hasNext()) {
            if (e.next()==null) {
                return true;
            }
        }
	} else {
	    while (e.hasNext()) {
            //这个元素的类 需要重写 equals() 方法，不然结果够呛
            if (o.equals(e.next())) {
            	return true;   
            }
        }
	}
	return false;
}
```

+ containsAll() 是否包含指定集合中的全部元素：

```java
public boolean containsAll(Collection<?> c) {
	Iterator<?> e = c.iterator();
    // 挨个遍历指定集合
    while (e.hasNext()) {
        // contails 里也是遍历，双重循环，O(n^2)
        if (!contains(e.next())) {
            return false;
        }
    }
	return true;
}
```

+ isEmpty() 是否为空：

```java
public boolean isEmpty() {
    // 调用子类实现的 size() 方法
	return size() == 0;
}
```

+ remove() 删除某个元素：

```java
public boolean remove(Object o) {
    // 获取子类实现的 迭代器
	Iterator<E> e = iterator();
	if (o==null) {
	    while (e.hasNext()) {
			if (e.next()==null) {
		    	e.remove();
		    	return true;
			}
	    }
	} else {
	    while (e.hasNext()) {
			if (o.equals(e.next())) {
		    	e.remove();
		    	return true;
			}
	    }
	}
	return false;
}
```

+ removeAll() 删除指定集合中包含在本集合的元素：

```java
public boolean removeAll(Collection<?> c) {
	boolean modified = false;
	Iterator<?> e = iterator();
	while (e.hasNext()) {
        // 双重循环
	    if (c.contains(e.next())) {
			e.remove();
			modified = true;
	    }
	}
	return modified;
}
```

+ retainAll() 保留共有的，删除指定集合中不共有的：

```java
public boolean retainAll(Collection<?> c) {
	boolean modified = false;
	Iterator<E> e = iterator();
	while (e.hasNext()) {
        // 排除异己，不在我集合中的统统去掉
	    if (!c.contains(e.next())) {
			e.remove();
			modified = true;
	    }
	}
	return modified;
}
```

+ toArray(), toArray(T[] contents) 转换成数组：

```java
public Object[] toArray() {
	Object[] r = new Object[size()];
    Iterator<E> it = iterator();
	for (int i = 0; i < r.length; i++) {
        if (! it.hasNext()) {
            return Arrays.copyOf(r, i);
        }
	    r[i] = it.next();
	}
	return it.hasNext() ? finishToArray(r, it) : r;
}

public <T> T[] toArray(T[] a) {
    int size = size();
    T[] r = a.length >= size ? a :
              (T[])java.lang.reflect.Array
              .newInstance(a.getClass().getComponentType(), size);
    Iterator<E> it = iterator();

	for (int i = 0; i < r.length; i++) {
	    if (! it.hasNext()) {
            if (a != r) {
                return Arrays.copyOf(r, i);
            }
			r[i] = null;
			return r;
	    }
	    r[i] = (T)it.next();
	}
	return it.hasNext() ? finishToArray(r, it) : r;
}
```

+ toString() 把内容转换成一个 String 进行展示：

```java
public String toString() {
    if (isEmpty()) {
        return "[]";
    }
    // 注意默认容量是 size() 的 16 倍，为什么是 16 呢?
    StringBuilder buffer = new StringBuilder(size() * 16);
    buffer.append('[');
    // 仍旧用到了迭代器
    Iterator<?> it = iterator();
    while (it.hasNext()) {
        Object next = it.next();
        if (next != this) {
            // 这个 Object 也得重写 toString() 方法，不然不能输出内容
            buffer.append(next);
        } else {
            buffer.append("(this Collection)");
        }
        if (it.hasNext()) {
            buffer.append(", ");
        }
    }
    buffer.append(']');
    return buffer.toString();
}
```

### 其他

+ AbstractCollection 默认的构造函数是 protected：

```java
/**
 * Sole constructor.  (For invocation by subclass constructors, typically
 * implicit.)
 */
// 官方推荐子类自己创建一个 无参构造函数
protected AbstractCollection() {
}
```

+ AbstractCollection 的 add(E) 方法默认是抛出异常，这样会不会容易导致问题？为什么不定义为抽象方法？

答案译自 stackoverflow :

> 如果你想修改一个不可变的集合时，抛出 UnsupportedOperationException 是标准的行为，比如 当你用 Collections.unmodifiableXXX() 方法对某个集合进行处理后，再调用这个集合的 修改方法（add,remove,set…），都会报这个错；
> 因此 AbstractCollection.add(E) 抛出这个错误是准从标准；
> 那为什么会有这个标准呢？

在 Java 集合中，很多方法都提供了有用的默认行为，比如：

+ Iterator.remove()
+ AbstractList.add(int, E)
+ AbstractList.set(int, E)
+ AbstractList.remove(int)
+ AbstractMap.put(K, V)
+ AbstractMap.SimpleImmutableEntry.setValue(V)

而之所以没有定义为抽象方法，是因为可能有很多地方用不到这个方法，用不到还必须实现，这岂不是让人很困惑么。

原因跟和设计模式中的**接口隔离原则**有些相似：不要给客户端暴露不需要的方法。



