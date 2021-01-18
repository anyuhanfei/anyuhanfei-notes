前情提示: 测试代码中，右尖括号（`>`）表示命令行中输入的命令； 单独一行并以井字符（`#`）开头的为输出内容； 库的导入仅在本文的第一个测试代码中展现，其他代码块均省略库的导入代码。

- **系统类型**: `Windows 10`
- **python 版本**: `Python 3.9.0`

`fractions` 模块支持分数运算。

使用 `fractions` 模块需要先实例化 `Fraction` 类, 实例化 `Fraction` 类 可以由多种类型的参数构建.

```
class fractions.Fraction(string)
class fractions.Fraction(decimal)
class fractions.Fraction(float)
class fractions.Fraction(other_fraction)
class fractions.Fraction(numerator=0, denominator=1)
```
直接上栗子:

```python
import fractions
import decimal
import math


'''传入一个字符串'''
obj1 = fractions.Fraction('2.1')
print(obj1)
# 21/10

'''传入一个 decimal 类型的浮点数'''
obj2 = fractions.Fraction(decimal.Decimal('2.1'))
print(obj2)
# 21/10

'''传入一个浮点数'''
obj3 = fractions.Fraction(2.1)
print(obj3)
# 4728779608739021/2251799813685248

'''传入另一个 Fraction 实例化对象'''
obj4 = fractions.Fraction(obj3)
print(obj4)
# 4728779608739021/2251799813685248

'''分别传入分子, 分母'''
obj5 = fractions.Fraction(210, 100)
print(obj5)
# 21/10
```

通过上面的例子, 可以很清晰的知道 `Fraction` 实例化时可以传什么参数, 但是 `class fractions.Fraction(numerator=0, denominator=1)` 这个实例化方法就需要稍微解释解释了, 文档上说, `numerator` 和 `denominator` 是 `numbers.Rational` 的实例, 那 `numbers.Rational` 又是个啥? `numerator` 意思是分子, `denominator` 意思是分母, 并且在 `python 3.9` 版本后, 这两个参数就只能传 `int` 类型的数据了, 那么其实就可以理解为传两个 `int` 类型的参数.

```python
obj = fractions.Fraction(2.2, 100)
print(obj)
# Traceback (most recent call last):
#   File "e:\project\test\test.py", line 3, in <module>
#     obj = fractions.Fraction(2.2, 100)
#   File "C:\Users\Administrator\AppData\Local\Programs\Python\Python39\lib\fractions.py", line 152, in __new__
#     raise TypeError("both arguments should be "
# TypeError: both arguments should be Rational instances
```

`Fraction` 实例化对象是可哈希的，并应当被视为不可变对象。

```
obj = fractions.Fraction(210, 100)
print(hash(obj))
# 2075258708292324558
```

上文例子中, `obj4` 实例化对象是传入 `obj3` 实例化对象后得到的, 那么它们的 `id` 是否相同呢?

```python
obj3 = fractions.Fraction(2.1)
obj4 = fractions.Fraction(obj3)
print(id(obj3))
# 2774800535456
print(id(obj4))
# 2774800535264
```

通过测试, 可看出它们是相互独立的, 互不影响.

###### as_integer_ratio()

得到由分子, 分母组成的元组数据

```
参数:
    无
返回值:
    tuple, (分子, 分母)
```

在实例化 `Fraction` 时, 实例化对象中的分子和分母已经自动更改为最简分数形式了, 并且如果分母是负数时, 会更改到分子上. 此函数的功能仅仅是获取最简化分数形式后的分子和分母.

```python
obj = fractions.Fraction(2, -100)
print(obj)  # 已经是最简分数形式了
# -1/50
print(obj.as_integer_ratio())
# (-1, 50)
```

###### from_float(flt)

生成一个 `Fraction` 实例化对象表示参数 `flt` 的精确值.

```
参数:
    flt: 浮点数
返回值:
    facations 实例化对象
```

传入浮点数类型的参数, 可生成一个 `Fraction` 实例化对象, 这个对象表示的值是此浮点数的精确值.

```python
'''使用方法一: 在一个实例化对象中使用, 会生成另一个实例化对象, 不会对旧实例化对象产生影响'''
obj = fractions.Fraction(2, -100)
print(obj)
# -1/50
obj2 = obj.from_float(0.1)
print(obj)
# -1/50
print(obj2)
# 3602879701896397/36028797018963968

'''使用方法二: 直接使用'''
obj3 = fractions.Fraction.from_float(0.1)
print(obj3)
# 3602879701896397/36028797018963968
```

为什么 `0.1` 生产的分子, 分母的数字会这么大呢? 一切还要从二进制存储浮点数的机制开始说起, emmmmmm............ 

小编并不打算详细说明, 有兴趣的小伙伴可以自己百度一下, 总之, 一些特殊的浮点数在二进制中能正常的存储, 但是向上文例子中的浮点数就只能存储到一个有限近似的值. 如:

```python
obj = fractions.Fraction.from_float(0.125)
print(obj)
# 1/8
```

###### from_decimal(dec)

生成一个 `Fraction` 实例化对象表示参数 `flt` 的精确值.

```
参数:
    dec: decimal.Decimal 实例化对象
返回值:
    facations 实例化对象
```

与 `from_float()` 函数相似, 不同的是, 此函数的参数必须是 `decimal.Decimal` 实例化对象(其实就是更精确的 `float` 类型对象)

```python
'''使用方法一: 在一个实例化对象中使用, 会生成另一个实例化对象, 不会对旧实例化对象产生影响'''
obj = fractions.Fraction(2, -100)
print(obj)
# -1/50
obj2 = obj.from_decimal(decimal.Decimal(0.1))
print(obj)
# -1/50
print(obj2)
# 3602879701896397/36028797018963968

'''使用方法二: 直接使用'''
obj3 = fractions.Fraction.from_decimal(decimal.Decimal(0.1))
print(obj3)
# 3602879701896397/36028797018963968
```

###### limit_denominator(max_denominator=1000000)

控制分母的最大值

```
参数:
    max_denominator: int, 分母的最大值, 默认为 1000000
返回值:
    facations 实例化对象
```

用于 `Fraction` 实例化对象上, 可得到一个新的 `Fraction` 实例化对象.

```python
obj3 = fractions.Fraction.from_decimal(decimal.Decimal(0.11))
print(obj3)
# 7926335344172073/72057594037927936
obj4 = obj3.limit_denominator()
print(obj4)
# 11/100
print(type(obj4))
# <class 'fractions.Fraction'>
```

###### 魔法方法

`facations` 模块还内置了一些魔法方法, 是为了可以让那些指定函数可以接收 `Fraction` 实例化对象参数.

- `__floor__()`, 通过 `math.floor()` 函数使用, 返回整数部分
- `__ceil__()`, 通过 `math.ceil()` 函数使用, 进一取整
- `__round__()`, 通过 `round()` 函数使用, 四舍五入

```python
obj1 = fractions.Fraction(0.11)
obj2 = fractions.Fraction(0.61)
'''未处理时'''
print(obj1)
# 7926335344172073/72057594037927936
print(obj2)
# 5494391545392005/9007199254740992
'''舍去小数部分'''
print(math.floor(obj1))
# 0
print(math.floor(obj2))
# 0
'''进一取整'''
print(math.ceil(obj1))
# 1
print(math.ceil(obj2))
# 1
'''四舍五入'''
print(round(obj1))
# 0
print(round(obj2))
# 1
```

#### 参考资料

**源代码**: Lib/fractions.py

**官方文档**: https://docs.python.org/zh-cn/3/library/fractions.html