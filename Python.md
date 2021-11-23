# Python

>turtle是Python内置的一个有趣的模块，可以在屏幕上绘制图形。

## 变量和类型
在程序设计中，变量是一种存储数据的载体。计算机中的变量是实际存在的数据或者说是存储器中存储数据的一块内存空间，变量的值可以被读取和修改，这是所有计算和控制的基础。计算机能处理的数据有很多种类型，除了数值之外还可以处理文本、图形、音频、视频等各种各样的数据，那么不同的数据就需要定义不同的存储类型。Python中的数据类型很多，而且也允许自定义新的数据类型。介绍几种常用的数据类型：

* 整型：Python中可以处理任意大小的整数，而且支持二进制（如0b100，换算成十进制是4）、八进制（如0o100，换算成十进制是64）、十进制（100）和十六进制（0x100，换算成十进制是256）的表示法。
* 浮点型：浮点数也就是小数，之所以称为浮点数，是因为按照科学记数法表示时，一个浮点数的小数点位置是可变的，浮点数除了数学写法（如123.456）之外还支持科学计数法（如1.23456e2）。
* 字符串型：字符串是以单引号或双引号括起来的任意文本，比如'hello'和"hello",字符串还有原始字符串表示法、字节字符串表示法、Unicode字符串表示法，而且可以书写成多行的形式（用三个单引号或三个双引号开头，三个单引号或三个双引号结尾）。
* 布尔型：布尔值只有True、False两种值，要么是True，要么是False，在Python中，可以直接用True、False表示布尔值（请注意大小写），也可以通过布尔运算计算出来（例如3 < 5会产生布尔值True，而2 == 1会产生布尔值False）。

### 变量命名
在Python中，变量命名需要遵循以下这些必须遵守硬性规则和强烈建议遵守的非硬性规则。

* 硬性规则：
    - 变量名由字母（广义的Unicode字符，不包括特殊字符）、数字和下划线构成，数字不能开头。
    - 大小写敏感（大写的a和小写的A是两个不同的变量）。
    - 不要跟关键字（有特殊含义的单词）和系统保留字（如函数、模块等的名字）冲突。
* PEP 8要求：
    - 用小写字母拼写，多个单词用下划线连接。
    - 受保护的实例属性用单个下划线开头。
    - 私有的实例属性用两个下划线开头

在Python中可以使用type函数对变量的类型进行检查。
可以使用Python中内置的函数对变量类型进行转换。

* int()：将一个数值或字符串转换成整数，可以指定进制。
* float()：将一个字符串转换成浮点数。
* str()：将指定的对象转换成字符串形式，可以指定编码。
* chr()：将整数转换成该编码对应的字符串（一个字符）。
* ord()：将字符串（一个字符）转换成对应的编码（整数）。

```python
print('%d + %d = %d' % (a, b, a + b))
print('%d - %d = %d' % (a, b, a - b))
print('%d * %d = %d' % (a, b, a * b))
print('%d / %d = %f' % (a, b, a / b))
print('%d // %d = %d' % (a, b, a // b))
print('%d %% %d = %d' % (a, b, a % b))
print('%d ** %d = %d' % (a, b, a ** b))
```
print函数中输出的字符串使用了占位符语法，其中%d是整数的占位符，%f是小数的占位符，%%表示百分号（因为百分号代表了占位符，所以带占位符的字符串中要表示百分号必须写成%%），字符串之后的%后面跟的变量值会替换掉占位符然后输出到终端中

### 运算符
is, is not 身份运算符 in, not in 成员运算符 not, or, and逻辑运算符

Python中的逻辑运算符是具有短路功能，如果and左边的布尔值是False，不管右边的布尔值是什么，最终的结果都是False，or运算符在它左边的布尔值为True的情况下，右边的表达式根本不会执行。not运算符的后面会跟上一个布尔值，它的作用是得到与该布尔值相反的值

## 分支结构

### if语句
在Python中，要构造分支结构可以使用if、elif和else关键字。所谓关键字就是有特殊含义的单词，像if和else就是专门用于构造分支结构的关键字

>需要说明的是和C/C++、Java等语言不同，Python中没有用花括号来构造代码块而是使用了缩进的方式来表示代码的层次结构
>连续的代码如果又保持了相同的缩进那么它们属于同一个代码块，相当于是一个执行的整体
>缩进可以使用任意数量的空格，但通常使用4个空格

```python
"""
分段函数求值

        3x - 5  (x > 1)
f(x) =  x + 2   (-1 <= x <= 1)
        5x + 3  (x < -1)
"""

x = float(input('x = '))
if x > 1:
    y = 3 * x - 5
elif x >= -1:
    y = x + 2
else:
    y = 5 * x + 3
print('f(%.2f) = %.2f' % (x, y))
```
分支结构也可以嵌套
```python
"""
分段函数求值
		3x - 5	(x > 1)
f(x) =	x + 2	(-1 <= x <= 1)
		5x + 3	(x < -1)
"""

x = float(input('x = '))
if x > 1:
    y = 3 * x - 5
else:
    if x >= -1:
        y = x + 2
    else:
        y = 5 * x + 3
print('f(%.2f) = %.2f' % (x, y))
```
>Python之禅中有这么一句话“Flat is better than nested.”，所以提倡代码“扁平化”是因为嵌套结构的嵌套层次多了之后会严重的影响代码的可读性，所以能使用扁平化的结构时就不要使用嵌套

## 循环结构

### for-in循环
如果明确知道循环执行的次数或者对一个容器进行迭代，推荐使用for-in循环

