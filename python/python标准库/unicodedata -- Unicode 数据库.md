前情提示: 测试代码中，右尖括号（`>`）表示命令行中输入的命令； 单独一行并以井字符（`#`）开头的为输出内容； 库的导入仅在本文的第一个测试代码中展现，其他代码块均省略库的导入代码。

- **系统类型**: `Windows 10`
- **python 版本**: `Python 3.9.0`

此模块提供了对 `Unicode Character Database` (`UCD`) 的访问。`UCD` 中定义了所有 `unicode` 字符的字符属性。

###### unicodedata.lookup(name)

通过 `unicode` 字符的名称来查找 `unicode` 字符。

```
参数:
    name: unicode 字符的名称
返回值:
    str，unicode 字符
```

根据传入的名称，来查找对应的 `unicode` 字符，如果查找到则返回 `unicode` 字符，否则会引发 `KeyError` 错误。

```python
import unicodedata

print(unicodedata.lookup('Cjk Compatibility Ideograph-2f80f'))
# 兔
print(unicodedata.lookup('Armenian Small Ligature Men Now'))
# ﬓ
print(unicodedata.lookup('111'))
# Traceback (most recent call last):
#   File "e:\project\test\test.py", line 3, in <module>
#     print(unicodedata.lookup('111'))
# KeyError: "undefined character name '111'"
```

###### unicodedata.name(chr[, default])

获取 `unicode` 字符的名称。

```
参数:
    chr: str，字符
    default: str，可选参数，当没有查找到 unicode 字符的名称时的默认返回值
返回值:
    str，unicode 字符的名称或传入的默认值
```

根据传入的 `unicode` 字符，来查找对应的名称，如果查找到则返回名称，如果未查找到并且传入了默认值则返回默认值，如果未查找到并且没有传入默认值则会引发 `ValueError` 错误。

```python
print(unicodedata.name('a'))
# LATIN SMALL LETTER A
print(unicodedata.name('❋'))
# HEAVY EIGHT TEARDROP-SPOKED PROPELLER ASTERISK
print(unicodedata.name('✍'))
# WRITING HAND
```

PS: 不是小编不测试默认值和报错，是根本找不到不是 `unicode` 字符的字符。😂

###### '毫无用处'三兄弟?
```
unicodedata.decimal(chr[, default])
unicodedata.digit(chr[, default])
unicodedata.numeric(chr[, default])
```

得到表示数字字符的数值

```
参数:
    chr: str，数字字符
    default: str，可选参数，当没有查找到 unicode 字符的名称时的默认返回值
返回值:
    int，表示数字字符的数值
```

传入一个数字字符，然后返回一个表示数字字符的数值，如果传入参数不符合要求，那么将引发 `ValueError` 错误，当 `default` 参数有值时则返回 `default` 参数的值。

```
print(unicodedata.decimal('3'))
# 3
print(unicodedata.decimal('b', 'Error'))
# Error
print(unicodedata.decimal('b'))
# Traceback (most recent call last):
#   File "e:\project\test\test.py", line 3, in <module>
#     print(unicodedata.decimal('b'))
# ValueError: not a decimal
```

经过一番测试，小编发现仅仅能传入 `'0'`~`'9'` 这 `10` 个 `str` 类型的数字，其他的均会报错。也就是说，传入一个 `str` 类型的数字，返回一个 `int` 类型的数字，这就要吐槽一下了, 这功能有什么作用吗，`int()` 函数它不香吗? `unicodedata.numeric(chr[, default])` 函数也有异曲同工之妙，也是只能传一个 `str` 类型的数字，返回一个 `int` 类型的数字。不理解，真的不理解。

`unicodedata.numeric()` 函数就比前两个函数更强大一些，虽然也是传入一个 `str` 类型的字符，但前两个函数只能只能传 `'0'`~`'9'` 这 `10` 个 `str` 类型的数字，`unicodedata.numeric()` 函数可以中文简写数字，中文大写数字，罗马数字，甚至字符数字(①)都可以返回对应的结果。

```python
print(unicodedata.numeric('①'))
# 1.0
print(unicodedata.numeric('一'))
# 1.0
print(unicodedata.numeric('壹'))
# 1.0
print(unicodedata.numeric('Ⅰ'))
# 1.0
print(unicodedata.numeric('1'))
# 1.0
```

###### 分类小分队

```
unicodedata.category(chr)
unicodedata.bidirectional(chr)
unicodedata.east_asian_width(chr)
```

在 `unicode` 中，有多种分类标准，字符在各个分类标准中都有自己的的分类定位。以上三个函数是查询字符分别在三种分类标准下的分类名称。

```
参数:
    chr: str，字符
返回值:
    str，分类名称
```

`unicodedata.category(chr)` 可以获取指定字符在`常规分类`中所属的分类名称，`unicodedata.bidirectional(chr)` 可以获取指定字符的`双向字符类型名称`，`unicodedata.east_asian_width(chr)` 可以获取指定字符的`字符宽度分类`名称。

需要介绍介绍 `双向字符类型` 和 `字符宽度分类` 这两个概念，首先说说 `双向字符类型`，在 `unicode` 字符中，大部分字符是从左向右显示的字符，但有些字符是从右向左显示的字符，如阿拉伯语,希伯来语等，所以 `unicode` 加入了一个双向算法，最终的结果就是对每个字符又进行了一次双向字符的分类。

再聊聊 `字符宽度分类`，在 `unicode` 字符中，字符是有宽度概念的，也就是说字符展示的宽度是不一样的， 特别是东亚地区的文字字符。因此 `unicode` 将所有的宽度都分类出来，并将字符根据宽度分类。`east_asian_width()` 函数就是获取指定字符的宽度类型，函数名中指明了 `east asian` (东亚) 可能是只有东亚地区的字符有不同的宽度，为了这个地区的文字能正常使用才加入的这一概念。

