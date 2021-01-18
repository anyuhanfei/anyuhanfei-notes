# os.path -- 常用路径操作

`os.path` 库主要针对路径、文件名和目录名进行信息获取、信息整理、判断等等。共计 `29` 个函数和一个属性，将分成两部分介绍，第一部分只介绍比较常用的函数与唯一的属性，第二部分介绍较冷门的函数。

- **系统类型**: `CentOS 8.2`
- **python 版本**: `Python 3.6.8`

#### 唯一的属性

###### os.path.supports_unicode_filenames

在当前文件系统中，若允许使用任意 `Unicode` 字符串作为文件名，那么返回 `True`，否则返回 `False`

```python
import os  # 在本文之后的代码段中，此行将省略

print(os.path.supports_unicode_filenames)
#True
```

#### 分离路径与名称

###### os.path.dirname(path)

返回 `参数路径` 的路径部分，若 `参数路径` 指向一个文件，则返回文件所在的目录路径; 若 `参数路径` 指向一个目录，则返回此目录上层目录的路径。

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `str`，目录路径，特殊情况下为空字符串

```python
print(os.path.dirname('/project/test/test.py'))  # 指定到文件，绝对路径
#'/project/test'
print(os.path.dirname('/project/test/'))  # 指定到目录内，绝对路径
#'/project/test'
print(os.path.dirname('/project/test'))  # 指定到目录上，绝对路径
#'/project'
print(os.path.dirname('./test.py'))  # 指定到文件，相对路径
#'.'
print(os.path.dirname('./'))  # 指定到目录，相对路径
#'.'
```

测试完示例之后就会发现，此函数就是以 `参数路径` 中最后一个 `\` 或 `/` 为标识截取的字符串。

###### os.path.basename(path)

返回 `参数路径` 的基本文件名称，若 `参数路径` 指向一个文件，则返回文件名; 若 `参数路径` 指向一个目录，则返回最后一层目录名。

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `str`，文件或目录名称. 特殊情况下为空字符串

```python
print(os.path.basename('/project/test/test.py'))  # path 是一个文件(绝对路径)
#'test.py'
print(os.path.basename('/project/test/'))  # path 是一个目录，但路径后添加了 '\'， 也就是说 path 指向的是目录内部
#''
print(os.path.basename('/project/test'))  # path 是一个目录(绝对路径)
#'test'
print(os.path.basename('./test.py'))  # path 是一个文件(相对路径)
#'test.py'
print(os.path.basename('./'))  # path 是执行目录(相对路径)
#''
print(os.path.basename('/project/test/tes.py'))  # 指向一个无效文件名
#'tes.py'
```
此函数可以获取路径中的目录名或者文件名，但是当路径指向目录内部并且没有指定目录内文件时，此函数将返回一个空字符串.


###### os.path.split(path)

将 `参数路径` 拆分为两部分，组合成一个元组，即 `(目录, 文件名)`

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `tuple`-- `(目录, 文件名)`

其实此函数得到的结果相当于 `dirname()` 函数和 `basename()` 函数的组合:

```python
print(os.path.split('/project/test/test.py'))
#('/project/test', 'test.py')
print((os.path.dirname('/project/test/test.py'), os.path.basename('/project/test/test.py')))
#('/project/test', 'test.py')
```

测试到这里，小编就感觉这三个函数不会就是用的截取字符串函数吧，然后小编就打开了源码，好家伙，直接好家伙，来看一眼 `dirname()` 函数的源代码:

```python
# 备注内容都是小编自己加的
def dirname(p):
    """Returns the directory component of a pathname"""
    p = os.fspath(p)                # 可以先理解为传什么返回什么
    sep = _get_sep(p)               # 返回二进制的 '/' 或者普通的 '/'
    i = p.rfind(sep) + 1            # 找到最后一个 '/'
    head = p[:i]                    # 截取字符串
    if head and head != sep*len(head):
        head = head.rstrip(sep)
    return head
