前情提示: 测试代码中，右尖括号（`>`）表示命令行中输入的命令； 单独一行并以井字符（`#`）开头的为输出内容； 库的导入仅在本文的第一个测试代码中展现，其他代码块均省略库的导入代码。

- **系统类型**: `Windows 10`
- **python 版本**: `Python 3.9.0`

上文中我们了解到模块中有 `Enum` 类，其实还有基于它的三个变种类，分别是 `IntEnum`、`Flag`、`IntFlag`。甚至还能自定义类来实现符合需求的特殊功能。

###### 传送门
[enum -- 枚举(一)](http://mp.weixin.qq.com/s?__biz=MzkyMDE3ODA3NQ==&mid=2247483699&idx=1&sn=7db5a2c6fa3c524039117fed6863f683&chksm=c1979dc7f6e014d197de749a21cd94ad85aa4419baf0c032f9e04441d5600cd9dcf151f136c1&scene=21#wechat_redirect)

[enum -- 枚举(二)](http://mp.weixin.qq.com/s?__biz=MzkyMDE3ODA3NQ==&mid=2247483703&idx=1&sn=8fa62060cbf115fcd6a334e9ac1f7549&chksm=c1979dc3f6e014d58e7244d9dbcc0774a7cbd9f60559c61e3587bcbc0fc47e72c59c729e5c83&scene=21#wechat_redirect)

[enum -- 枚举(三)](https://mp.weixin.qq.com/s/g1cmqs9DVBnX4zrSmOSATA)

###### IntEnum

`IntEnum` 类是 `Enum` 类的子类，也是 `int` 的子类。`IntEnum` 的枚举成员的值必须是 `int` 类型，填写其他类型会报错。

```python
class IntTest(enum.IntEnum):
    A = 1
    B = 'b'
# ValueError: invalid literal for int() with base 10: 'b'
```

除此之外，`IntEnum` 类和 `Enum` 类最大的区别在与比较时的差异，前文中并没有介绍 `Enum` 类比较功能的特性，这里先说一下 `Enum` 类的比较:

枚举成员只与自己本身和别名相等，枚举成员与非枚举值比较永远不相等，枚举成员只能无法进行排序比较。

```python
class TestA(enum.Enum):
    A = 1
    B = 1
class TestB(enum.Enum):
    A = 1
    B = 2

print(TestA.A is TestB.A)  # 不同枚举的同名同值的枚举成员的 is 比较
# False
print(TestA.A is TestA.B)  # 同一枚举的同值枚举成员 is 比较(别名)
# True
print(TestA.A == TestB.A)  # 不同枚举的同名同值的枚举成员的 == 比较
# False
print(TestA.A == TestA.B)  # 同一枚举的同值枚举成员 == 比较(别名)
print(TestA.A == 1)  # 与 int 类型比较
# False
print(TestA.A < TestA.B)  # 排序比较
# Traceback (most recent call last):
#   File "e:\project\test\test.py", line 15, in <module>
#     print(TestA.A < TestA.B)
# TypeError: '<' not supported between instances of 'TestA' and 'TestA'
```

`IntEnum` 的枚举成员可以直接与 `int` 类型的值比较，也支持排序比较，并且不同枚举的同名同值的枚举成员使用 `==` 比较时也相等。

```python
class IntTestA(enum.IntEnum):
    A = 1
    B = 1

class IntTestB(enum.IntEnum):
    A = 1
    B = 2

print(IntTestA.A is IntTestB.A)  # 不同枚举的同名同值的枚举成员的 is 比较
# False
print(IntTestA.A is IntTestA.B)  # 同一枚举的同值枚举成员 is 比较(别名)
# True
print(IntTestA.A == IntTestB.A)  # 不同枚举的同名同值的枚举成员的 == 比较
# True
print(IntTestA.A == IntTestA.B)  # 同一枚举的同值枚举成员 == 比较(别名)
# True
print(IntTestA.A == 1)  # 与 int 类型比较
# True
print(IntTestA.A < IntTestB.B)  # 排序比较
# True
```

`IntEnum` 在其他方面的行为类似于 `int` 类型的数据:

```python
class IntTestA(enum.IntEnum):
    A = 1
    B = 2

print(['a', 'b', 'c'][IntTestA.A])  # 枚举成员作为索引
# b
print([i for i in range(IntTestA.B)])  # 枚举成员作为 range() 函数的参数
# [0, 1]
```

###### Flag

`Flag` 类是 `Enum` 类的子类，`Flag` 类与 `Enum` 类的差别就是 `Flag` 枚举成员可以使用按位运算符(`&`、`|`、`^`、`~`)。

```python
class TestFlag(enum.Flag):
    A = 1
    B = 2
    C = A | B

print(list(TestFlag))  # 所有枚举成员
# [<TestFlag.A: 1>, <TestFlag.B: 2>, <TestFlag.C: 3>]

print(repr(TestFlag.A | TestFlag.B))  # 枚举成员A 与 B 按位或
# <TestFlag.C: 3>
print(repr(TestFlag.C))  # 枚举成员 C
# <TestFlag.C: 3>
print(1 | 2)  # 1 与 2 按位或
# 3
```

`Flag` 类的枚举成员可以在定义时使用按位运算符，也可以在使用枚举成员时使用按位运算符， 并且如果得到的值与枚举中的枚举成员的值相等时，最后得到的结果就是值与结果相同的枚举成员。那么如果在使用枚举成员时使用按位运算符，最后的结果在枚举中没有枚举成员的值与之相等呢?

```python
class TestFlag(enum.Flag):
    A = 1
    B = 2

print(repr(TestFlag.A | TestFlag.B))  # 枚举成员A 与 B 按位或
# <TestFlag.B|A: 3>
print(type(TestFlag.A | TestFlag.B))  # 查看结果的类型
# <enum 'TestFlag'>
```

`Flag` 类的枚举成员之间使用按位运算符之后， 如果枚举中有相同值的枚举成员时得到那个枚举成员，否则将获取一个新的枚举成员。

#### IntFlag

`IntFlag` 类是 `Flag` 与 `Int` 类的子类。其实，`IntFlag` 类更像是可以使用按位运算符的 `IntEnum`。但在某些方面 `IntFlag` 还是有自己独有的特点。

首先, `IntFlag` 的枚举成员的值只能是数字，这点与 `IntEnum` 类相同。`IntFlag` 的枚举成员在定义时可以使用按位运算符，在使用枚举成员时也可以使用按位运算符, 得到的结果还是枚举成员，这点与 `Flag` 类相同。

```python
class TestIntFlag(enum.IntFlag):
    A = 1
    B = 2
    C = A | B

print(repr(TestIntFlag.A | TestIntFlag.B))
# <TestIntFlag.C: 3>
```

因为继承了 `Int` 类，`IntFlag` 类的枚举成员也可以像 `IntEnum` 类的枚举成员那样进行运算，作索引值等等。但是，`IntFlag` 类的枚举成员进行除按位运算以外的其他运算都将导致失去 `IntFlag` 类的枚举成员的资格。

```python
class TestIntFlag(enum.IntFlag):
    A = 1
    B = 2

print(TestIntFlag.A + TestIntFlag.B)  # 加法运算
# 3
print(['a', 'b', 'c', 'd'][TestIntFlag.A])  # 索引值
# b
```

#### 自定义类

当以上枚举类都无法满足需求时，可以修改枚举类中的魔法方法来改变某些特性或功能。

`__new__()` 魔法方法在每个枚举成员定义前调用，可以修改枚举成员的值和设置自定义属性。

```
'''修改枚举成员定义的值'''
class Test(enum.Enum):
    A = 'aaa'
    B = 'bbb'

    def __new__(cls, value):
        obj = object.__new__(cls)
        obj._value_ = str(value) + '...'
        return obj

print(list(Test))
# [<Test.A: 'aaa...'>, <Test.B: 'bbb...'>]
```

`__init__()` 魔法方法在枚举成员定义后调用，可实现的功能与 `__new__()` 魔法方法基本一致。这里官方文档中标明出了一点，虽然 `__init__()` 魔法方法可以定义枚举成员的值，但是不允许使用 `__init__()` 魔法方法定义枚举成员的值。 

```
'''被官方文档禁止的操作, 修改枚举成员的值'''
class Test(enum.Enum):
    A = 'aaa'
    B = 'bbb'

    def __init__(self, pantone='unknown'):
        self._value_ = str(self.value) + '...'

print(list(Test))
# [<Test.A: 'aaa...'>, <Test.B: 'bbb...'>]
```

`__repr__()` 魔法方法的重定义，用来修改使用 `repr()` 函数时返回的内容:

```python
'''使用 repr() 函数时, 不展示枚举成员的值'''
class TestA(enum.Enum):
    A = enum.auto()
    B = enum.auto()

    def __repr__(self):
        return '<%s.%s>' % (self.__class__.__name__, self.name)


print(repr(TestA.A))
# <TestA.A>
print(TestA.A.value)  # 正常获取枚举成员的值
# 1
```

同 `__repr__()` 魔法方法类似，`__str__()`、`__add__()`、`__format__()`、`__bool__()` 等等魔法方法都是可以重新定义的。

下面是官方文档中的两个有趣的小例子:

```python
'''一个有序枚举，它不是基于 IntEnum，因此保持了正常的 Enum 不变特性'''
class OrderedEnum(enum.Enum):
    def __ge__(self, other):
        if self.__class__ is other.__class__:
            return self.value >= other.value
        return NotImplemented

    def __gt__(self, other):
        if self.__class__ is other.__class__:
            return self.value > other.value
        return NotImplemented

    def __le__(self, other):
        if self.__class__ is other.__class__:
            return self.value <= other.value
        return NotImplemented

    def __lt__(self, other):
        if self.__class__ is other.__class__:
            return self.value < other.value
        return NotImplemented

class Grade(OrderedEnum):
    A = 5
    B = 4
    C = 3
    D = 2
    F = 1

print(Grade.C < Grade.A)
# True


'''如果发现重复的成员名称则将引发错误而不是创建别名'''
class DuplicateFreeEnum(enum.Enum):
    def __init__(self, *args):
        cls = self.__class__
        if any(self.value == e.value for e in cls):
            a = self.name
            e = cls(self.value).name
            raise ValueError(
                "aliases not allowed in DuplicateFreeEnum:  %r --> %r"
                % (a, e))

class Color(DuplicateFreeEnum):
    RED = 1
    GREEN = 2
    BLUE = 3
    GRENE = 2
# ValueError: aliases not allowed in DuplicateFreeEnum:  'GRENE' --> 'GREEN'
```

#### 参考资料

**官方文档**: https://docs.python.org/zh-cn/3/library/enum.html

**源代码**: Lib/enum.py