```python
"""
用for循环实现1~100求和
"""

sum = 0
for x in range(101):
    sum += x
print(sum)
```
上面代码中的range(1, 101)可以用来构造一个从1到100的范围
>range(101)：可以用来产生0到100范围的整数，需要注意的是取不到101。
>range(1, 101)：可以用来产生1到100范围的整数，相当于前面是闭区间后面是开区间。
>range(1, 101, 2)：可以用来产生1到100的奇数，其中2是步长，即每次数值递增的值。
>range(100, 0, -2)：可以用来产生100到1的偶数，其中-2是步长，即每次数字递减的值。

```py

# 用for循环实现1~100之间的偶数求和

sum = 0
for x in range(2, 101, 2):
    sum += x
print(sum)
```
### while循环
如果构造不知道具体循环次数的循环结构，推荐使用while循环。while循环通过一个能够产生或转换出bool值的表达式来控制循环，表达式的值为True则继续循环；表达式的值为False则结束循环。

```py
# 猜数字游戏
import random

answer = random.randint(1, 100)
counter = 0
while True:
    counter += 1
    number = int(input('请输入: '))
    if number < answer:
        print('大一点')
    elif number > answer:
        print('小一点')
    else:
        print('恭喜你猜对了!')
        break
print('你总共猜了%d次' % counter)
if counter > 7:
    print('你的智商余额明显不足')
```

上面的代码中使用了break关键字来提前终止循环，需要注意的是break只能终止它所在的那个循环，这一点在使用嵌套的循环结构（下面会讲到）需要引起注意。除了break之外，还有另一个关键字是continue，它可以用来放弃本次循环后续的代码直接让循环进入下一轮。

和分支结构一样，循环结构也是可以嵌套的，也就是说在循环中还可以构造循环结构。
```py
# 输出乘法表
for i in range(1, 10):
    for j in range(1, i + 1):
        print('%d*%d=%d' % (i, j, i * j), end='\t')
    print()
```

>学习了Python的核心语言元素（变量、类型、运算符、表达式、分支结构、循环结构等）之后，必须做的一件事情就是尝试用所学知识去解决现实中的问题，换句话说就是锻炼自己把用人类自然语言描述的算法（解决问题的方法和步骤）翻译成Python代码的能力

## 函数和模块的使用

函数是绝大多数编程语言中都支持的一个代码的"构建块"

### 定义函数
在Python中可以使用def关键字来定义函数，和变量一样每个函数也有一个响亮的名字，而且命名规则跟变量的命名规则是一致的。在函数名后面的圆括号中可以放置传递给函数的参数，函数执行完成后可以通过return关键字来返回一个值，例如：
```py
# 求阶乘
def fac(num):
    result = 1
    for n in range(1, num + 1):
        result *= n
    return result


m = int(input('m = '))
n = int(input('n = '))
# 当需要计算阶乘的时候不用再写循环求阶乘而是直接调用已经定义好的函数
print(fac(m) // fac(n) // fac(m - n))
```
### 函数的参数
在Python中，函数的参数可以有默认值，也支持使用可变参数，所以Python并不需要像其他语言一样支持函数的重载，因为在定义一个函数的时候可以让它有多种不同的使用方式，下面是两个小例子。
```py
from random import randint

def roll_dice(n=2):
    """摇色子"""
    total = 0
    for _ in range(n):
        total += randint(1, 6)
    return total

def add(a=0, b=0, c=0):
    """三个数相加"""
    return a + b + c

# 如果没有指定参数那么使用默认值摇两颗色子
print(roll_dice())
# 摇三颗色子
print(roll_dice(3))
print(add())
print(add(1))
print(add(1, 2))
print(add(1, 2, 3))
# 传递参数时可以不按照设定的顺序进行传递
print(add(c=50, a=100, b=200))
```
在上面的代码中我们可以用各种不同的方式去调用add函数，这跟其他很多语言中函数重载的效果是一致的。

当可能会对0个或多个参数进行加法运算，而具体有多少个参数是由调用者来决定时，函数的设计者对这一点是一无所知的，因此在不确定参数个数时可以使用可变参数，代码如下所示
```py
# 在参数名前面的*表示args是一个可变参数
def add(*args):
    total = 0
    for val in args:
        total += val
    return total


# 在调用add函数时可以传入0个或多个参数
print(add())
print(add(1))
print(add(1, 2))
print(add(1, 2, 3))
print(add(1, 3, 5, 7, 9))
```
### 用模块管理函数
对于任何一种编程语言来说，给变量、函数这样的标识符起名字都是一个让人头疼的问题，因为我们会遇到命名冲突这种尴尬的情况。最简单的场景就是在同一个.py文件中定义了两个同名函数，由于Python没有函数重载的概念，那么后面的定义会覆盖之前的定义，也就意味着两个函数同名函数实际上只有一个是存在的。

Python中每个文件就代表了一个模块（module），在不同的模块中可以有同名的函数，在使用函数的时候我们通过import关键字导入指定的模块就可以区分到底要使用的是哪个模块中的foo函数，代码如下所示
```py
# module1.py
def foo():
    print('hello, world!')

# module2.py

def foo():
    print('goodbye, world!')

#test.py

from module1 import foo
# 输出hello, world!
foo()

from module2 import foo
# 输出goodbye, world!
foo()
```

也可以按照如下所示的方式来区分到底要使用哪一个foo函数。
```py
# test.py

import module1 as m1
import module2 as m2

m1.foo()
m2.foo()
```
> __name__是Python中一个隐含的变量,它代表了模块的名字
> 只有被Python解释器直接执行的模块的名字才是__main__

```py
def is_prime(num):
    """判断一个数是不是素数"""
    for factor in range(2, int(num ** 0.5) + 1):
        if num % factor == 0:
            return False
    # 三元表达式
    return True if num != 1 else False
```

### 变量的作用域
最后，来讨论一下Python中有关变量作用域的问题。

```py
def foo():
    b = 'hello'

    # Python中可以在函数内部再定义函数
    def bar():
        c = True
        print(a)
        print(b)
        print(c)

    bar()
    # print(c)  # NameError: name 'c' is not defined


if __name__ == '__main__':
    a = 100
    # print(b)  # NameError: name 'b' is not defined
    foo()
```

