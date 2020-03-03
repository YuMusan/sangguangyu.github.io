# JavaScript算法与数据结构
## 数据结构
数据结构是在计算机中组织和存储的一种特殊方式，使得数据可以高效的被访问和修改。更确切的说，数据结构是数据值的集合，表示数据之间的关系，也包括了作用在数据上的函数或操作。
### 链表
在计算机科学中，一个链表是数据元素的线性集合，元素的线性顺序不是由它们在内存中的物理位置给出，相反每个元素指向下一个元素。它是由一组节点组成的数据结构，这些节点一起表示序列。

在最简单的形式下，每个节点由数据和到序列中下一个节点的引用组成。这种结构允许在迭代期间有效地从序列中的任何位置插入或删除元素。

更复杂的变体添加额外的链接，允许有效地插入或删除任意元素引用。链表的一个缺点是访问时间是线性的(而且难以管道化)

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
## 队列
在计算机科学中，一个队列(queue)是一种特殊类型的抽象数据类型或集合。集合中的实体按顺序保存。队列基本操作有两种：向队列的后端位置添加实体，称为入队，并从队列的前端位置移除实体，称为出队。队列中元素先进先出FIFO(first in , first out)示意

![alt](https://camo.githubusercontent.com/7fecf0b843d5f7b26e4514b4e9e047d6c84ee76b/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f352f35322f446174615f51756575652e737667)

## 栈
在计算机科学中，一个栈(stack)是一种抽象数据类型，用作表示元素的结合，具有两种主要操作：
* push，添加元素到栈的顶端(末尾)
* pop，移除栈最顶端(末尾)的元素

以上两种操作可以简单概括为后进先出(LIFO = last in, first out)
此外，应有一个`peek`操作用于访问当亲顶端(末尾)的元素

![alt](https://camo.githubusercontent.com/464c4087d283619fe8e8c77cf5805e45faa54ca9/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f622f62342f4c69666f5f737461636b2e706e67)

## 哈希表
在计算中，一个哈希表(hash table 或hash map)是一种实现**关联数组(associative array)**的抽象数据类型，该结构可以将**键映射到值**。

哈希表使用**哈希函数/散列函数**来计算一个值在数组或桶(buckets)中或槽(slots)中对应的索引，可使用该索引找到所需的值。

理想情况下，散列函数将为每个键分配给一个唯一的桶(bucket)，但是大多数哈希表设计采用不完美的散列函数，这可能会导致**哈希冲突(hash collisions)**,也就是散列函数为多个键生成了相同的索引，这种碰撞必须以某种方式进行处理。

![hash table](https://camo.githubusercontent.com/2b2b396c714c8344d3928c46d6b0f6be47d3d8c8/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f372f37642f486173685f7461626c655f335f315f315f305f315f305f305f53502e737667)

通过单独的链接解决哈希冲突

![hash collision](https://camo.githubusercontent.com/404b54bac0302f96ef42dcd4c9bc4fc5ea03ec0b/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f642f64302f486173685f7461626c655f355f305f315f315f315f315f315f4c4c2e737667)

## 堆(数据结构)
在计算机科学中，一个堆(heap)是一种特殊的基于树的数据结构他满足下面描述的堆属性。
在一个**最小堆(min heap)** 如果p是c的一个父级节点，那么p的key或(value)应小于或等于C的对应值

![最小堆](https://camo.githubusercontent.com/16e4220b69a866f97cc20d934c4b16fe5b9147de/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f362f36392f4d696e2d686561702e706e67)

在一个**最大堆(max heap)** 中,P的key(或value)大于C的对应值。

![最大堆](https://camo.githubusercontent.com/cf3c66d0d2ed67af70a8bc500fc215526d266a0d/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f332f33382f4d61782d486561702e737667)

在堆顶部的没有父级节点的节点，被称之为根节点。

## 优先队列
在计算机科学中，**优先级队列(priority queue)** 是一种抽象数据类型，它类似于常规的队列或栈，但每个元素都有与之关联的优先级

在优先队列中，低优先级的元素之前应该是高优先级的元素，如果两个元素具有相同的优先级，则根据它们在队列中的顺序是它们的出现顺序即可。

优先队列虽通常用堆来实现，但它在概念上与堆不同。优先队列是一个抽象概念，就像列表或图这样的抽象概念一样。

正如列表可以用链表或数组实现一样，优先队列可以用堆或各种其他方法实现，例如无序数组

## 字典树
在计算机科学中，**字典树(trie,中文又被称为单词查找树或键树)** 也称为数字树，有时候也被称为基数树或前缀树(因为它们可以通过前缀搜索)，它是一种搜索树，一种已排序的数据结构，通常用于存储动态集或键为字符串的关联数组。

与二叉搜索树不同，树上没有节点存储与该节点关联的键；相反，节点在树上的位置定义了与之关联的键。一个节点的全部后代节点都有一个与该节点关联的通用的字符串前缀，与根节点关联的是空字符串。

值对于字典树中关联的节点来说，不是必需的，相反值往往和相关的叶子相关，以及与一些键相关的内部节点相关。

有关字典树的空间优化示意，请参阅紧凑前缀树

![alt](https://camo.githubusercontent.com/3815e61b976f8ca1fee251556ac80b3acb25cefc/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f622f62652f547269655f6578616d706c652e737667)

## 树
在计算机科学中，**树(tree)** 是一种广泛使用的抽象数据类型(ADT)，或实现此ADT的数据结构--模拟分层树结构，具有根节点和有父节点的子树，表示为一组链接节点。

树可以被递归定义为一个(始于一个根节点)节点集，每个节点都是一个包含了值的数据结构，除了值，还有该节点的节点引用列表。树的节点之间没有引用重复的约束。

一棵简单的无序树，在下图中：
标记为7的节点具有两个子节点，标记为2和6，一个父节点标记为2，作为根节点，在顶部，没有父节点。
![alt](https://camo.githubusercontent.com/38340edffe661998f395184c2ac1578aea636788/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f662f66372f42696e6172795f747265652e737667)

### 二叉查找树(Binary Search Tree)
在计算机科学中，二叉查找树(Binary Search Tree)(有时称为有序或排序的二叉树)是一种特殊的容器：在内存中存储"元素(items)",(例如数字，名称等)的数据结构。它们允许快速查找，添加和删除元素，并且可以用于实现动态项目集或查找表，该查找表允许按其键查找某个项目(例如，按姓名查找某人的电话号码)。

二叉查找树将其关键字保存在排序的顺序中，以便查找和其他操作可以使用二叉搜索的原理：在树中查找关键字时(或在其中插入新关键字的位置)，它们从根到叶遍历该树，对存储在树节点中的键进行比较，基于这种比较决定在左侧或右侧子树中继续搜索。一般来说，这样意味着每次比较都允许操作跳过树的大约一半节点，因此每次查找，插入或删除所花的时间与树中存储的元素数目的对数成正比。这比(未排序)数组中按键查找元素所需的线性时间要少得多，但是比哈希表上的相应操作要慢。

下图的二叉查找树，大小为9，深度为3，根为8，没有画叶子。

![alt](https://camo.githubusercontent.com/cd4bc41832630c9db51a7216109ac209e23d97a7/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f642f64612f42696e6172795f7365617263685f747265652e737667)

### Pseudocode for Basic Operations基本操作的伪代码
#### insertion
```text
insert(value)
    Pre: value has passed custom type checks for type T
    Post: value has been placed in the correct location in the tree
    if root = φ
        root ← node(value)
    else
        insertNode(root, value)
    end if
end insert

insertNode(current,value)
    Pre: current is the node to start from
    Post:value has been placed in the correct location in the tree
    if value < current.value
        if current.left = φ
            current.left ← node(value)
        else
            insertNode(current.left, value)
        end if
    else
        if current.right = φ
            current.right ← node(value)
        else
            insertNode(current.right,value)
        end if
    end if
end insertNode
```
##### searching
```text
cotains(root, value)
    Pre:root is the root node of the tree,value is what we would like to locate
    Post: value is either located or not
    if root = φ
        return false
    end if
    if root.value = value
        return true
    end if value <root.value
        return contains(root.left, value)
    else
        return cotains(root.right, value)
```
#### deletion
```text
remove(value)
    Pre: value is the value of the node to remove, root is the node of the BST count is the number of items in the BST
    Post: node with value is removed if found in which case yields true, otherwise false
    nodeToRemove ← findNode(value)
    if nodeToRemove = φ
        return false
    end if
    parent ← findParent(value)
    if count = 1
        root ← φ
    else if nodeToRemove.left = φ and nodeToRemove.right = φ
        if nodeToRemove.value < parent.value
            parent.left ← nodeToRemove.right
        else
            parent.right ← nodeToRemove.right
        end if
    else if nodeToRemove.left != φ and nodeToRemove.right != φ
        next ← nodeToRemove.right
        while next.left != φ
            next ← next.left
        end while
        if next != nodeToRemove.right
            remove(next.value)
            nodeToRemove.value ← next.value
            nodeToRemove.right ← nodeToRemove.right.right
        end if
    else
        if nodeToRemove.left = φ
            next ← nodeToRemove.right
        else
            next ← nodeToRemove.left
        end if
        if root = nodeToRemove
            root =next
        else if parent.left = nodeToRemove
            parent.left = next
        else if parent.right = nodeToRemove
            parent.right = next 
        end if
    end if 
    count ← count - 1
    return true
end remove
```
#### Find Parent of Node
```text
findParent(value, root)
    Pre: value is the value of the node we want to find the parent of root is the root node of the BST and is != φ
    Post: a reference to the prent node of value if found; otherwise φ
    if value = root.value
        retrun φ
    end if
    if value < root.value
        if root.left = φ
            return φ
        else if root.left.value = value
            return root
        else
            return findParent(value, root.left)
        end if
    else
        if root.right = φ
            return φ
        else if root.right.value = value
            return root
        else
            return findParent(value, root.right)
        end if
    end if
end findParent
```
#### Find Node
```text
findNode(root, value)
    Pre: value is the value of the node we want to find the parent of root is the root node of the BST
    Post: a referrence to the node of value if found; otherwise φ
    if root = φ
        return φ
    end if
    if root.value = value
        return root
    else if value < root.value
        return findNode(root.left, value)
    else
        return findNode(root.right, value)
    end if 
end findNode
```
#### Find Minimum
```text
findMin(root)
    Pre: root is the root node of the BST
        root = φ
    Post: the smallest value in the BST is located
    if root.left = φ
        return root.value
    end if
    findMin(root.left)
end findMin
```
#### Find Maximum
```text
findMax(root)
    Pre: root is the root node of the BST root = φ
    Post: the largest value in the BST is located
    if root.right = φ
        return root.value
    end if
    findMax(root.right)
end findMax
```
#### InOrder Traversal
```text
inorder(root)
    Pre: root is the root node of the BST
    Post: the nodes in the BST have been visited in inorder
    if root = φ
        inorder(root.left)
        yield root.value
        inorder(root.right)
    end if
end inorder
```






##### Update Bit
此方法是"清除位"和"设置位"方法的组合。
```js
/**
 * @param {number} number
 * @param {number} bitPosition - zero based.
 * @param {number} bitValue - 0 or 1.
 * @return {number}
 */
export default function updataBit(number, bitPosition, bitValue) {
    // normalized bit value
    const bitValueNormalized = bitValue?1:0;
    //init clear mask
    const clearMask = ~(1 << bitPosition);
    //clear bit value and then set it up to required value
    return (number & clearMask) | (bitValueNormalized << bitPosition)
}
```
##### isEven
此方法确定提供的数字是否为偶数。这是基于奇数的最后一个正确位被设置为1的事实。
```text
Number: 5 = 0b0101
isEven: false

Number: 4 = 0b0100
isEven: true
```
```js
/**
 * @param {number} number
 * @return {boolean}
 */
export default function isEven(number) {
    retrun (number & 1) === 0;
}
```
##### isPositive
此方法确定数字是否为正。它基于这样一个事实：所有正数的最左边的位都设置为0。但是，如果提供的数字为零或负零，则仍应返回false。
```text
Number: 1 = 0b0001
isPositive: true

Number: -1 = -0b0001
isPositive: false
```
```js
/**
 * @param {number} number - 32-bit integer.
 * @return {boolean}
 */
export default function isPositive(number) {
    //Zero is neither a positive nor a negative number
    if(number === 0) {
        return false
    }
    //the most significant 32nd bit can be used to determine whether the number is positive.
    return ((number >> 31) & 1) === 0; 
}
```
##### Multiply By Two 乘以2
此方法将原始数字向左移动一位。因此，所有二进制数的分量（2的幂）都是乘2的，因此数字本身是乘2的。
```text
Before the shift
Number: 0b0101 = 5
Powers of two: 0 + 2^2 + 0 + 2^0

After the shift
Number: 0b1010 = 10
Powers of two: 2^3 + 0 + 2^1 + 0
```
```js
/**
 * @param {number} number
 * @return {number}
 */
export default function multiplyByTwo(number) {
    return number << 1;
}
```
##### Divide By Two
此方法将原始数字向右移动一位。因此，所有二进制数的分量（2的幂）都被2除，因此数字本身被2除，没有余数。
```text
Before the shift
Number: 0b0101 = 5
Powers of two: 0 + 2^2 + 0 + 2^0

After the shift
Number: 0b0010 = 2
Powers of two: 0 + 0 + 2^1 + 0
```
```js
/**
 * @param {number} number
 * @return {number}
 */
export default function divdeByTwo(number) {
    return number >> 1;
}
```
##### Switch Sign
这种方法使正数变成负数和倒数。为了做到这一点，它使用了“两个补码”的方法，通过反转数字的所有位并加上1来实现。
```text
1101 -3
1110 -2
1111 -1
0000  0
0001  1
0010  2
0011  3
```
```js
/**
 * Switch the sign of the number using "Twos Complement" approach.
 * @param {number} number
 * @return {number}
 */
export default function switchSign(number) {
    return ~number + 1;
}
```
##### Multiply Two Signed Numbers 两个有符号的数字相乘
此方法使用位运算符将两个有符号整数相乘。该方法基于以下事实：
```text
a * b can be written in the below formats:
  0                     if a is zero or b is zero or both a and b are zeroes
  2a * (b/2)            if b is even
  2a * (b - 1)/2 + a    if b is odd and positive
  2a * (b + 1)/2 - a    if b is odd and negative
```
这种方法的优点是，在每个递归步骤中，一个操作数减少到其原始值的一半。因此，运行时复杂性为O（log（b）），其中b是在每个递归步骤上减少到一半的操作数。
```js
function isEven(number) {
    retrun (number & 1) === 0;
}

function isPositive(number) {
    //Zero is neither a positive nor a negative number
    if(number === 0) {
        return false
    }
    //the most significant 32nd bit can be used to determine whether the number is positive.
    return ((number >> 31) & 1) === 0; 
}

function multiplyByTwo(number) {
    return number << 1;
}

function divdeByTwo(number) {
    return number >> 1;
}

export default function multiply(a,b) {
    // if a is zero or b is zero or if both a and b are zeros then the pro duction is also zero
    if (b === 0 || a === 0) {
        return 0;
    }
    // Otherwise we will have four different cases that are described above.
    const multiplyByOddPositive = ()=> multiply(multiplyByTwo(a),divideByTwo(b-1)) + a;
    const multiplyByOddNegative = ()=> multiply(multiplyByTwo(a),divideByTwo(b+1)) - a;
    const multiplyByEven = ()=> multiply(multiplyByTwo(a),devideByTwo(b));
    const multiplyByOdd = ()=> (isPositive(b) ? multiplyByOddPositive() : multiplyByOddNegative());
    return isEven(b) ? multiplyByEven() : multiplyByOdd();
}
```
