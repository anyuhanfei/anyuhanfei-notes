前情提示: 测试代码中，右尖括号（`>`）表示命令行中输入的命令； 单独一行并以井字符（`#`）开头的为输出内容； 库的导入仅在本文的第一个测试代码中展现，其他代码块均省略库的导入代码。

- **系统类型**: `Windows 10`
- **python 版本**: `Python 3.9.0`

前两篇文章详解了枚举模块中的四个枚举类和自定义枚举类及其相关内容，没看过的小伙伴可以通过传送门点击进入前两篇文章阅读。枚举模块的最后一篇来更加深入的了解一下比较深入并且不常用的一些知识点。

###### 传送门
[enum -- 枚举(一)](http://mp.weixin.qq.com/s?__biz=MzkyMDE3ODA3NQ==&mid=2247483699&idx=1&sn=7db5a2c6fa3c524039117fed6863f683&chksm=c1979dc7f6e014d197de749a21cd94ad85aa4419baf0c032f9e04441d5600cd9dcf151f136c1&scene=21#wechat_redirect)

[enum -- 枚举(二)](http://mp.weixin.qq.com/s?__biz=MzkyMDE3ODA3NQ==&mid=2247483703&idx=1&sn=8fa62060cbf115fcd6a334e9ac1f7549&chksm=c1979dc3f6e014d58e7244d9dbcc0774a7cbd9f60559c61e3587bcbc0fc47e72c59c729e5c83&scene=21#wechat_redirect)

[enum -- 枚举(三)](https://mp.weixin.qq.com/s/g1cmqs9DVBnX4zrSmOSATA)

###### 不重要的枚举成员的值

在某些场景中，人们并不关心枚举成员的值是什么，可以使用下列几种方法定义枚举:

- 使用 `enum.auto()` 函数作为枚举成员的值
- 使用 `object()` 作为枚举成员的值
- 使用描述性的字符作为枚举成员的值，同时做到了可进一步解释此枚举成员
- 使用空元组作为值，并在自定义的 `__new__()` 中重新定义枚举成员的值(只要不重复即可)

看完以上的列举，这种实现这种需求并不局限于上面几种方法，只要最终枚举成员的值不重复即可，比如自定义一个随机生成字符串的函数。

```python
import enum
import pickle  # 封存


'''enum.auto()'''
class TestA(enum.Enum):
    A = enum.auto()
    B = enum.auto()

'''object()'''
class TestB(enum.Enum):
    A = object()
    B = object()

'''描述字符串'''
class TestC(enum.Enum):
    A = '字母A'
    B = '字母B'

'''元组, 并自定义 __new__()'''
class TestD(enum.Enum):
    A = ()
    B = ()

    def __new__(cls, *args):
        '''获取当前枚举成员数并加一, 将结果赋值给新定义的枚举成员'''
        value = len(cls.__members__) + 1
        obj = object.__new__(cls)  # 必须创建一个枚举成员对象
        obj._value_ = value
        return obj  # 必须返回一个枚举成员对象

print(list(TestA))
# [<TestA.A: 1>, <TestA.B: 2>]
print(list(TestB))
# [<TestB.A: <object object at 0x0000023986178210>>, <TestB.B: <object object at 0x0000023986178220>>]
print(list(TestC))
# [<TestC.A: '字母A'>, <TestC.B: '字母B'>]
print(list(TestD))
# [<TestD.A: 1>, <TestD.B: 2>]
```

###### 封存

枚举可以被封存和解封:

```
class Test(enum.Enum):
    A = enum.auto()
    B = enum.auto()

封存的枚举成员A = pickle.dumps(Test.A)
解封的枚举成员A = pickle.loads(封存的枚举成员A)

print(封存的枚举成员A)
# b'\x80\x04\x95\x1b\x00\x00\x00\x00\x00\x00\x00\x8c\x08__main__\x94\x8c\x04Test\x94\x93\x94K\x01\x85\x94R\x94.'
print(解封的枚举成员A)
# Test.A
print(Test.A is 解封的枚举成员A)  # 解封后的数据与原数据是否一致
# True
```

###### 功能性API

`Enum` 类属于可调用对象，也就是说 `Enum` 类可以像函数一样被调用:

```
Enum(value='NewEnumName', names=<...>, *, module='...', qualname='...', type=<mixed-in class>, start=1)

参数:
    value: str, 枚举的名称
    names: 枚举成员们的名称, 可使用多种方式设置枚举成员
    module: 关键字参数, str 或 None, 指明所在的模块
    qualname: 关键字参数, str, 指明所在的模块中的具体位置
    type: 关键字参数, 指明枚举类的类型
    start: 关键字参数, int, 枚举成员的起始值, 默认为 1
返回值:
    枚举
```

`names` 参数可以传入一个以空格或逗号隔开的字符串，分隔的子字符串就是枚举成员的名称，各枚举成员的值按顺序自增，起始值由 `start` 参数设置，起始值默认为 `1`。`names` 参数也可以传入迭代器或映射。

```python
'''以空格分隔'''
TestA = enum.Enum('TestEnum', 'A B C D')
print(list(TestA))
# [<TestEnum.A: 1>, <TestEnum.B: 2>, <TestEnum.C: 3>, <TestEnum.D: 4>]

'''以空格分隔, 设置起始值为10'''
TestB = enum.Enum('TestEnum', 'A B C D', start=10)
print(list(TestB))
# [<TestEnum.A: 10>, <TestEnum.B: 11>, <TestEnum.C: 12>, <TestEnum.D: 13>]

'''传入列表设置枚举成员, 枚举成员的值按顺序自增, 起始值由 start 参数设置'''
TestC = enum.Enum('TestEnum', ['A', 'B', 'C', 'D'])
print(list(TestC))
# [<TestEnum.A: 1>, <TestEnum.B: 2>, <TestEnum.C: 3>, <TestEnum.D: 4>]

'''传入列表, 并且元素格式为 ('名称', '值')'''
TestD = enum.Enum('TestEnum', [('A', 11), ('B', 22), ('C', 33), ('D', 44)])
print(list(TestD))
# [<TestEnum.A: 11>, <TestEnum.B: 22>, <TestEnum.C: 33>, <TestEnum.D: 44>]

'''传入映射, 格式为 {名称: 值}'''
TestE = enum.Enum('TestEnum', {'A': 11, 'B': 22, 'C': 33, 'D': 44})
print(list(TestE))
# [<TestEnum.A: 11>, <TestEnum.B: 22>, <TestEnum.C: 33>, <TestEnum.D: 44>]
```

`module` 参数需要传入一个模块，为了在进行封存操作时成功执行。若指定一个错误的模块，那么封存操作将报错。若参数值若设置为 `None` 或 `__name__` 最终指向的模块均为当前文件 `__main__`。

```python
'''module 参数默认为 None'''
TestA = enum.Enum('TestA', 'AAA BBB CCC')
print(pickle.loads(pickle.dumps(TestA)))  # 封存+解封
# <enum 'TestA'>

'''module 参数设置为 __name__'''
TestB = enum.Enum('TestB', 'AAA BBB CCC', module=__name__)
print(pickle.loads(pickle.dumps(TestB)))
# <enum 'TestB'>

'''module 参数设置为一个不存在的模块'''
TestD = enum.Enum('TestD', 'AAA BBB CCC', module='aaa')
print(pickle.loads(pickle.dumps(TestD)))
# _pickle.PicklingError: Can't pickle <enum 'TestD'>: import of module 'aaa' failed
```
`qualname` 参数是指明在模块中的具体位置，同 `module` 参数类似，设置这个参数的目的也是为了封存操作，`pickle` 协议版本 `4` 在某些情况下需要依赖设置的具体位置。


###### 布尔值

枚举类的布尔值总是 `True`，枚举中的枚举成员的布尔值也总是 `True`。 枚举成员的布尔值与枚举成员的值无关，如果想要以枚举成员的值来计算布尔值，可以修改 `__bool__()` 魔法方法。

```python
'''不修改 __bool__() 魔法方法的枚举类'''
class Test(enum.Enum):
    A = 0
    B = None

print(bool(Test))
# True
print(bool(Test.A))
# True
print(bool(Test.B))
# True

'''修改 __bool__() 魔法方法的枚举类'''
class Test(enum.Enum):
    A = 0

print(bool(Test.A))
# False
```

###### 支持枚举类使用的属性

`__members__` 是一个 `member_name:member` 条目的只读有序映射。得到的结果是以 `{名称: 枚举成员对象}` 为结构的字典。

```python
class Test(enum.Enum):
    A = 1
    B = 2
    C = 1

print(Test.__members__)
# {'A': <Test.A: 1>, 'B': <Test.B: 2>, 'C': <Test.A: 1>}
```

###### 支持枚举成员使用的属性

- `_name_` 和 `_value_` 分别返回枚举成员的名称和值;
- `_generate_next_value_` 在功能性API中被使用，为枚举成员获取适当的值，也可被打印;
- `_missing_`作用是当未发现某个值时所使用的查找函数;
- `_ignore_` 是一个名称列表，结果可以是 `list`，也可以是 `str`，不会转化为枚举成员，并将从最终类中被移除;
- `_order_` 是 `Python2` 的遗留属性，在 `Python2` 中负责记录枚举成员的定义顺序;

```python
'''可直接打印的属性'''
class Test(enum.Enum):
    A = 1
    B = 2
    C = 1

print(Test.A._name_)
# 'A'
print(Test.A._value_)
# 1
print(Test.A._generate_next_value_)
# <bound method Enum._generate_next_value_ of <Test.A: 1>>

'''_ignore_的用法'''
class Period(enum.Enum):
    _ignore_ = 'Period i'
    Period = vars()
    for i in range(3):
        Period['obj_%d' % i] = i

print(list(Period))
# [<Period.obj_0: 0>, <Period.obj_1: 1>, <Period.obj_2: 2>]
```

#### 参考资料

**官方文档**: https://docs.python.org/zh-cn/3/library/enum.html

**源代码**: Lib/enum.py