上面的代码能够顺利的执行并且打印出100、hello和True，在bar函数的内部并没有定义a和b两个变量，那么a和b是从哪里来的。在上面代码的if分支中定义了一个变量a，这是一个全局变量（global variable），属于全局作用域，因为它没有定义在任何一个函数中。在上面的foo函数中定义了变量b，这是一个定义在函数中的局部变量（local variable），属于局部作用域，在foo函数的外部并不能访问到它；但对于foo函数内部的bar函数来说，变量b属于嵌套作用域，在bar函数中是可以访问到它的。bar函数中的变量c属于局部作用域，在bar函数之外是无法访问的。
事实上，Python查找一个变量时会按照“局部作用域”、“嵌套作用域”、“全局作用域”和“内置作用域”的顺序进行搜索，前三者在上面的代码中已经看到了，所谓的“内置作用域”就是Python内置的那些标识符，之前用过的input、print、int等都属于内置作用域。

从现在开始可以将Python代码按照下面的格式进行书写，这一点点的改进其实就是在理解了函数和作用域的基础上跨出的巨大的一步。

```py
def main():
    # Todo: Add your code here
    pass


if __name__ == '__main__':
    main()
```

## 数据类型
### 字符串
字符串，是由零个或多个字符组成的有限序列，在Python程序中，把单个或多个字符用单引号或双引号表示。
Python为字符串类型提供了非常丰富的运算符。
```py
s1 = 'hello' * 3
# hello hello hello
s2 =  'word'
s1 += s2
print(s1) # hello hello hello world
print('ll' in s1) # True
print('good' in s1) # False
str2 = 'abc123456'
# 从字符串中取出指定位置的字符(下标运算)
print(str2[2]) # c
# 字符串切片(从指定的开始索引到指定的结束索引)
print(str2[2:5]) # c12
print(str2[2:]) # c123456
print(str2[2::2]) # c246
print(str2[::2]) # ac246
print(str2[::-1]) # 654321cba
print(str2[-3:-1]) # 45
```
还可以通过一系列的方法来完成对字符串的处理
```py
str1 = 'hello, world!'
# 通过内置函数len计算字符串的长度
print(len(str1)) # 13
# 获得字符串首字母大写的拷贝
print(str1.capitalize()) # Hello, world!
# 获得字符串每个单词首字母大写的拷贝
print(str1.title()) # Hello, World!
# 获得字符串变大写后的拷贝
print(str1.upper()) # HELLO, WORLD!
# 从字符串中查找子串所在位置
print(str1.find('or')) # 8
print(str1.find('shit')) # -1
# 与find类似但找不到子串时会引发异常
# print(str1.index('or'))
# print(str1.index('shit'))
# 检查字符串是否以指定的字符串开头
print(str1.startswith('He')) # False
print(str1.startswith('hel')) # True
# 检查字符串是否以指定的字符串结尾
print(str1.endswith('!')) # True
# 将字符串以指定的宽度居中并在两侧填充指定的字符
print(str1.center(50, '*'))
# 将字符串以指定的宽度靠右放置左侧填充指定的字符
print(str1.rjust(50, ' '))
str2 = 'abc123456'
# 检查字符串是否由数字构成
print(str2.isdigit())  # False
# 检查字符串是否以字母构成
print(str2.isalpha())  # False
# 检查字符串是否以数字和字母构成
print(str2.isalnum())  # True
str3 = '  jackfrued@126.com '
print(str3)
# 获得字符串修剪左右两侧空格之后的拷贝
print(str3.strip())
```
格式化输出字符串
```py
a, b = 5, 10
print('%d * %d = %d' % (a, b, a * b))
# 用字符串提供的方法来完成字符串的格式
print('{0} * {1} = {2}'.format(a, b, a * b))
# 可以使用下面的语法糖来简化上面的代码
print(f'{a} * {b} = {a * b}')
```
### 列表
数值类型是标量类型，也就是说这种类型的对象没有可以访问的内部结构；而字符串类型是一种结构化的、非标量类型，所以才会有一系列的属性和方法。

列表（list），也是一种结构化的、非标量类型，它是值的有序序列，每个值都可以通过索引进行标识，定义列表可以将列表的元素放在`[]`中，多个元素用,进行分隔，可以使用for循环对列表元素进行遍历，也可以使用`[]或[:]`运算符取出列表中的一个或多个元素。

```py
list1 = [1, 3, 5, 7, 100]
print(list1) # [1, 3, 5, 7, 100]
# 乘号表示列表元素的重复
list2 = ['hello'] * 3
print(list2) # ['hello', 'hello', 'hello']
# 计算列表长度(元素个数)
print(len(list1)) # 5
# 下标(索引)运算
print(list1[0]) # 1
print(list1[4]) # 100
# print(list1[5])  # IndexError: list index out of range
print(list1[-1]) # 100
print(list1[-3]) # 5
list1[2] = 300
print(list1) # [1, 3, 300, 7, 100]
# 通过循环用下标遍历列表元素
for index in range(len(list1)):
    print(list1[index])
# 通过for循环遍历列表元素
for elem in list1:
    print(elem)
# 通过enumerate函数处理列表之后再遍历可以同时获得元素索引和值
for index, elem in enumerate(list1):
    print(index, elem)
```

向列表中添加元素以及如何从列表中移除元素

