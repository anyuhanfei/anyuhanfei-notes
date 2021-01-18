前情提示: 测试代码中，右尖括号（`>`）表示命令行中输入的命令； 单独一行并以井字符（`#`）开头的为输出内容； 库的导入仅在本文的第一个测试代码中展现，其他代码块均省略库的导入代码。

- **系统类型**: `Windows 10`
- **python 版本**: `Python 3.9.0`

枚举是一组符号名称（枚举成员）的集合，枚举成员应该是唯一的、不可变的。

`enum` 模块将分为三个部分解析，第一部分主要介绍枚举的特性和 `Enum` 类，第二部分将介绍 `Enum` 类的三个变种类及自定义类，第三部分将更深入的了解枚举。

###### 传送门

[enum -- 枚举(一)](http://mp.weixin.qq.com/s?__biz=MzkyMDE3ODA3NQ==&mid=2247483699&idx=1&sn=7db5a2c6fa3c524039117fed6863f683&chksm=c1979dc7f6e014d197de749a21cd94ad85aa4419baf0c032f9e04441d5600cd9dcf151f136c1&scene=21#wechat_redirect)

[enum -- 枚举(二)](http://mp.weixin.qq.com/s?__biz=MzkyMDE3ODA3NQ==&mid=2247483703&idx=1&sn=8fa62060cbf115fcd6a334e9ac1f7549&chksm=c1979dc3f6e014d58e7244d9dbcc0774a7cbd9f60559c61e3587bcbc0fc47e72c59c729e5c83&scene=21#wechat_redirect)

[enum -- 枚举(三)](https://mp.weixin.qq.com/s/g1cmqs9DVBnX4zrSmOSATA)

#### 创建一个 Enum

枚举是使用 `class` 语法来创建的，这使得它们易于读写。但还是有一种以使用函数形式的创建方式，这个留到下文再讲，现在主要来说明一下使用 `class` 语法的创建方式。

```python
import enum

class Test(enum.Enum):
    A = 1
    B = 'b'
```

上面的代码创建了一个枚举，`class Test` 是一个`枚举`，其中 `Test` 就是枚举对象的名称。

`A = 1`、`B = 'b'` 这些等号两边的整体被称为 **枚举成员**，等号左边的被称为枚举成员的名称，枚举通常是用来表示常量的，所以枚举成员的名称一般使用大写形式。等号右边的被称为枚举成员的值，枚举成员的值可以是任意类型，如 `int`、`str`等等，只需要注意枚举成员的值在 枚举对象中是唯一的。

虽然创建枚举时使用了 `class` 语法，但是枚举与普通的 `python` 类不同，可以打印一些信息来进行对比:

```python
'''枚举'''
class Test(enum.Enum):
    A = 1

print(Test)                      # 打印枚举
# <enum 'Test'>
print(Test.A)                    # 打印枚举成员本身
# Test.A
print(repr(Test.A))              # 打印枚举成员在解释器中的形式
# <Test.A: 1>
print(type(Test.A))              # 打印枚举成员类型
# <enum 'Test'>
print(isinstance(Test.A, Test))  # 判断枚举成员类型是否有枚举类型一致
# True

'''普通的 python 类'''
class Common:
    A = 1

print(Common)                    # 打印类
# <class '__main__.Common'>
print(Common.A)                  # 打印类属性的值
# 1
print(repr(Common.A))            # 打印类属性的值在解释器中的形式
# 1
print(type(Common.A))            # 打印类属性的值的类型
# <class 'int'>
print(isinstance(Common.A, Test))  # 判断类属性的值的类型是否有类的类型一致
# False
```

首先，枚举创建出的对象自成一种类型，类型名就是枚举的名称(类名)。枚举的枚举成员(类的属性)被创建成了一个对象，并且类型就是所属枚举的类型。

枚举可以按照定义顺序进行迭代，并且枚举成员是不可变的，是可哈希的，因此字典中的键可以使用枚举成员。

```python
class Test(enum.Enum):
    A = 1

test_dict = {}
for i in Test:
    test_dict[i] = 'value.' + str(i)
print(test_dict)
# {<Test.A: 1>: 'value.Test.A', <Test.B: 'b'>: 'value.Test.B', <Test.C: 'c'>: 'value.Test.C'}
print(test_dict[Test.A])
# value.Test.A
```

那么怎么获取枚举成员呢，在枚举中有很多种访问方式:

```python
class Test(enum.Enum):
    A = 1

print(Test(1))       # 程序化访问
# Test.A
print(Test['A'])     # 条目访问
# Test.A
print(Test.A.value)  # 枚举成员的 value 属性
# 1
print(Test.A.name)   # 枚举成员的 name 属性
# A
```

枚举中不允许有相同的枚举成员，但是允许枚举成员有同样的值，若查找的值有多个枚举成员，那么会按定义顺序返回第一个。

```python
'''枚举成员有相同的名称'''
class Test(enum.Enum):
    A = 1
    A = 2
# TypeError: Attempted to reuse key: 'A'

'''枚举成员有相同的值'''
class Test(enum.Enum):
    A = 1
    B = 1

print(Test(1))
# Test.A
```

`enum` 模块中定义了一个装饰器 -- `@enum.unique`，可以强制规定枚举成员的值也必须是唯一的。

```python
@enum.unique
class Test(enum.Enum):
    A = 1
    B = 1
# ValueError: duplicate values found in <enum 'Test'>: B -> A
```

若枚举成员的值不重要或者其他不需要一一指定枚举成员的场景下，可以使用 `enum.auto()` 方法来替代输入的值。

```python
class Test(enum.Enum):
    A = enum.auto()
    B = enum.auto()
    C = 1

print(repr(Test.A))
# <Test.A: 1>
print(repr(Test.B))
# <Test.B: 2>
print(repr(Test.C))
# <Test.A: 1>
print(list(Test))
# [<Test.A: 1>, <Test.B: 2>]
```

当使用 `enum.auto()` 来定义枚举成员的值时，再次使用 `int` 类型的值时，最后得到的结果可能会和预想的不太一样。

`enum.auto()` 函数的返回值是由 `_generate_next_value_()` 函数决定的，默认情况下，此函数是根据最后一个 `int` 类型的枚举成员的值增加 `1`。此函数是可以重载的，重载时，方法定义必须在任何成员之前。

```python
'''先定义一个值为 int 类型的枚举成员, 再使用 auto() 函数'''
class Test(enum.Enum):
    A = 2
    B = enum.auto()
    C = enum.auto()
print(list(Test))
# [<Test.A: 2>, <Test.B: 3>, <Test.C: 4>]

'''定义一个值为非 int 类型的枚举成员, 再使用 auto() 函数'''
class Test(enum.Enum):
    A = 'b'
    B = enum.auto()
    C = enum.auto()
print(list(Test))
# [<Test.A: 'b'>, <Test.B: 1>, <Test.C: 2>]

'''重载 _generate_next_value_()'''
class Test(enum.Enum):
    def _generate_next_value_(name, start, count, last_values):
        return name  # 返回枚举成员的名字

    A = enum.auto()
    B = enum.auto()

print(list(Test))
# [<Test.A: 'A'>, <Test.B: 'B'>]
```

在介绍 `enum.auto()` 函数时，测试示例中，枚举对象转换为 `list` 类型时少了一个枚举成员，这是因为少的那个枚举成员与之前的枚举成员的值是一样的，这个枚举成员被认为是一个别名，当对枚举成员迭代时，不会给出别名。

```python
class Test(enum.Enum):
    A = 1
    B = 1
print(list(Test))
# [<Test.A: 1>]
```

而特殊属性 `__members__`是一个从名称到成员的只读有序映射。它包含枚举中所有的枚举成员，但是在迭代时，因为别名的原因，指向的枚举成员还是该值第一个定义的枚举成员。

```python
class Test(enum.Enum):
    A = 1
    B = 1
print(dict(Test.__members__))
# {'A': <Test.A: 1>, 'B': <Test.A: 1>}
```

小栗子，找出枚举中所有的别名:

```python
class Test(enum.Enum):
    A = 1
    B = 1
    C = 2
    D = 2
print([名称 for 名称, 枚举成员 in Test.__members__.items() if 枚举成员.name != 名称])
# ['B', 'D']
```

在枚举中，不仅仅只可以定义一些枚举成员，枚举也属于 `python` 的类，是可以拥有普通方法和特殊方法的，这里列举一个文档上的示例:

```python
class Mood(enum.Enum):
    FUNKY = 1
    HAPPY = 3

    def describe(self):
        return self.name, self.value

    def __str__(self):
        return 'my custom str! {0}'.format(self.value)

    @classmethod
    def favorite_mood(cls):
        return cls.HAPPY

print(Mood.favorite_mood())
# my custom str! 3
print(Mood.HAPPY.describe())
# ('HAPPY', 3)
print(str(Mood.FUNKY))
# my custom str! 1
```

但是枚举还是有一些限制，以单下划线开头和结尾的名称是由枚举保留而不可使用，例外项是包括特殊方法成员 (`__str__()`、`__add__()` 等)，描述符 (方法也属于描述符) 以及在 `_ignore_` 中列出的变量名。

在介绍 `_generate_next_value_()` 函数时，为了重载这个函数，我们创建了一个基于 `enum.Enum` 的枚举，可以成为 `枚举A`，重载了 `_generate_next_value_()` 函数，然后又创建了一个基于 `枚举A` 的枚举。这样的定义是被允许的，在 `enum` 模块的规定下，一个新的 `Enum` 类必须基于一个 `Enum` 类，至多一个实体数据类型以及出于实际需要的任意多个基于 `object` 的 `mixin` 类。 这些基类的顺序为:

```
class EnumName([mix-in, ...,] [data-type,] base-enum):
    pass
```

另外，没有定义枚举成员的枚举才能被其他枚举继承，否则将会报错:

```python
class A(enum.Enum):
    AA = 1
class B(A):
    BB = 2
# TypeError: B: cannot extend enumeration 'A'
```

#### 参考资料

**官方文档**: https://docs.python.org/zh-cn/3/library/enum.html

**源代码**: Lib/enum.py
