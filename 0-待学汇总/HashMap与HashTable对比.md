> 知识路径：Android > Java > 集合
>
> version：2021/4/2
>
> review：2021/4/2
>
> 掌握程度：入门

## 一、预备知识

- 掌握HashMap源码；
- 掌握HashTable源码。

## 一、Java集合框架图

![image-20210402111534623](C:\Users\NJCS\AppData\Roaming\Typora\typora-user-images\image-20210402111534623.png)

- List接口存储一组不唯一，有序（插入顺序）的对象。
- Set接口存储一组唯一，无序的对象。

## 二、HashMap与HashTable对比

HashMap是非synchronized的，性能更好，HashMap可以接受为null的key-value。

HashTable是线程安全的，比HashMap慢，HashTable不接受为null的key-value。

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
    
    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
    
    
    
}
```