```py
list1 = [1, 3, 5, 7, 100]
# 添加元素
list1.append(200)
list1.insert(1, 400)
# 合并两个列表
# list1.extend([1000, 2000])
list1 += [1000, 2000]
print(list1) # [1, 400, 3, 5, 7, 100, 200, 1000, 2000]
print(len(list1)) # 9
# 先通过成员运算判断元素是否在列表中，如果存在就删除该元素
if 3 in list1:
	list1.remove(3)
if 1234 in list1:
    list1.remove(1234)
print(list1) # [1, 400, 5, 7, 100, 200, 1000, 2000]
# 从指定的位置删除元素
list1.pop(0)
list1.pop(len(list1) - 1)
print(list1) # [400, 5, 7, 100, 200, 1000]
# 清空列表元素
list1.clear()
print(list1) # []
```
和字符串一样，列表也可以做切片操作，通过切片操作我们可以实现对列表的复制或者将列表中的一部分取出来创建出新的列表，代码如下所示。

```py
fruits = ['grape', 'apple', 'strawberry', 'waxberry']
fruits += ['pitaya', 'pear', 'mango']
# 列表切片
fruits2 = fruits[1:4]
print(fruits2) # apple strawberry waxberry
# 可以通过完整切片操作来复制列表
fruits3 = fruits[:]
print(fruits3) # ['grape', 'apple', 'strawberry', 'waxberry', 'pitaya', 'pear', 'mango']
fruits4 = fruits[-3:-1]
print(fruits4) # ['pitaya', 'pear']
# 可以通过反向切片操作来获得倒转后的列表的拷贝
fruits5 = fruits[::-1]
print(fruits5) # ['mango', 'pear', 'pitaya', 'waxberry', 'strawberry', 'apple', 'grape']
```

对列表的排序操作 

```py
list1 = ['orange', 'apple', 'zoo', 'internationalization', 'blueberry']
list2 = sorted(list1)
# sorted函数返回列表排序后的拷贝不会修改传入的列表
# 函数的设计就应该像sorted函数一样尽可能不产生副作用
list3 = sorted(list1, reverse=True)
# 通过key关键字参数指定根据字符串长度进行排序而不是默认的字母表顺序
list4 = sorted(list1, key=len)
print(list1)
print(list2)
print(list3)
print(list4)
# 给列表对象发出排序消息直接在列表对象上进行排序
list1.sort(reverse=True)
print(list1)
```
### 生成式和生成器
使用列表的生成式语法来创建列表
```py
f = [x for x in range(1, 10)]
print(f)
f = [x + y for x in 'ABCDE' for y in '1234567']
print(f)
# 用列表的生成表达式语法创建列表容器
# 用这种语法创建列表之后元素已经准备就绪所以需要耗费较多的内存空间
f = [x ** 2 for x in range(1, 1000)]
print(sys.getsizeof(f))  # 查看对象占用内存的字节数
print(f)
# 请注意下面的代码创建的不是一个列表而是一个生成器对象
# 通过生成器可以获取到数据但它不占用额外的空间存储数据
# 每次需要数据的时候就通过内部的运算得到数据(需要花费额外的时间)
f = (x ** 2 for x in range(1, 1000))
print(sys.getsizeof(f))  # 相比生成式生成器不占用存储数据的空间
print(f)
for val in f:
    print(val)
```
除了生成器语法，还有另外一种定义生成器的方式，通过yield关键字将一个普通函数改造成生成器函数。

```py
# 斐波那契数列
def fib(n):
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
        yield a

def main():
    for val in fib(20):
        print(val)

if __name__ == '__main__':
    main()
```
### 使用元组
Python中元组与列表类似也是一种容器数据类型，可以用一个变量(对象)来存储多个数据，不同之处在于元组的元素不能修改。
```py
# 定义元组
t = ('骆昊', 38, True, '四川成都')
print(t)
# 获取元组中的元素
print(t[0])
print(t[3])
# 遍历元组中的值
for member in t:
    print(member)
# 重新给元组赋值
# t[0] = '王大锤'  # TypeError
# 变量t重新引用了新的元组原来的元组将被垃圾回收
t = ('王大锤', 20, True, '云南昆明')
print(t)
# 将元组转换成列表
person = list(t)
print(person)
# 列表是可以修改它的元素的
person[0] = '李小龙'
person[1] = 25
print(person)
# 将列表转换成元组
fruits_list = ['apple', 'banana', 'orange']
fruits_tuple = tuple(fruits_list)
print(fruits_tuple)
```
已经有了列表这种数据结构，为什么还需要元组这样的类型呢？
1. 元组中的元素是无法修改的，事实上我们在项目中尤其是多线程环境（后面会讲到）中可能更喜欢使用的是那些不变对象（一方面因为对象状态不能修改，所以可以避免由此引起的不必要的程序错误，简单的说就是一个不变的对象要比可变的对象更加容易维护；另一方面因为没有任何一个线程能够修改不变对象的内部状态，一个不变对象自动就是线程安全的，这样就可以省掉处理同步化的开销。一个不变对象可以方便的被共享访问）。所以结论就是：如果不需要对元素进行添加、删除、修改的时候，可以考虑使用元组，当然如果一个方法要返回多个值，使用元组也是不错的选择。
2. 元组在创建时间和占用的空间上面都优于列表。

### 使用集合
Python中的集合跟数学上的集合是一致的，不允许有重复元素，而且可以进行交集并集差集等运算。
可以用下面代码所示的方式来创建和使用集合。
```py
# 创建集合的字面量语法
set1 = {1, 2, 3, 3, 3, 2}
print(set1)
print('Length =', len(set1))
# 创建集合的构造器语法(面向对象部分会进行详细讲解)
set2 = set(range(1, 10))
set3 = set((1, 2, 3, 3, 2, 1))
print(set2, set3)
# 创建集合的推导式语法(推导式也可以用于推导集合)
set4 = {num for num in range(1, 100) if num % 3 == 0 or num % 5 == 0}
print(set4)
```

向集合添加元素和从集合删除元素

```py
set1.add(4)
set1.add(5)
set2.update([11, 12])
set2.discard(5)
if 4 in set2:
    set2.remove(4)
print(set1, set2)
print(set3.pop())
print(set3)
```

