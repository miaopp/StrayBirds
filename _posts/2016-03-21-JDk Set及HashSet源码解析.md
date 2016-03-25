---
layout: post
title: JDk Set及HashSet源码解析
category: 技术
comments: true
---

# JDk Set及HashSet源码解析

## Set源码解析
首先我们看看在JDK中Set端口的源码：

```java
package asdf;

import java.util.*;
import java.util.Collection;

/**
 * Created by ping.miao on 2015/7/29.
 * set源码分析
 */
public interface Set<E> extends Collection<E> {
	int size();

	/**
	 * @return Set包含元素的个数
	 */
	boolean isEmpty();
	/**
	 * @return 判断Set是否为空，为空返回true，不为空返回false
	 */
	boolean contains(Object o);
	/**
	 * @return 判断set是否包含元素与o相等，假如o == null，那么只需要判断set中是否有元素为null 
	 * 假如有返回true，没有返回false，假如 o != null，判断set中是否有元素与o相等，有返回true， 
	 * 没有返回false
	 */
	Iterator<E> iterator();
	/**
	 * 返回包含set所有元素的Iterator
	 */
	Object[] toArray();
	/**
	 * 返回包含set所有元素的array
	 */
	<T> T[] toArray(T[] a);
	/**
	 * 返回一个包含set元素的指定类型的数组
	 */
	boolean add(E e);
	/**
	 * 插入元素，假如当前set中存在元素与e相等，那么保持原set不改变，返回false，
	 * 否则插入元素，并返回true
	 */
	boolean remove(Object o);
	/**
	 * remove类似于这样的元素(o == null? e == null : o.equals(e)),并返回true
	 */
	boolean containsAll(Collection<?> c);
	boolean addAll(Collection<? extends E> c);
	boolean retainAll(Collection<?> c);
	boolean removeAll(Collection<?> c);
	void clear();
	boolean equals(Object o);
	int hashCode();
}
```
从源码可以看出Set端口是Collection端口的子端口，上面的代码中已经做了详细的注解，这里不再赘述Set中各个方法是用来干什么的。

接下来我们看看HashSet的源码：

## HashSet源码：
首先看看HashSet到底是一个什么样的类，它继承了哪些类。
```java
public class HashSet<E>
	extends AbstractSet<E>
	implements Set<E>, Cloneable, java.io.Serializable
```

由上面的HashSet的头部可以看到，HashSet是AbstractSet的子类、Set的子接口，并且实现了其他额外的一些东西，Cloneable和Serializable。我们先看看AbstractSe中的部分源码。

```java
package asdf;

import java.util.*;
import java.util.Collection;
import java.util.Set;

/**
 * Created by ping.miao on 2015/7/30.
 */
public abstract class AbstractSet<E> extends AbstractCollection<E> implements java.util.Set<E> {
	protected AbstractSet() {
	}
	/**
	 * 构造方法
	 */

	public boolean equals(Object o) {
		if (o == this)
			return true;

		if (!(o instanceof Set))
			return false;
		java.util.Collection c = (Collection) o;
		if (c.size() != size())
			return false;
		try {
			return containsAll(c);
		} catch (ClassCastException unused)   {
			return false;
		} catch (NullPointerException unused) {
			return false;
		}
	}
	/**
	 * 比较Object o与set是否相等，o可以是单个元素，也可以是一个Set。o是单个元素的时候
	 * 假如set中存在与o相等的元素返回true，否则返回false。o是Set的时候，假若它与当前
	 * set长度相同，并且每个元素相等，返回true，否则返回false。
	 */

	public int hashCode() {
		int h = 0;
		Iterator<E> i = iterator();
		while (i.hasNext()) {
			E obj = i.next();
			if (obj != null)
				h += obj.hashCode();
		}
		return h;
	}
	/**
	 * 得到set的hashcode。假如当前值为null的时候，它的hashcode是0，最后返回的是set中
	 * 所有元素的hashcode的总和。
	 */

	public boolean removeAll(Collection<?> c) {
		boolean modified = false;
		if (size() > c.size()) {
			for (Iterator<?> i = c.iterator(); i.hasNext(); )
				modified |= remove(i.next());
		} else {
			for (Iterator<?> i = iterator(); i.hasNext(); ) {
				if (c.contains(i.next())) {
					i.remove();
					modified = true;
				}
			}
		}
		return modified;
	}
	/**
	 * 删除参数中给出的set中的所有元素，删除成功返回true，失败返回false
	 */
}
```
