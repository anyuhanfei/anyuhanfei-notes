前情提示: 测试代码中，右尖括号（`>`）表示命令行中输入的命令； 单独一行并以井字符（`#`）开头的为输出内容； 库的导入仅在本文的第一个测试代码中展现，其他代码块均省略库的导入代码。

- **系统类型**: `Windows 10`
- **python 版本**: `Python 3.9.0`

`array` 模块定义了一种对象类型，可以紧凑的表示以 `字符`、`整数`、`浮点数` 等基本类型为元素组成的数组。`array` 模块中定义的数组属于序列类型，其行为也与列表类型非常相似，但是数组中的元素的数据类型是受到限制的，只能设置在初始化时指定的某一种类型。

**++PS: 数组与列表、元组等组合数据类型相似，可以由多个值组成。出于个人习惯，小编把这些值称为元素，也就是说，列表、元组等可以是由多个元素组成的，在本文中也将组成数组的那些值也称为元素。++**

`array` 模块在使用时，需要先实例化一个数组对象，然后再进行下一步操作:

```python
array.array(typecode[, initializer])
'''
参数:
    typecode: 指定当前数组所能接受的元素数据类型
    initializer: 可选参数, 数组初始化时添加的元素, 必须是可迭代对象, 元素的数据类型受参数 typecode 的限制
'''
```

`typecode` 参数的值是一个字符，这个字符被称为**类型码**，其代表一种类型限制，所有的类型码可以使用 `array.typecodes` 查看:

```python
import array

print(array.typecodes)
# bBuhHiIlLqQfd
```

在上述的例子中，返回值中的每个字符都是一个类型码，那么这些类型码都是什么意思呢?

```
类型码        C 类型        Python 类型     以字节表示的最小尺寸

 'b'       signed char          int                 1

 'B'      unsigned char         int                 1

 'u'         wchar_t         Unicode 字符           2

 'h'       signed short         int                 2

 'H'      unsigned short        int                 2

 'i'        signed int          int                 2

 'I'       unsigned int         int                 2

 'l'        signed long         int                 4

 'L'       unsigned long        int                 4

 'q'      signed long long      int                 8

 'Q'     unsigned long long     int                 8

 'f'          float            float                4

 'd'          double           float                8
```

接下来就可以初始化一个数组了:

```python
test = array.array('u', 'ABC')
print(test)
# array('u', 'ABC')
```

初始化的元素类型一定要和设置的类型码一致，否则将报错:

```python
test = array.array('b', 'ABC')
# TypeError: cannot use a str to initialize an array with typecode 'b'
```

`array` 模块的大多数内容都在初始化后的数组对象上展开的，那么下面将根据功能进行分组介绍。

#### 属性

- `array.typecode`: 获取数组的类型码
- `array.itemsize`: 获取在内部表示中一个元素的字节长度

```python
test = array.array('u', 'ABC')
print(test.typecode)
# u
print(test.itemsize)
# 2
```

#### 添加

添加功能比较统一的一点就是都没有返回值，直接作用于数组本身。

- **`array.append(x)`**

将一个值为 `x` 的新元素添加到数组的末尾。参数 `x` 必须是一个符合类型码的值。

- **`array.extend(iterable)`**

将来自 `iterable` 可迭代对象中的元素添加到数组的末尾。`iterable` 可迭代对象中所有元素都符合类型码。

- **`array.fromlist(list)`**

将 `list` 中的元素添加到数组末尾。`list` 中所有元素都符合类型码。

- **`array.fromunicode(s)`**

将 `Unicode` 字符串添加到数组末尾，数组的类型码必须是 `u`，否则将发生 `ValueError` 错误。

- **`array.insert(i, x)`**

将参数 `x` 插入到在数组中索引为 `i` 的位置。若指定参数 `i` 为负数，则将元素 `x` 插入到数组末尾。参数 `x` 必须是一个符合类型码的值。