```

瞬间没有高大上的感觉了，但是这些封装的函数是考虑到了所有的情况， 所以比自己动手实现的同类函数肯定是强上不少的。


#### 获取文件时间信息三兄贵

文件共计有三类时间，最后访问时间，最后修改时间，状态变化时间。以下三个函数分别对应获取一种时间。关于文件时间，这里就不详细解释了，以后用一篇文章来详细说明。

###### os.path.getatime(path)

获取指定文件的最后访问时间

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `float` -- 纪元秒数，也就是时间戳

如果该文件不存在或不可访问，则抛出 `OSError` 异常。

```python
print(os.path.getatime('/project/test/test.py'))  # 文件
#1606185746.9224665

print(os.path.getatime('/project/test'))  # 目录
#1606185672.190904

print(os.path.getatime('/project/test/'))  # 目录，但指向了目录内部
#1606185672.190904

print(os.path.getatime('/project/tes'))  # 错误路径
#Traceback (most recent call last):
#  File "test.py", line 3, in <module>
#    print(os.path.getatime('/project/tes'))
#  File "/usr/lib64/python2.7/genericpath.py", line 59, in getatime
#    return os.stat(filename).st_atime
#OSError: [Errno 2] No such file or directory: '/project/tes'
```

当 `参数路径` 传入的是一个指向目录内部并且没有指明文件的路径时，返回的是目录的最后访问时间。

###### os.path.getmtime(path)

获取指定文件的最后修改时间

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `float` -- 纪元秒数，也就是时间戳

如果该文件不存在或不可访问，则抛出 `OSError` 异常。

```python
print(os.path.getmtime('/project/test/test.py'))  # 文件
#1606188825.5

print(os.path.getmtime('/project/test'))  # 目录
#1606188846.69

print(os.path.getmtime('/project/test/'))  # 目录，但指向了目录内部
#1606188864.17

print(os.path.getatime('/project/tes'))  # 无效路径
#Traceback (most recent call last):
#  File "test.py", line 3, in <module>
#    print(os.path.getatime('/project/tes'))
#  File "/usr/lib64/python2.7/genericpath.py", line 59, in getmtime
#    return os.stat(filename).st_mtime
#OSError: [Errno 2] No such file or directory: '/project/tes'
```

###### os.path.getctime(path)

获取指定文件的变化时间(`ctime`)

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `float` -- 纪元秒数，也就是时间戳

如果该文件不存在或不可访问，则抛出 `OSError` 异常。

```python
print(os.path.getmtime('/project/test/test.py'))  # 文件
#1606189021.03

print(os.path.getmtime('/project/test'))  # 目录
#1606188977.83

print(os.path.getmtime('/project/test/'))  # 目录，但指向了目录内部
#1606188999.06

