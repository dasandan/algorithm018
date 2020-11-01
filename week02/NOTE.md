# 学习笔记
## 一、hashmap的源码解读

### 1.put（）

```
/* h = key.hashCode() ^ (h >>> 16);
在网上找了下资料，这段代码的用处在于将生成的hashcode的高16位扰动一下低16位，使得哈希冲突能够发生的少一点。
当你本身散列做的不好的情况下，最后的几个低位可能会呈规律性的重复。这时候用高16位来影响一下低16位，就可以加大低位的随机性。
*/

static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}

public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                 boolean evict) {
    Node<K, V>[] tab;
    Node<K, V> p;
    int n, i;
    //如果table没有初始化，调用resize（）方法初始化
    if ((tab = table) == null || (n = tab.length) == 0)
      n = (tab = resize()).length;
      /*因为hashmap的数组大小是按2的幂次来的，所以 （n-1）&hash 等价于 hash % n。
      但是&的效率比%的效率高。
      看看当前key对应索引中的首节点是不是null，是的话就创建一个新的链表
      */
    if ((p = tab[i = (n - 1) & hash]) == null)
      tab[i] = newNode(hash, key, value, null);
    else {
      Node<K, V> e;
      K k;
      //看插入键值对的key和hash是不是一样，一样得话就把value直接覆盖掉。
      if (p.hash == hash &&
              ((k = p.key) == key || (key != null && key.equals(k))))
        e = p;
        //判断是不是红黑树，是的话就调用红黑树的putTreeVal方法
      else if (p instanceof TreeNode)
        e = ((TreeNode<K, V>) p).putTreeVal(this, tab, hash, key, value);
      else {
        for (int binCount = 0; ; ++binCount) {//遍历链表
          if ((e = p.next) == null) {
            p.next = newNode(hash, key, value, null);
            if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st//如果超过了负载就将链表生成一棵红黑树
              treeifyBin(tab, hash);
            break;
          }
          if (e.hash == hash &&
                  ((k = e.key) == key || (key != null && key.equals(k))))
            break;
          p = e;
        }
      }
      if (e != null) { // existing mapping for key
        V oldValue = e.value;
        if (!onlyIfAbsent || oldValue == null)
          e.value = value;
        afterNodeAccess(e);
        return oldValue;
      }
    }
    ++modCount;
    //如果超过最大容量就扩容
    if (++size > threshold)
      resize();
    afterNodeInsertion(evict);
    return null;
  }
  
  
```

![img](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2016/45205ec2.png)

![img](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2016/d669d29c.png)

### 2.get（）

```
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final HashMap.Node<K, V> getNode(int hash, Object key) {
    HashMap.Node<K, V>[] tab;
    HashMap.Node<K, V> first, e;
    int n;
    K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
      if (first.hash == hash && // always check first node
              ((k = first.key) == key || (key != null && key.equals(k))))
        return first;
      if ((e = first.next) != null) {
        if (first instanceof HashMap.TreeNode)
          return ((HashMap.TreeNode<K, V>) first).getTreeNode(hash, key);
        do {
          if (e.hash == hash &&
                  ((k = e.key) == key || (key != null && key.equals(k))))
            return e;
        } while ((e = e.next) != null);
      }
    }
    return null;
  }
```

get方法的几个点：

get（）方法调用的是getNode（）方法。

1.判断一下table不为null，长度大于0，索引为（n-1）&  hash 的节点不为null则进入下一步判断。

2.判断首节点的key和hash是否一致，如果一致就返回首节点，否则进入下一步。

3.看看链表是不是只有一个节点，是的话返回null，否则进入下一步。

4.判断首节点是不是树节点，是就用getTreeNode（）方法遍历红黑树，否则就是链表

5.do while（（e = e.next) != null) 遍历链表，找到key和hash一致的节点，返回该节点，否则返回null。

## 二、学习小结

这个礼拜能够明显感觉到课的理解难度比上一周课难，可能是因为还没有系统接触到递归的原因。这周的递归题在过五毒神掌的时候，三四遍都还不能自己写出来。而且这周的课程内容的量感觉也比上周大，一定可以坚持下来。