集合的成员、交集、并集、差集等运算。

```py
# 集合的交集、并集、差集、对称差运算
print(set1 & set2)
# print(set1.intersection(set2))
print(set1 | set2)
# print(set1.union(set2))
print(set1 - set2)
# print(set1.difference(set2))
print(set1 ^ set2)
# print(set1.symmetric_difference(set2))
# 判断子集和超集
print(set2 <= set1)
# print(set2.issubset(set1))
print(set3 <= set1)
# print(set3.issubset(set1))
print(set1 >= set2)
# print(set1.issuperset(set2))
print(set1 >= set3)
# print(set1.issuperset(set3))
```

### 使用字典
字典是另一种可变容器模型，它可以存储任意类型对象，与列表、集合不同的是，字典的每个元素都是一个键和一个值组成的键值对，键和值通过冒号分开。
```py
# 创建字典的字面量语法
scores = {'骆昊': 95, '白元芳': 78, '狄仁杰': 82}
print(scores)
# 创建字典的构造器语法
items1 = dict(one=1, two=2, three=3, four=4)
# 通过zip函数将两个序列压成字典
items2 = dict(zip(['a', 'b', 'c'], '123'))
# 创建字典的推导式语法
items3 = {num: num ** 2 for num in range(1, 10)}
print(items1, items2, items3)
# 通过键可以获取字典中对应的值
print(scores['骆昊'])
print(scores['狄仁杰'])
# 对字典中所有键值对进行遍历
for key in scores:
    print(f'{key}: {scores[key]}')
# 更新字典中的元素
scores['白元芳'] = 65
scores['诸葛王朗'] = 71
scores.update(冷面=67, 方启鹤=85)
print(scores)
if '武则天' in scores:
    print(scores['武则天'])
print(scores.get('武则天'))
# get方法也是通过键获取对应的值但是可以设置默认值
print(scores.get('武则天', 60))
# 删除字典中的元素
print(scores.popitem())
print(scores.pop('骆昊', 100))
# 清空字典
scores.clear()
print(scores)
```

## 练习
### 屏幕上显示跑马灯文字
```py
import os
import time

def main():
    content = '世界这么大，我想去看看。。。'
    while True:
        # 清理屏幕输出
        os.system('cls')
        print(content)
        time.sleep(0.2)
        content = content[1:] + content[0]

if __name__ == '__main__':
    main()

```
### 设计一个函数产生指定长度的验证码，验证码由大小写字母和数字构成
```py
import random

def generate_code(code_len=4):
    '''
    :param code_len:验证码长度(默认4个字符)
    '''
    all_chars = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'
    last_pos = len(all_chars) - 1
    code = ''
    for _ in range(code_len)
        index = random.randint(0,last_pos)
        code += all_chars[index]
    return code
```

### 双色球选号
```py
from random import randrange, randint, sample

def display(balls):
    '''
    输出类表中的双色球号码
    '''
    for index, ball in enumerate(balls):
        if index == len(balls) - 1:
            print('|',end= ' ')
        print('%02d'% ball, end=' ')
    print()

def random_select():
    '''
    随机选择一组号码
    '''
    red_balls = [x for x in range(1, 34)]
    selected_balls = []
    selected_balls = sample(red_balls, 6)
    selected_balls.sort()
    selected_balls.append(randint(1,16))
    return selected_balls

def main():
    n = int(input('机选几注'))
    for _ in range(n):
        display(random_select())
    
if __name__ == '__main__':
    main()

# random模块的sample函数来实现从列表中选择不重复的n个元素
```

### 约瑟夫环问题


```py
def main():
    person = [True] * 30
    counter, index, number = 0, 0, 0
    while counter <15:
        if person[index]:
            number += 1
            if nummber == 9:
                persons[index] = False
                counter += 1
                number = 0
        index += 1
        index %= 30
    for person in persons:
        print('基' if person else '非', end='')

if __name__ == '__main__':
    main()

```

### 井字棋游戏

```py
import os

def print_board(board):
    print(board['TL'] + '|' + board['TM'] + board['TR'])
    print('-+-+-')
    print(board['ML'] + '|' + board['MM'] + '|' + board['MR'])
    print('-+-+-')
    print(board['BL'] + '|' + board['BM'] + '|' + board['BR'])

def main():
    init_board = {
        'TL': ' ', 'TM': ' ', 'TR': ' ',
        'ML': ' ', 'MM': ' ', 'MR': ' ',
        'BL': ' ', 'BM': ' ', 'BR': ' '
    }
    begin = True
    while begin:
        curr_board = init_board.copy()
        begin = False
        turn = 'x'
        counter = 0
        os.system('clear')
        print_board(curr_board)
        while counter < 9:
            move = input('轮到%s走棋，请输入位置：' % turn)
            if curr_board[move] == ' ':
                counter += 1
                curr_board[move] = turn
                if turn == 'x':
                    turn = 'o'
                else:
                    turn = 'x'
            os.system('clear')
            print_board(curr_board)
        choice = input('再玩一局?(yes|no)')
        begin = choice == 'yes'

if __name__ == '__main__':
    main()

```

# 面向对象编程基础

>把一组数据结构和处理它们的方法组成对象（object），把相同行为的对象归纳为类（class），通过类的封装（encapsulation）隐藏内部细节，通过继承（inheritance）实现类的特化（specialization）和泛化（generalization），通过多态（polymorphism）实现基于对象类型的动态分派

## 类和对象

简单的说，类是对象的蓝图和模板，而对象是类的实例。这个解释虽然像用概念解释概念，但是可以看出，类是抽象的概念，而对象是具体的东西。在面向对象编程的世界中，一切皆为对象，对象都有属性和行为，每个对象都是独一无二的，而对象一定属于某个类。当把一大堆拥有共同特征的对象的静态特征(属性)和动态特征(行为)都抽取出来后，就可以定义出一个叫做类的东西。

