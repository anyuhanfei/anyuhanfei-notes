前情提示: 测试代码中，右尖括号（`>`）表示命令行中输入的命令； 单独一行并以井字符（`#`）开头的为输出内容； 库的导入仅在本文的第一个测试代码中展现，其他代码块均省略库的导入代码。

- **系统类型**: `Windows 10`
- **python 版本**: `Python 3.9.0`

`textwrap` 可以将文本字符串按照一定的规则换行，或在对文本字符串进行一些简单的处理。 

`textwrap` 模块提供了一些快捷函数，通常情况下这些快捷函数就足够满足需求，如果无与需求相符的快捷函数，还可以自定义 `TextWrapper` 类来满足需求。


#### 快捷函数

###### textwrap.wrap(text, width=70, **kwargs)

自动换行并以列表类型返回

```
参数:
    text: str，要处理的字符串
    width: int，默认为 70，每行字符最大数量
    **kwargs: 可选参数，与 TextWrapper 实例中的参数相同
返回值:
    list[str, str, str, ...]，包含一个或多个 str 类型子元素的列表
```

对 `text` 以每行 `width` 个字符自动换行，所有行组成列表返回，行尾不带换行符。

```python
import textwrap

s1 = 'abcdefghijklmnopqrstuvwxyz'
print(textwrap.wrap(s1, 7))
# ['abcdefg', 'hijklmn', 'opqrstu', 'vwxyz']

s11 = 'ab\ncd efghijklmnop'
print(textwrap.wrap(s11, 10))
# ['ab cd e', 'fghijklmno', 'p']
```
对于表示换行的特殊字符 `\n` 被转换成空格，转换后的空格被当作了一个字符来处理。 那么就来测试测试所有转义字符：

```python
s11 = 'start\a\b\f\n\r\t\v\\\'\"\0end'
print(textwrap.fill(s11, 30))
# ['start\x07\x08            \\\'"\x00end']
```

`\a`，`\b`，被转换成了16进制的字符， `\f`、`\n`、`\r`、`\t`、`\v` 都被转换成了空格，`\\`、`\'`、`\"` 正常转义了，它们本身就代表一个字符，而不像前面一些转义字符一样有特殊功能。`\0` 直接消失了，它本身代表的意义就是空字符，那应该也是没有被转换。


###### textwrap.fill(text, width=70, **kwargs)

自动换行并返回新字符串

```
参数:
    text: str，要处理的字符串
    width: int，默认为 70，每行字符最大数量
    **kwargs: 可选参数，与 TextWrapper 实例中的参数相同
返回值:
    str，一个一行或多行的字符串
```

对 `text` 以每行 `width` 个字符自动换行（每隔 `width` 个字符就在其字符后添加 `\n` 字符），返回值为字符串，相当于 `'\n'.join(['a', 'b', 'c'])`

```python
s1 = 'abcdefghijklmnopqrstuvwxyz'
print(textwrap.fill(s1, 7))
# abcdefg
# hijklmn
# opqrstu
# vwxyz

s11 = 'start\a\b\f\n\r\t\v\\\'\"\0end'
print(textwrap.fill(s11, 30))
# star            \'"end
```

`textwrap.fill()` 函数也有 `textwrap.wrap()` 函数类似的特性，会将一部分专业字符转换成空格。

###### textwrap.shorten(text, width, **kwargs)

折叠空格并截短字符串

```
参数:
    text: str，要处理的字符串
    width: int，默认为 70，每行字符最大数量
    **kwargs: 可选参数，与 TextWrapper 实例中的参数相同
返回值:
    str，一个一行的字符串
```

将 `text` 中的多个连续空格折叠为一个，并且按照 `width` 给出的长度截取字符串并添加后缀，截取的字符串加后缀的长度不大于 `width`。截短内容以单词为单位截短。

控制后缀的参数为 `placeholder`，默认值为 `[...]`，并且后缀会占用要求的最大长度，也就是说，加上后缀，截取后的字符串小于或等于 `width` 参数设置的值。

```python
s2 = 'test    test   test  test'
print(textwrap.shorten(s2, 12, placeholder='...'))
# test test...

print(textwrap.shorten(s2, 11, placeholder='...'))
# test...
```

在第一个示例中，将空格折叠后，`test test` 共计 `9` 个字符，加上后缀 `...`，刚巧 `12` 个字符，与 `width` 参数指定的 `12` 个字符相等，在第二个示例中，`test test` 加后缀 `...` 超过了 `width` 参数指定的 `11` 个字符，那么只能再缩短一个单词，也就是结果展示的 `test...`。

###### textwrap.dedent(text)

移除所有行的相同前缀空白符

```
参数:
    text: str，要处理的字符串
返回值:
    str，一行或多行字符串
```
找到所有行中前缀空白符最短的一个，然后所有行移除同等数量的前缀空白符。