print(os.path.getatime('/project/tes'))  # 无效路径
#Traceback (most recent call last):
#  File "test.py", line 3, in <module>
#    print(os.path.getatime('/project/tes'))
#  File "/usr/lib64/python2.7/genericpath.py", line 59, in getctime
#    return os.stat(filename).st_ctime
#OSError: [Errno 2] No such file or directory: '/project/tes'
```

#### 文件其他信息获取

###### os.path.getsize(path)

获取指定文件的大小，单位为字节

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `int` -- 文件存储字节

如果该文件不存在或不可访问，则抛出 `OSError` 异常。

```python
print(os.path.getsize('/project/test/test.py'))  # 文件
#60
print(os.path.getsize('/project/test'))  # 目录
#4096
print(os.path.getsize('/project/test/'))  # 目录，但指向了目录内部
#4096
print(os.path.getsize('/project/tes'))  # 无效路径
#Traceback (most recent call last):
#  File "test.py", line 3, in <module>
#    print(os.path.getsize('/project/tes'))
#  File "/usr/lib64/python2.7/genericpath.py", line 49, in getsize
#    return os.stat(filename).st_size
#OSError: [Errno 2] No such file or directory: '/project/tes'
```

#### 判断

###### os.path.isabs(path)

判断指定文件路径是否是绝对路径

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `bool`

`参数路径` 是绝对路径返回 `True`，否则返回 `False`

```python
print(os.path.isabs('/project/test'))  # 目录，绝对路径
#True
print(os.path.isabs('/project/test/'))  # 目录，但指向了目录内部，绝对路径
#True
print(os.path.isabs('/project/test/test.py'))  # 文件，绝对路径
#True
print(os.path.isabs('~'))  # 家目录，Unix 系统家目录的快捷字符
#False
print(os.path.isabs('./test.py')  # 文件，相对路径
#False
print(os.path.isabs('./'))  # 目录，相对路径
#False
print(os.path.isabs('/project/tes'))  # 无效路径，绝对路径
#True
```

###### os.path.isfile(path)

如果 `参数路径` 是 现有的 常规文件，则返回 True。

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `bool`

```python
print(os.path.isfile('/project/test'))  # 目录，绝对路径
#False
print(os.path.isfile('/project/test/test.py'))  # 文件，绝对路径
#True
print(os.path.isfile('./'))  # 目录，相对路径
#False
print(os.path.isfile('./test.py'))  # 文件，相对路径
#True
print(os.path.isfile('/project/test/tes.py'))  # 无效路径，绝对路径
#False
print(os.path.isfile('./tes.py'))  # 无效路径，相对路径
#False
```

这个函数可以跟踪符号链接，也就是软链接，那么也可以来测试一下，先创建一个软链接文件:

```
> ln -S test.py s_test.py
> ls -l
total 4
lrwxrwxrwx 1 root root  7 Nov 24 13:57 s_test.py -> test.py
-rw-r--r-- 1 root root 58 Nov 24 13:47 test.py
```

软链接文件创建成功，开始测试:

```python
print(os.path.isfile('/project/test/s_test.py'))
#True
```

###### os.path.isdir(path)

如果 `参数路径` 是 现有的 目录，则返回 True。

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `bool`

`isdir()` 函数与 `isfile()` 函数类似，前者是判断目录，后者是判断文件，重复一遍测试:

```python
print(os.path.isdir('/project/test'))  # 目录，绝对路径
#True
print(os.path.isdir('/project/test/test.py'))  # 文件，绝对路径
#Flase
print(os.path.isdir('./'))  # 目录，相对路径
#True
print(os.path.isdir('./test.py'))  # 文件，相对路径
#False
print(os.path.isdir('/project/tes'))  # 无效路径，绝对路径
#False
print(os.path.isdir('../tes'))  # 无效路径，相对路径
#False
```

`isdir()` 函数同样可以跟踪符号链接，那么就接着测试，第一步，创建目录的软链接:

```
> ln -s /root/test /project/root_test_s
> ls -l
total 4
lrwxrwxrwx 1 root root   10 Nov 24 16:39 root_test_s -> /root/test
drwxr-xr-x 2 root root 4096 Nov 24 16:13 test
```

第二步，测试目录软链接 isdir() 函数是否可以正确判断:

```python
print(os.path.isdir('/project/root_test_s'))
#True
```

###### os.path.islink(path)

如果 `参数路径` 指向的 现有 目录条目是一个符号链接，则返回 True。

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `bool`

利用 `isdir()` 函数和 `isfile()` 函数创建的两个软链接进行测试:

```python
print(os.path.islink('/project/root_test_s'))  # 指向目录的软链接
#True
print(os.path.islink('/project/test/s_test.py'))  # 指向文件的软链接
#True
print(os.path.islink('/project/test'))  # 普通目录
#False
print(os.path.islink('/project/test/test.py'))  # 普通文件
#False
print(os.path.islink('/project/tes'))  # 无效目录
#False
print(os.path.islink('/project/test/tes.py'))  # 无效文件
#False
```

原文文档上说，如果 Python 运行时不支持符号链接，则总是返回 False。这就难为小编了

###### os.path.exists(path)

判断传入路径或文件描述符，判断路径是否存在或者描述符是否有效.

- **参数**
    - `path` -- 类路径对象，可以是表示路径的 `str` 或 `bytes` 对象，还可以是实现了 `os.PathLike` 协议的对象
- **返回值**
    - `bool`

原文文档上说的小编一脸蒙B，其实可以理解为 `isfile()`、`isdir()` 和 `islink()` 三个函数的合体版，通常使用这个函数判断文件或目录或软链接是否存在就可以了。

```python
print(os.path.exists('/project/test'))  # 目录，绝对路径
#True
print(os.path.exists('/project/test/test.py'))  # 文件，绝对路径
#True
print(os.path.exists('./'))  # 目录，相对路径
#True
print(os.path.exists('./test.py'))  # 文件，相对路径
#True
print(os.path.exists('/project/test/s_test.py'))  # 文件的软链接
#True
print(os.path.exists('/project/root_test_s'))  # 目录的软链接
#True
print(os.path.exists('/project/test/tes.py'))  # 无效文件
#False
print(os.path.exists('/project/tes'))  # 无效目录
#False
```

另外，还有一个 `os.path.lexists(path)` 函数和这个函数的功能一模一样，原文文档上讲的区分也没办法测试出来，小编大胆预测是历史遗留的兼容性问题而派生出来的一个函数。


#### 路径与路径的共同点


###### os.path.commonpath(paths)

获取路径集合 `paths` 的最长公共子路径

- 参数:
    - `paths` -- 包含一个或多个路径的序列
- 返回值:
    - `str`，最长的公共子路径(绝对路径)

```python
print(os.path.commonpath(['E:\\project\\', 'E:\\project\\test']))  # 都是绝对路径
#E:\project
print(os.path.commonpath(['../', './']))  # 都是相对路径
#
print(os.path.commonpath(['E:\\project\\']))  # 单绝对路径
#E:\project
print(os.path.commonpath(['../']))  # 单相对路径
#..
print(os.path.commonpath(['E:\\project\\', './']))  # 同时包含绝对路径和相对路径
#报错
print(os.path.commonpath(['E:\\project\\', 'D:\\nginx']))  # 在不同的驱动器上
#报错
print(os.path.commonpath([]))  # 空 paths
#报错
print(os.path.commonpath(['111', '112']))  # 乱写
#
```


###### os.path.commonprefix(list)

获取路径列表 list 的最长公共前缀

- 参数:
    - list -- 包含一个或多个路径的列表
- 返回值:
    - str，最长公共前缀

```python
print(os.path.commonprefix(['E:\\project\\', 'E:\\project\\test']))  # 绝对路径
#E:\project\
print(os.path.commonprefix(['E:\\abcd', 'E:\\aaa']))  # 绝对路径
#E:\a
print(os.path.commonprefix(['./', '../']))  # 相对路径
#.
print(os.path.commonprefix(['1111', '1122']))  # 非路径
#11

print(os.path.commonprefix(['/project/test/test.py', '/project/test/s_test.py']))  # 绝对路径
#'/project/test/'
print(os.path.commonprefix(['/project/test/test.py', '/project/test/test_s.py']))  # 绝对路径
#'/project/test/test'
print(os.path.commonprefix(['./test.py', '../test/test.py']))  # 相对路径
#'.'
print(os.path.commonprefix(['./test.py', '/project/test/test.py']))  # 相对路径与绝对路径
#''
print(os.path.commonprefix(['11111', '11122']))  # 非路径
#'111'

```

此函数是逐字符比较，因此得到的结果可能是无效路径，对于获取公共子路径这个需求使用 `os.path.commonpath()` 比较合适. 可以将此函数作为获取字符串公共前缀函数使用 (~~不务正业的函数+1~~)


#### 总结

###### 本篇详解了 15 个函数和 1 个属性，剩余的函数留到下篇说明。公众号也是刚刚起步，文章也是在尝试和修改各种风格，本篇文章可能不是很能让人坚持看下去，小编会继续努力的。

#### 参考资料

**源代码**：

[Lib/posixpath.py](https://github.com/python/cpython/tree/3.9/Lib/posixpath.py) （用于 POSIX）

[Lib/ntpath.py](https://github.com/python/cpython/tree/3.9/Lib/ntpath.py) （用于 Windows NT）

**官方文档**: https://docs.python.org/zh-cn/3/library/os.path.html

==公众号文章欢迎转载，转载时请注明来源，微信公众号: python库详解==