高能预警，一大波测试代码袭来:

```python
'''获取字符的常规分类'''
print(unicodedata.category('①'))  # 符号
# No
print(unicodedata.category('壹'))  # 中文
# Lo
print(unicodedata.category('✔'))  # 符号
# So
'''获取字符的双向字符类型'''
print(unicodedata.bidirectional('ب'))  # 阿拉伯语中的一个字母
# AL
print(unicodedata.bidirectional('①'))
# ON
print(unicodedata.bidirectional('壹'))
# L
print(unicodedata.bidirectional('✔'))
# ON
print(unicodedata.bidirectional('1'))
# EN
'''获取字符的字符宽度分类'''
print(unicodedata.east_asian_width('①'))  # 符号
# A
print(unicodedata.east_asian_width('壹'))  # 中文
# W
print(unicodedata.east_asian_width('あ'))  # 日文
# W
print(unicodedata.east_asian_width('ب'))  # 阿拉伯语中的一个字母
# N
print(unicodedata.east_asian_width('1'))  # 数字
# Na
print(unicodedata.east_asian_width('A'))  # 英文字母
# A
```

看到测试代码，小伙伴们应该发现了，获取的分类名称都是缩写，这就更加不理解每个分类名称都是什么意思了，小伙伴们可以在 `unicode` 的文档中查找到相关的说明，网址已经列在了下方参考资料中。

###### unicodedata.combining(chr)

获取指定字符的规范组合值

```
参数:
    chr: str，字符
返回值:
    int，unicode 字符的规范组合值
```

返回指定字符的规范组合值，正规操作时，根据这个值由小到大进行排序，如果字符未定义规范组合值，那么将返回 `0`。

```python
print(unicodedata.combining('壹'))
# 0
print(unicodedata.combining('1'))
# 0
print(unicodedata.combining('A'))
# 0
```

###### unicodedata.mirrored(chr)

判断指定字符是不是镜像字符

```
参数:
    chr: str，字符
返回值:
    int，0 或 1
```

可以理解为指定字符在通常情况下是不是需要成对出现或与其他字符一同出现，如 `(` 和 `)`。如果是这样的字符，则返回 `1`，否则返回 `0`。

```python
print(unicodedata.mirrored('口'))
# 0
print(unicodedata.mirrored('1'))
# 0
print(unicodedata.mirrored('A'))
# 0
print(unicodedata.mirrored('/'))
# 0
print(unicodedata.mirrored('('))
# 1
print(unicodedata.mirrored('['))
# 1
print(unicodedata.mirrored('{'))
# 1
print(unicodedata.mirrored('<'))
# 1
print(unicodedata.mirrored('"'))
# 1
print(unicodedata.mirrored("'"))
# 1
```


###### unicodedata.decomposition(chr)

获取指定字符的分解映射

```
参数:
    chr: str，字符
返回值:
    str，类似 16 进制的字符串
```

获取指定字符的分解映射，如果没有则返回空值


```
print(unicodedata.decomposition('ﬁ'))
# <compat> 0066 0069
print(unicodedata.decomposition('আ'))
# 
print(unicodedata.decomposition('▇'))
# 
print(unicodedata.decomposition('ㄌ'))
# 
print(unicodedata.decomposition('⠟'))
# 
print(unicodedata.decomposition('𐊨'))
# 
```

###### unicodedata.is_normalized(form, unistr)

判断指定字符串是否是正规形式

```
参数:
    form: str，正规形式，有效值有: NFC、NFKC、NFD、NFKD
    unistr: str，字符串
返回值:
    bool
```

判断指定字符串是否是正规形式，如果是则返回 `True`，否则返回 `False`。

```python
print(unicodedata.is_normalized('NFC', 'āáǎàōóǒòēéěè'))
# True
print(unicodedata.is_normalized('NFD', 'āáǎàōóǒòēéěè'))
# False
```

###### unicodedata.normalize(form, unistr)

获取指定字符串的指定正规形式

```
参数:
    form: str，正规形式，有效值有: NFC、NFKC、NFD、NFKD
    unistr: str，字符串
返回值:
    str
```

将指定字符串转换为指定形式后返回，下面的例子中，先判断指定字符串不是 `NFD` 形式， 然后转换为 `NFD` 形式，再次判断转化后的字符串是否是 `NFD` 形式。以此来验证 `normalize()` 函数。

```python
temp_str = 'āáǎàōóǒòēéěè'
print(unicodedata.is_normalized('NFD', temp_str))  # 字符串不是 NFD 形式
# False
print(unicodedata.is_normalized('NFD', unicodedata.normalize('NFD', temp_str)))  # 将字符串转换为 NFD 然后再判断是否是 NFD 形式
# True
```


###### 属性两枚

**`unicodedata.unidata_version`** 可以查看当前引用的 `unicodedata` 模块使用的 `Unicode` 数据库的版本。

**`unicodedata.ucd_3_2_0`** 是一个与 `unicodedata` 模块有相同方法的对象，但是使用的 `Unicode` 数据库的版本是 `3.2.0`，在特殊情况下需要用到。

```python
print(unicodedata.unidata_version)
# 13.0.0
print(unicodedata.ucd_3_2_0.unidata_version)
# 3.2.0
print(unicodedata.ucd_3_2_0.name('a'))
# LATIN SMALL LETTER A
```

#### 参考资料

**官方文档**: https://docs.python.org/zh-cn/3/library/unicodedata.html

**unicode双向字符类型**: https://www.unicode.org/reports/tr9/

**unicode东亚宽度类型**: https://www.unicode.org/reports/tr11/