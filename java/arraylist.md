## ArrayList

ArrayList 是一个数组队列，相当于 动态数组.
与Java中的数组相比，它的容量能动态增长.
它继承于AbstractList, 实现了List, RandomAccess, Cloneable, java.io.Serializable这些接口。

ArrayList 继承了AbstractList，实现了List。它是一个数组队列，提供了相关的添加、删除、修改、遍历等功能。

ArrayList中的操作不是线程安全的

```
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

### ArrayList 构造函数

```
// 默认构造函数
ArrayList()

// capacity是ArrayList的默认容量大小。当由于增加数据导致容量不足时，容量会添加上一次容量大小的一半。
ArrayList(int capacity)

// 创建一个包含collection的ArrayList
ArrayList(Collection<? extends E> collection)
```
### ArrayList 的 api

```
// Collection中定义的API
boolean             add(E object)
boolean             addAll(Collection<? extends E> collection)
int                 size()
...
```

### ArrayList 的三种遍历方式

迭代器遍历
```
Iterator<Integer> it = arrayList.iterator();
while(it.hasNext()){
    System.out.print(it.next() + " ");
}
```

索引遍历

```
for(int i = 0; i < arrayList.size(); i++){
   System.out.print(arrayList.get(i) + " ");
}
```

for 遍历

```
for(Integer number : arrayList){
   System.out.print(number + " ");
}
```

* 遍历ArrayList时，通过索引值遍历效率最高，for循环遍历次之，迭代器遍历最低