### 定义类
在Python中使用`Class`关键字定义类，然后在类中通过之前学习的函数来定义方法，就可以将对象的动态特征描述出来：

```py
class Student(object):
    # __init__是一个特殊方法用于在创建对象时进行初始化操作
    # 通过这个方法为学生对象绑定name和age两个属性
    def __init__(slef,name,age):
        self.name = name
        self.age = age
    
    def study(self,course_name):
        print('%s正在学习%s.' %(slef.name,course_name))

    def wathc_movie(self):
        if self.age < 14:
            print('%s在看动画片' %self.name)
        else:
            print('%s在看美剧' %self.name)

# 写在类中的函数，通常称之为对象的方法，这些方法就是对象可以接收的消息
```

### 创建和使用对象

定义好一个类以后，可以通过下面的方式来创建对象并给对象发消息

```py
def main():
    stu1 = Student('小明',16)
    stu1.study('Python程序设计')
    stu1.watch_movie()
    stu2 = Student('小红', 15)
    stu2.study('前端苦狗')
    stu2.watch_movie()

if __name__ == '__main__':
    main()

```

### 访问可见性问题
在很多面向对象的编程语言中，通常将对象的属性设置为私有的private或受保护的protected，简单说就是不允许外界访问， 而对象的方法通常都是公开的public。在Python中，属性和方法的访问权限只有两种，公开的和私有的，如果希望属性时私有的，给属性命名时可以用两个下划线作为开头：

```py
class Test:

    def __init__(self, foo):
        self.__foo = foo

    def __bar(self):
        print(self.__foo)
        print('__bar')


def main():
    test = Test('hello')
    # AttributeError: 'Test' object has no attribute '__bar'
    test.__bar()
    # AttributeError: 'Test' object has no attribute '__foo'
    print(test.__foo)


if __name__ == "__main__":
    main()
```

python并没有从语法上严格保证私有属性或方法的私密性，它只是给私有的属性和方法换了名字来妨碍对它们的访问，事实上更换名字的规则仍然可以访问到它们。

```py
class Test:

    def __init__(self, foo):
        self.__foo = foo

    def __bar(self):
        print(self.__foo)
        print('__bar')


def main():
    test = Test('hello')
    test._Test__bar()
    print(test._Test__foo)


if __name__ == "__main__":
    main()
```
实际开发中，并不建议将属性设置为私有，会导致子类无法访问。所以大多数会遵循一种命名惯例就是让属性名以单下划线开头来表示属性时受保护的，本类之外的代码在访问这样的属性应该要保持慎重

### 面向对象的支柱

面向对象有三大支柱：封装、继承和多态。封装可以理解为：隐藏一切可以隐藏的实现细节，只向外界暴露简单的编程接口。在类中定义的方法其实就是把数据和对数据的操作封装起来，创建对象之后，只需要给对象发送一个消息就可以执行方法中的代码。

```py
# 定义一个类描述数字时钟

from time import sleep

class Clock(object):
    '''数字时钟'''
    def __init__(slef, hour=0, minute=0, second=0):
        self._hour = hour
        self._minute = minute
        self._second = second
    
    def run(self):
        self._second += 1
        if self._second == 60:
            self._second == 1
            self._minute += 1
            if self._minute == 60:
                self._minute = 0
                self._hour += 1
                if self._hour == 24:
                    self._hour = 0
    
    def show(self):
        '''显示时间'''
        return '%02d:%02d:%02d' %(self._hour, self._minute, self._second)

def main():
    clock = Clock(16, 48, 30)
    while True:
        print(clock.shwo())
        sleep(1)
        clock.run()

if __name__ == '__main__':
    main()

```

## 面向对象进阶

### @property装饰器

之前讨论过Python属性和方法访问权限的问题，虽然不建议将属性设置为私有，但是如果直接将属性暴露给外界也是有问题的，比如没有办法检查赋给属性的值是否有效。之前的建议是将属性命名以单下划线开头，通过这种方式暗示属性是受保护的，不建议外界直接访问，那么如果想访问属性，可以通过属性的getter访问器和setter修改器方法进行对应的操作。这样可以考虑使用@property包装器来包装getter和setter方法，使得对属性的访问安全又方便

```py
class Person(object):
    def __init__(self, name, age):
        self._name = name
        self._age = age
    
    # 访问器 -getter方法
    @property
    def name(self):
        return self._name
    @property
    def age(self):
        return self._age
    
    # 修改器 - setter方法
    @age.setter
    def age(self, age):
        self._age = age
    
    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋.' % self._name)
        else:
            print('%s正在玩斗地主.' % self._name)

def main():
    person = Person('小明', 18)
    person.play()
    person.age = 12
    person.play()
    # person.name = '白元芳'  # AttributeError: can't set attribute


if __name__ == '__main__':
    main()
```

### __slots__ 魔法

Python是一门动态语言，通常，动态语言允许在程序运行时给对象绑定新的属性或方法，也可以对已经绑定的属性和方法进行解绑定。但是如果需要限定自定义类型的对象只能绑定某些属性，可以通过类中定义__slots__变量来进行限定。需要注意的是__slots__的限定只对当前类的对象生效，对子类并不起任何作用。

```py
class Person(object):
    # 限定person对象只能绑定_name, _age, _gender属性
    __slots__ = ('_name','_age','_gender')

    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def name(self):
        return self._name

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, age):
        self._age = age

    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋.' % self._name)
        else:
            print('%s正在玩斗地主.' % self._name)


def main():
    person = Person('王大锤', 22)
    person.play()
    person._gender = '男'
    # AttributeError: 'Person' object has no attribute '_is_gay'
    # person._is_gay = True

```