```
s = '''
    aaa
  bbb'''
print(textwrap.dedent(s))
# 
#   aaa
# bbb

s = '''
\t\taaa
\tbbb'''
print(textwrap.dedent(s))
# 
#         aaa
# bbb

s = '''
        aaa
\tbbb'''
print(textwrap.dedent(s))
#
#         aaa
#         bbb
```
空格和 `\t` 转义字符都属于空白符，但是二者并不相同，在小编的测试环境中，`\t` 符与 `8` 个空格展示效果相似，但是却没有被移除，说明二者并不被认定为是相同的前缀字符串。

###### textwrap.indent(text, prefix, predicate=None)

在指定行的开头添加字符

```
参数:
    text: str，要处理的字符串
    prefix: str，添加到行开头的字符串
    predicate: 可传入一个匿名函数，默认为 None
返回值:
    str，一个一行或多行的字符串
```

将 `prefix` 添加到 `text` 中每行子字符串中，可以通过 `predicate` 参数传入一个匿名函数来筛选符合条件的行添加 `prefix`。默认情况下，`prefix` 会添加到 `text` 的所有不是只由空白符（包括任何行结束符）组成的行中。

```python
s4 = 'aaa\n bbb \nccc \n'

print(textwrap.indent(s4, '+'))
# +aaa
# + bbb
# +ccc
# 

print(textwrap.indent(s4, '+', lambda line: line[:1] == ' '))  # 指定行第一个字符是空格的
# aaa
# + bbb
# ccc
# 
```

#### class textwrap.TextWrapper(**kwargs)

`TextWrapper` 构造器接受多个可选的关键字参数，也可以在实例化后设置其属性。以下两个对象是类似的。

```python
wr1 = textwrap.TextWrapper()
wr1.width = 9

wr2 = textwrap.TextWrapper(width=9)
```

######  TextWrapper 类包含的属性

- `width` -- 默认为 `70`，自行换行的最大行长度。
- `expand_tabs` -- 默认为 `True`，如果为 `True` 则字符串中的制表符将使用字符串的 `expandtabs()` 方法扩展为空格。
- `tabsize` -- 默认为 `8`，控制字符串的制表符被扩展为空格时转换的数量，仅在 `expand_tabs` 为 `True` 时有效。
- `replace_whitespace` -- 默认为 `True`，使用 `wrap()` 方法会先将字符串中的空白字符（`制表符`，`换行符`，`垂直制表`，`进纸`，`回车`）替换为单个空格。
- `drop_whitespace` -- 默认为 `True`，每一行开头和末尾的空白字符（在包装之后、缩进之前）会被丢弃。 但是段落开头的空白字符如果后面不带任何非空白字符则不会被丢弃。 如果被丢弃的空白字符占据了一个整行，则该整行将被丢弃。
- `initial_indent` -- 默认为 `''`，将被添加到被自动换行输出内容的第一行的字符串。 其长度会被计入第一行的长度。 空字符串不会被缩进。
- `subsequent_indent` -- 默认为 `''`，将被添加到被自动换行输出内容除第一行外的所有行的字符串。 其长度会被计入除行一行外的所有行的长度。
- `fix_sentence_endings` -- 默认为 `False`，将尝试检测句子结尾并确保句子间总是以恰好两个空格符分隔。
- `break_long_words` -- 默认为 `True`，单词长度超过 `width` 值是是否要分开。
- `break_on_hyphens` -- 默认为 `True`，`True`: 将根据英文惯例首选在空白符和复合词的连字符之后自动换行; `False`: 只有空白符会被视为合适的潜在断行位置。
- `max_lines` -- 默认为 `None`，输出字符串最大行数。
- `placeholder` -- 默认为 `'[...]'`，输出被截短文本时，末尾填充的子字符串。

###### TextWrapper 类包含的方法

- `wrap(text)` -- 对 `text` （字符串） 中的单独段落自动换行以使每行长度最多为 `width` 个字符。 所有自动换行选项均获取自 `TextWrapper` 实例的实例属性。 返回由输出行组成的列表，行尾不带换行符。 如果自动换行输出结果没有任何内容，则返回空列表。
- `fill(text)` -- 对 `text` 中的单独段落自动换行并返回包含被自动换行段落的单独字符串。

###### 简单的示例
```python
'''每7个字符自动换行，不转换空白字符，并且第一行行前添加 '--' 前缀'''
wr = textwrap.TextWrapper()
wr.width = 7
wr.replace_whitespace = False
wr.initial_indent = '--'
print(wr.wrap('a\f\v\nbcdefghijklmn'))
# ['--a\x0c\x0b\nb', 'cdefghi', 'jklmn']
```


#### 参考资料

**源代码**: [Lib/textwrap.py](https://github.com/python/cpython/tree/3.9/Lib/textwrap.py)

**官方文档**：https://docs.python.org/zh-cn/3/library/textwrap.html
