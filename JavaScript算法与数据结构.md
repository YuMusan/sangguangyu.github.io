# JavaScript算法与数据结构
## 数据结构
----
数据结构是在计算机中组织和存储的一种特殊方式，使得数据可以高效的被访问和修改。更确切的说，数据结构是数据值的集合，表示数据之间的关系，也包括了作用在数据上的函数或操作。
### 链表
在计算机科学中，一个链表是数据元素的线性集合，元素的线性顺序不是由它们在内存中的物理位置给出，相反每个元素指向下一个元素。它是由一组节点组成的数据结构，这些节点一起表示序列。

在最简单的形式下，每个节点由数据和到序列中下一个节点的引用组成。这种结构允许在迭代期间有效地从序列中的任何位置插入或删除元素。

更复杂的辩题添加额外的链接，允许有效地插入或删除任意元素引用。链表的一个缺点是访问时间是线性的(而且难以管道化)

如果需要更快的访问，如随机访问，是不可行的。与链表相比，数组具有更好的缓存位置。

![alt](https://camo.githubusercontent.com/37013b59008ed49a6701968da6b182eb6a9d24c8/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f362f36642f53696e676c792d6c696e6b65642d6c6973742e737667)

### 基本操作的伪代码
#### 插入
```text
Add(value)
    Pre: value is the value to add to the list
    Post: valye has been placed at the tail of the list
    n ← node(value)
    if head = ø
        head ← n
        tail ← n
    else
        tail.next ← n
        tail ← n
    end if
end Add
```
#### 搜索
```text
Contains(head,value)
    Pre: head is the head node in the list
         value is the value to search for
    Post: the item is either in he linked list ,true; otherwise false
    n ← head
    while n != ø and n.value != value
        n ← n.next
    end while
    if n = ø
        return false
    end if
    return true
end Contains
```
#### 删除
```text
Remove(head,value)
    Pre: head is the head node in the list
         value is the value to remove from the list
    Post: value is removed from the list, true, otherwise false
    if head = ø
        return false
    end if
    n ← head
    if n.value = value
        if head = tail
            head ← ø
            tail ← ø
        else
            head ← head.next
        end if
        return true 
    end if 
    while n.next != ø and n.next.value != value
        n ← n.next
    end while
    if n.text != ø
        if n.next =tail
            tail ← n
        end if
        n.next ← n.next.next
        return true
    end if
    return false
end Remove
```