```python
test = array.array('u', 'A')

'''append()'''
test.append('B')
print(test)
# array('u', 'AB')

'''extend()'''
test.extend(['C', 'D'])
print(test)
# array('u', 'ABCD')

'''fromlist()'''
test.fromlist(['E', 'F'])
print(test)
# array('u', 'ABCDEF')

'''fromunicode()'''
test.fromunicode('IJ')
print(test)
# array('u', 'ABCDEFIJ')

'''insert()'''
test.insert(6, 'H')
print(test)
# array('u', 'ABCDEFHIJ')
```

#### 统计 and 获取
- **`array.count(x)`**

获取值为 `x` 的元素的个数。参数 `x` 必须是一个符合类型码的值。

- **`array.index(x)`**

获取第一个值为 `x` 的元素所在的位置，也就是索引值。参数 `x` 必须是一个符合类型码的值。

- **`array.pop([i])`**

将指定索引值的元素从数组中移除并返回，默认移除并返回数组中的最后一个元素。

```python
test = array.array('u', 'ABCABC')

'''count()'''
print(test.count('A'))
# 2

'''index()'''
print(test.index('B'))
# 1

'''pop()'''
print(test.pop())
# C
print(test)
# array('u', 'ABCAB')
```

#### 机器值、文件相关

- **`array.frombytes(s)`**

将二进制字符串解读后加入数组末尾。

- **`array.fromfile(f, n)`**

将从文件对象 `f` 中读取 `n` 个元素添加到数组末尾。如果可读数据少于参数 `n`，那么将报 `EOFError` 错误，但是有效的元素仍然会添加到数组中。参数 `f` 必须是内置文件对象。

- **`array.tobytes()`**

将数组转换成机器值并返回。

- **`array.tofile(f)`**

将数组转换成机器值并写入到文件中。参数 `f` 必须是内置文件对象。

- **`array.byteswap()`**

将数组中的所有元素字节对调，此方法仅支持大小为 `1`、`2`、`4`、`8` 字节的值，其他的值将报 `RuntimeError` 错误。

```python
test = array.array('u', 'ABC')
'''tobytes()'''
print(test.tobytes())
# b'A\x00B\x00C\x00'

'''tofile()'''
with open('./test', 'wb+') as f:
    test.tofile(f)
    f.seek(0)                 # 指针移动到文件起始位置
    print(f.read())           # 读取文件内容
# b'A\x00B\x00C\x00'

'''frombytes()'''
test.frombytes(b'A\x00B\x00C\x00')
print(test)
# array('u', 'ABCABC')

'''fromfile()'''
with open('./test', 'rb+') as f:
    test.fromfile(f, 3)
print(test)
# array('u', 'ABCABCABC')

'''byteswap()'''
test = array.array('u', 'ABC')
test.byteswap()
print(test)
# array('u', '䄀䈀䌀')
```

#### 转换

- **`array.tolist()`**

将数组转换成列表.

- **`array.tounicode()`**

将数组转换成 `Unicode` 字符串，数组的类型码为 `u`，否则将报 `ValueError` 错误。

```python
test = array.array('u', 'ABC')
'''tolist()'''
print(test.tolist())
# ['A', 'B', 'C']

'''tounicode()'''
print(test.tounicode())
# ABC
```

#### 其他
- **`array.buffer_info()`**

返回一个元组 `(address, length)` 以给出用于存放数组内容的缓冲区元素的当前内存地址和长度。

- **`array.remove(x)`**

从数组中移除第一个值为 `x` 的元素。参数 `x` 必须是一个符合类型码的值。

- **`array.reverse()`**

反转数组内元素的顺序。

```python
test = array.array('u', 'ABCABC')

'''buffer_info()'''
print(test.buffer_info())
# (1864606516176, 6)

'''remove()'''
test.remove('C')
print(test)
# array('u', 'ABABC')

'''reverse()'''
test.reverse()
print(test)
# array('u', 'CBABA')
```

#### 参考资料

**官方文档**: https://docs.python.org/zh-cn/3/library/array.html

公众号 : 「python库详解」，专注于 python 标准库与 python 第三方库的解析教程，以及其他 python 的相关内容。发掘更多原创文章，期待您的关注。