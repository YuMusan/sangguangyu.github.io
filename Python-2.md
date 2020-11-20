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
