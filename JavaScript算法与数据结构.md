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
#### 遍历
```text
Traverse(head)
    Pre:head is the head node in the list 
    Post: the items in the list have been traversed
    n ← head
    while n != 0
        yield n.value
        n ← n.next
    end while
end Traverse
```
#### 反向遍历
```text
ReverseTraversal(head, tail)
    Pre: head and tail belong to the same list 
    Post: the items in the  list have been traversed in reverse order
    if tail = φ
        curr ← tail
        while curr !=head
            prev ← head
            while prev.next != curr
                prev ← prev.next
            end while
            yield curr.value
            curr ← prev
        end while
        yield curr.value
    end if
end ReverseTraversal
```
## 双向链表
在计算机科学中，一个**双向链表(doubly linked list)**是由一组称为节点的顺序链接记录组成的链接数据结构。每个节点包含两个字段，称为链接，它们是对节点序列中上一个节点和下一个节点的引用。开始节点和结束节点的上一个链接和下一个链接分别指向某种终止节点，通常是前哨节点或null，以方便遍历。如果只有一个前哨节点，则列表通过前哨节点循环链接。它可以被概念化为两个由相同数据项组成的单链表，但顺序相反。
![alt](https://camo.githubusercontent.com/a77efae509d76b6329bf3752d5367aaa4d8905f0/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f352f35652f446f75626c792d6c696e6b65642d6c6973742e737667)
两个节点链接允许在任一方向上遍历列表。

在双向链表中进行添加或者删除节点时，需做的链接更改要比单向链表复杂得多。这种操作在单向链表中更简单高效，因为不需要关注一个节点(除第一个和最后一个节点以外的节点)的两个链接，而只需要关注一个链接即可。
### 基础操作的伪代码
#### 插入
```text
Add(value)
    Pre: value is the value to add to the list
    Post: value has been Placed at the tail of the list
    n ← node(value)
    if head = φ
        head ← n
        tail ← n
    else 
        n.previous ← tail
        tail.next ← n
        tail ← n
    end if
end Add
```
#### 删除
```text
Remove(head, value)
    Pre: head is the head node in the list
         value is the value to remove from the list
    Post: value is removed from the list, true, otherwise false
    if head = φ
        return false
    end if
    if value = head.value
        if head = tail
            head ← φ
        else
            head ← head.next
            head.previous ← φ
        end if 
        return true
    end if
    n ← head.next
    while n = φ and value = n.value
        n ← n.next
    end while
    if n = tail 
        tail ← tail.previous
        tail.next ← φ
        return true
    end if n = φ
        n.previous.next ← n.next
        n.next.previous ← n.previous
        return true
    end if 
    return true
end Remove
```