### 静态方法和类方法

之前，在类中定义的方法都是对象方法，也就是说这些方法都是发给对象的消息。实际上，写在类中的方法并不需要都是对象方法，例如定义一个三角形类，通过传入三条边来构造三角形，并提供计算周长和面积的方法，但是传入的三条边长未必能构造出三角形对象，因此可以先写一个方法来验证三条边长是否可以构成三角形，这个方法很显然不是对象方法，在调用这个方法时，三角形对象尚未创建出来，所以这个方法属于三角形类而并不属于三角形对象。可以使用静态方法来解决这个问题

```py
from math import sqrt

class Triangle(object):

    def __init__(self, a, b, c):
        self._a = a
        self._b = b
        self._c = c
    
    @staticmethod
    def is_valid(a, b, c):
        return a+b>c and b+c>a and a+c>b
    
    def perimeter(self):
        return self._a + self._b + self_.c
    
    def area(self):
        half = self.perimeter() / 2
        return sqrt(half * (half - self._a) * 
                    (half - self._b) * (half- self._c))

def main(a, b, c):
    # 静态方法和类方法都是通过给类发消息来调用的
    if Triangle.is_valid(a, b, c):
        t = Triangle(a, b, c)
        print(t.perimeter())
        # 也可以通过给类发消息来调用对象方法但是要传入接收消息的对象作为参数
        # print(Triangle.perimeter(t))
        print(t.area())
        # print(Triangle.area(t))
    else:
        print('无法构成三角形.')

if __name__ == '__main__':
    main(4,5,6)
```

和静态方法比较类似，Python还可以在类中定义类方法，类方法的第一个参数约定名为cls，它代表的是当前类相关的信息的对象，通过这个参数可以获取和类相关的信息并且可以创建出类的对象

```py
from time import time, localtime, sleep

class Clock(object):
    def __init__(self, hour=0, minute=0, second=0):
        self._hour = hour
        self.minute = minute
        self._second = second

    @classmethod
    def now(cls):
        ctime = localtime(time())
        return cls(ctime.tm_hour, ctime.tm_min, ctime.tm_sec)

    def run(self):
        """走字"""
        self._second += 1
        if self._second == 60:
            self._second = 0
            self._minute += 1
            if self._minute == 60:
                self._minute = 0
                self._hour += 1
                if self._hour == 24:
                    self._hour = 0

    def show(self):
        """显示时间"""
        return '%02d:%02d:%02d' % \
               (self._hour, self._minute, self._second)


def main():
    # 通过类方法创建对象并获取系统时间
    clock = Clock.now()
    while True:
        print(clock.show())
        sleep(1)
        clock.run()


if __name__ == '__main__':
    main()
```

### 类之间的关系

简单的说，类和类之间的关系有三种：is-a、has-a和use-a关系。

- is-a关系也叫继承或泛化，比如学生和人的关系、手机和电子产品的关系都属于继承关系。
- has-a关系通常称之为关联，比如部门和员工的关系，汽车和引擎的关系都属于关联关系；关联关系如果是整体和部分的关联，那么我们称之为聚合关系；如果整体进一步负责了部分的生命周期（整体和部分是不可分割的，同时同在也同时消亡），那么这种就是最强的关联关系，我们称之为合成关系。
- use-a关系通常称之为依赖，比如司机有一个驾驶的行为（方法），其中（的参数）使用到了汽车，那么司机和汽车的关系就是依赖关系。

利用类之间的关系，可以在已有类的基础上完成某些操作，也可以在已有类的基础上创建新的类，这些都是代码复用的重要手段。复用现有的代码不仅可以减少开发的工作量，也有利于代码的管理和维护。

### 继承和多态

在已有类的基础上创建新类，其中的一个做法就是直接继承下来，从而减少重复代码的编写。提供继承信息的称之为父类，也叫超类或基类；得到继承信息的称之为子类，也叫派生类或衍生类。子类除了继承父类提供的属性和方法，还可以定义特有的属性和方法，所以子类比父类拥有更多的能力。实际开发中，经常用子类对象替换掉一个父类对象，这是面向对象编程中一个常见的行为。

```py
class Person(object):
    """人"""

    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def name(self):
        return self._name

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, age):
        self._age = age

    def play(self):
        print('%s正在愉快的玩耍.' % self._name)

    def watch_av(self):
        if self._age >= 18:
            print('%s正在观看爱情动作片.' % self._name)
        else:
            print('%s只能观看《熊出没》.' % self._name)

class Student(Person):
    def __init__(self, name, age, grade):
        super().__init__(name, age)
        self._grade = grade

    @property
    def grade(self, grade):
        self._grade = grade

    def study(self, course):
        print('%s的%s正在学习%s.' % (self._grade, self._name, course))

class Teacher(Person):
    """老师"""

    def __init__(self, name, age, title):
        super().__init__(name, age)
        self._title = title

    @property
    def title(self):
        return self._title

    @title.setter
    def title(self, title):
        self._title = title

    def teach(self, course):
        print('%s%s正在讲%s.' % (self._name, self._title, course))


def main():
    stu = Student('王大锤', 15, '初三')
    stu.study('数学')
    stu.watch_av()
    t = Teacher('骆昊', 38, '砖家')
    t.teach('Python程序设计')
    t.watch_av()


if __name__ == '__main__':
    main()

```

子类在继承父类的方法后，可以对父类已有的方法给出新的实现版本，这称之为重写override，通过方法重写可以让父类的同一个行为在子类中拥有不同的实现版本，当调用子类重写的方法时，不同的子类对象会表现出不同的行为，这个就是多态poly-morphism

```py
from abc import ABCMeta, abstractmethod


class Pet(object, metaclass=ABCMeta):
    """宠物"""

    def __init__(self, nickname):
        self._nickname = nickname

    @abstractmethod
    def make_voice(self):
        """发出声音"""
        pass


class Dog(Pet):
    """狗"""

    def make_voice(self):
        print('%s: 汪汪汪...' % self._nickname)


class Cat(Pet):
    """猫"""

    def make_voice(self):
        print('%s: 喵...喵...' % self._nickname)


def main():
    pets = [Dog('旺财'), Cat('凯蒂'), Dog('大黄')]
    for pet in pets:
        pet.make_voice()


if __name__ == '__main__':
    main()

```
上面的代码中，pet类处理成了一个抽象类，所谓抽象类就是不能够创建对象的类，这种类的存在就是专门为了让其他类去继承它。Python没有从语法层面提供对抽象类的支持，但是可以通过abc模块的ABCMeta元类和abcstractmethod包装器来达到抽象类的效果，如果一个类中存在抽象方法，那么这个类就不能勾实例化(创建对象)。上面的代码中，dog和cat两个子类分别对pet类中的make_voice抽象方法进行了重写并给出不同的实现版本，在main()函数调用该方法时，这个方法就表现出多态行为。

```py
from abc import ABCMeta, abstractmethod
from random import randint, randrange

class Fighter(object, metaclass=ABCMeta):
    # 通过__slots__魔法限定对象可以绑定的成员变量
    __slots__ = ('_name', '_hp')

    def __init__(self, name, hp):
        self._name = name
        self._hp = hp

    @property
    def name(self):
        return self._name
    
    @property
    def hp(self):
        return self._hp
    
    @hp.setter
    def hp(self, hp):
        self._hp = hp if hp>=0 else 0
    
    @property
    def alive(self):
        return self._hp > 0
    
    @abstractmethod
    def attack(self, other):
        pass
    
class Ultraman(Fighter):
    __slots__ = ('_name', '_hp', '_mp')

    def __init__(self, name, hp, mp):
        """初始化方法

        :param name: 名字
        :param hp: 生命值
        :param mp: 魔法值
        """
        super().__init__(name, hp)
        self._mp = mp

    def attack(self, other):
        other.hp -= randint(15, 25)

    def huge_attack(self, other):
        """究极必杀技(打掉对方至少50点或四分之三的血)

        :param other: 被攻击的对象

        :return: 使用成功返回True否则返回False
        """
        if self._mp >= 50:
            self._mp -= 50
            injury = other.hp * 3 // 4
            injury = injury if injury >= 50 else 50
            other.hp -= injury
            return True
        else:
            self.attack(other)
            return False
    
    def magic_attack(self, others):
        """魔法攻击

        :param others: 被攻击的群体

        :return: 使用魔法成功返回True否则返回False
        """
        if self._mp >= 20:
            self._mp -= 20
            for temp in others:
                if temp.alive:
                    temp.hp -= randint(10, 15)
            return True
        else:
            return False

    def resume(self):
        """恢复魔法值"""
        incr_point = randint(1, 10)
        self._mp += incr_point
        return incr_point

    def __str__(self):
        return '~~~%s奥特曼~~~\n' % self._name + \
            '生命值: %d\n' % self._hp + \
            '魔法值: %d\n' % self._mp


class Monster(Fighter):
    """小怪兽"""

    __slots__ = ('_name', '_hp')

    def attack(self, other):
        other.hp -= randint(10, 20)

    def __str__(self):
        return '~~~%s小怪兽~~~\n' % self._name + \
            '生命值: %d\n' % self._hp


def is_any_alive(monsters):
    """判断有没有小怪兽是活着的"""
    for monster in monsters:
        if monster.alive > 0:
            return True
    return False


def select_alive_one(monsters):
    """选中一只活着的小怪兽"""
    monsters_len = len(monsters)
    while True:
        index = randrange(monsters_len)
        monster = monsters[index]
        if monster.alive > 0:
            return monster


def display_info(ultraman, monsters):
    """显示奥特曼和小怪兽的信息"""
    print(ultraman)
    for monster in monsters:
        print(monster, end='')


def main():
    u = Ultraman('骆昊', 1000, 120)
    m1 = Monster('狄仁杰', 250)
    m2 = Monster('白元芳', 500)
    m3 = Monster('王大锤', 750)
    ms = [m1, m2, m3]
    fight_round = 1
    while u.alive and is_any_alive(ms):
        print('========第%02d回合========' % fight_round)
        m = select_alive_one(ms)  # 选中一只小怪兽
        skill = randint(1, 10)   # 通过随机数选择使用哪种技能
        if skill <= 6:  # 60%的概率使用普通攻击
            print('%s使用普通攻击打了%s.' % (u.name, m.name))
            u.attack(m)
            print('%s的魔法值恢复了%d点.' % (u.name, u.resume()))
        elif skill <= 9:  # 30%的概率使用魔法攻击(可能因魔法值不足而失败)
            if u.magic_attack(ms):
                print('%s使用了魔法攻击.' % u.name)
            else:
                print('%s使用魔法失败.' % u.name)
        else:  # 10%的概率使用究极必杀技(如果魔法值不足则使用普通攻击)
            if u.huge_attack(m):
                print('%s使用究极必杀技虐了%s.' % (u.name, m.name))
            else:
                print('%s使用普通攻击打了%s.' % (u.name, m.name))
                print('%s的魔法值恢复了%d点.' % (u.name, u.resume()))
        if m.alive > 0:  # 如果选中的小怪兽没有死就回击奥特曼
            print('%s回击了%s.' % (m.name, u.name))
            m.attack(u)
        display_info(u, ms)  # 每个回合结束后显示奥特曼和小怪兽的信息
        fight_round += 1
    print('\n========战斗结束!========\n')
    if u.alive > 0:
        print('%s奥特曼胜利!' % u.name)
    else:
        print('小怪兽胜利!')

if __name__ == '__main__':
    main()
```

