前情提示: 测试代码中，右尖括号（`>`）表示命令行中输入的命令； 单独一行并以井字符（`#`）开头的为输出内容； 库的导入仅在本文的第一个测试代码中展现，其他代码块均省略库的导入代码。

- **系统类型**: `Windows 10`
- **python 版本**: `Python 3.9.0`

`itertools` 模块标准化了一个快速、高效利用内存的核心工具集，主要用来实现一系列迭代器。这些工具函数本身或工具函数之间的组合都是非常有用的。它们一起形成了 `迭代器代数`。

`itertools` 模块中所有的函数都是创建并返回一个迭代器。它们的区别就是迭代器生成的内容，以及使用场景。

#### 无穷迭代器

无穷迭代器最大的特点是可无限迭代元素，所以在使用时要注意一定要有限制条件来控制迭代器的停止，否则将造成死循环。

###### itertools.count(start=0, step=1)

```
    start: int | float, 起始值
    step: int | float, 步长
```
创建一个迭代器，第一个元素的值为参数 `start` 的值，之后元素的值按照参数 `step` 的值依次累加。

```python
import itertools

'''打印一下迭代器本身'''
print(itertools.count(7, 1))
# count(7)

'''迭代器类型'''
print(type(itertools.count(7, 1)))
# <class 'itertools.count'>

'''一个简单的小应用'''
for i in itertools.count(7, 1):
    print(i)
    if i > 10:
        break
# 7
# 8
# 9
# 10
# 11
```
`itertools` 模块中使用函数返回的迭代器的类型与函数同名，可直接在使用迭代器的场景使用。

###### itertools.cycle(iterable)
```
iterable: 可迭代对象
```

创建一个迭代器，以传入的迭代器中的元素为基础，返回这些元素，当这些元素被取完时，会重新从第一个元素获取，无限重复。

```python
'''小应用, 当取到第三个 A 时停止'''
test_str = ''
for i in itertools.cycle('ABC'):
    test_str += i
    if test_str.count('A') >= 3:
        break
print(test_str)
# ABCABCA
```

###### itertools.repeat(object[, times])
```
object: python 对象, 可以是任何数据
times: int, 迭代次数, 可选参数, 默认为无限次
```
创建一个迭代器，不断重复 `object`。若设定了 `times`，则最多重复 `times` 次，否则将重复无限次。通常用于 `map()` 函数中当作其中一个参数。

```python
'''有限的迭代器'''
print(list(itertools.repeat(10, 3)))
# [10, 10, 10]

'''使用 map() 函数将两个迭代器元素之间的 pow() 结果收集起来'''
print(list(map(pow, range(10), itertools.repeat(2))))
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

以上这些无穷迭代器在测试时都是使用自身条件或外部限制来使它迭代有限的次数，这些函数生成的迭代器通常用于不确定要迭代次数的场景。

#### 迭代器的操作与处理

###### itertools.accumulate(iterable[, func, *, initial=None])
```
iterable: 可迭代对象
func: 带有两个参数的函数, 可选参数
initial: 关键字参数, 默认为 None, 若此参数传参, 则此参数作为 iterable 的第一个元素
```
创建一个迭代器，默认情况下返回累计汇总值，若传入 `func`，则按照 `func` 中的方法执行。例如: 若 `iterable` 为 `[1, 2, 3]`，则返回的迭代器为 `[1, 1+2, 1+2+3]`。

```python
'''默认情况, 返回累计汇总值'''
print(list(itertools.accumulate([1, 2, 3, 4, 5])))
# [1, 3, 6, 10, 15]

'''方法修改为计算累计的乘积'''
print(list(itertools.accumulate([1, 2, 3, 4, 5], lambda x, y: x * y)))
# [1, 2, 6, 24, 120]

'''在迭代器起始位置添加一个元素, 方法不变, 返回累计汇总值'''
print(list(itertools.accumulate([1, 2, 3, 4, 5], initial=100)))
# [100, 101, 103, 106, 110, 115]

'''在迭代器起始位置添加一个元素, 方法修改为计算累计的乘积'''
print(list(itertools.accumulate([1, 2, 3, 4, 5], lambda x, y: x * y, initial=10)))
# [10, 10, 20, 60, 240, 1200]
```

###### itertools.chain(*iterables)
```
*iterables: 多个可迭代对象, 以逗号隔开
```
创建一个迭代器，将多个可迭代对象的元素添加到迭代器中。

```python
'''将多个可迭代对象整合成一个迭代器'''
temp = itertools.chain([1, 2, 3], ('A', 'B', 'C'), {'一', '二', '三'})
print(list(temp))
# [1, 2, 3, 'A', 'B', 'C', '一', '三', '二']
```

###### itertools.chain.from_iterable(iterable)

```
iterable: 可迭代对象
```
与 `itertools.chain()` 函数类似，但是参数是一个可迭代对象，将这个可迭代对象中元素一一添加到新迭代器中，如果元素是一个可迭代对象，那么会将这个元素内的元素一一添加到新迭代器中。小编自己的理解就是迭代器降维。

```python
'''将二维迭代器降维'''
temp = itertools.chain.from_iterable(['1', ['2', '3'], ('4', '5')])
print(list(temp))
# ['1', '2', '3', '4', '5']

'''只能降一层维度, 三维将至二维'''
temp = itertools.chain.from_iterable(['1', ['2', '3'], ('4', ['5', '6'])])
print(list(temp))
# ['1', '2', '3', '4', ['5', '6']]
```

**PS:** 迭代器维度概念可以理解为，整个迭代器中的元素都不是迭代器类型的就是**一维**，迭代器中的元素有是迭代器的就是**二维**，迭代器中的元素有是迭代器的，然后这个迭代器中还有元素是迭代器的就是**三维**，依此类推。

###### itertools.compress(data, selectors)
```
data: 可迭代对象, 包含所需的数据
selectors: 可迭代对象, 真值测试数据
```
创建一个迭代器，将 `data` 中经过 `selectors` 真值测试为 `True` 的元素保留。当两个可迭代对象中的某一个到达末尾时执行停止，返回最终结果。

```python
'''只判断前三个元素, 并且索引值为 0 和 2 的元素会保留到新迭代器中并返回'''
temp = itertools.compress(['A', 'B', 'C', 'D'], [1, 0, 1])
print(list(temp))
# ['A', 'C']
```

###### itertools.dropwhile(predicate, iterable)
```
predicate: 只需要一个参数的函数
iterable: 可迭代对象
```
创建一个迭代器，依次根据 `perdicate` 函数计算 `iterable` 中的元素，当计算结果第一次为 `false` 时，此元素之前的元素全部抛弃，剩余的 (包含计算结果为负数的这个元素) 保留至新迭代器中。

此函数实际运行流程为: 计算第一个元素，若为 `True` 则不返回，然后计算下一个元素，若都为 `True` 则都不返回，直到第 `N` 个元素的计算结果为 `False`，这时返回这个元素，此后的所有元素都不进行计算了，直接返回。

```python
'''真值测试'''
temp = itertools.dropwhile(bool, [1, 2, 0, 3])
print(list(temp))
# [0, 3]

'''元素的值减2小于等于0的为True'''
temp = itertools.dropwhile(lambda x: x - 2 <= 0, [1, 2, 0, 3, 1])
print(list(temp))
# [3, 1]
```

###### itertools.takewhile(predicate, iterable)
```
predicate: 只需要一个参数的函数
iterable: 可迭代对象
```
创建一个迭代器，将 `iterable` 中的元素当作 `function` 的参数计算，与 `dropwhile()` 函数恰恰相反，当结果的布尔值为 `True` 时，其元素添加到新迭代器中, 直到有元素的计算结果为 `False` 时，此元素与之后的元素全部抛弃。

```python
temp = itertools.takewhile(bool, [1, 3, None, 0, 1])
print(list(temp))
# [1, 3]
```

###### itertools.filterfalse(predicate, iterable)
```
predicate: 只需要一个参数的函数
iterable: 可迭代对象
```
创建一个迭代器，仅保留 `iterable` 中在 `predicate` 计算中为 `False` 的元素。如果 `predicate` 传入的是 `None`，则相当于传入 `bool`，意思是做真值测试。

```python
'''元素的值减2小于等于0的为True'''
temp = itertools.filterfalse(lambda x: x - 2 <= 0, [1, 2, 0, 3, 1])
print(list(temp))
# [3]

'''真值测试'''
temp = itertools.filterfalse(None, [1, 2, 0, 3, 1])
print(list(temp))
# [0]
```

###### itertools.groupby(iterable, key=None)
```
iterable: 可迭代对象
key: 执行函数
```

创建一个迭代器，将 `iterable` 的元素依次通过 `key` 计算出结果，每种结果分别创建一个列表，结果作为列表的第一个元素，所有结果为这个数值的元素组成一个迭代器作为列表的第二个元素，最后这些列表保存至新迭代器中并返回。

在返回的迭代器中，列表内的第二个元素也是迭代器，类型与返回的迭代器一致，并且是共享底层的可迭代对象。因为源是共享的，所以当对返回的迭代器进行操作时，总是会得到空值，所以如果需要在之后的程序中使用这些数据，那么就要手动保存到列表或其他位置。

```python
'''真值测试'''
groups = []
for k, g in itertools.groupby([0, 3, 2, 1, 4], bool):
    groups.append((k, list(g)))

print(groups)
# [(False, [0]), (True, [3, 2, 1, 4])]
```

###### itertools.islice(iterable, start, stop[, step])
```
iterable: 可迭代对象
start: 起始位置
stop: 结束位置
step: 步长
```

创建一个迭代器，返回从 `iterable` 中被选中的元素，被选中的元素从 `start` 索引位置开始，以 `step` 为步长，直到 `stop` 索引位置结束。类似列表、字符串等的切片操作。

在使用 `itertools.islice()` 函数时, 还可以只传入 `iterable` 和 `stop` 两个参数。

若仅这两个参数，则 `start` 默认为 `0`，`step` 默认为 `1`. 若 `stop` 参数传入 `None`，则默认为直到迭代器末尾。`start`、`stop`、`step` 三个参数均不支持负数。
```python
iterable = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

'''结束位置7'''
print(list(itertools.islice(iterable, 7)))
# [0, 1, 2, 3, 4, 5, 6]

'''起始位置1, 结束位置7'''
print(list(itertools.islice(iterable, 1, 7)))
# [1, 2, 3, 4, 5, 6]

'''起始位置1, 结束位置到末尾'''
print(list(itertools.islice(iterable, 1, None)))
# [1, 2, 3, 4, 5, 6, 7, 8, 9]

'''起始位置1, 结束位置7, 步长2'''
print(list(itertools.islice(iterable, 1, 7, 2)))
# [1, 3, 5]
```

###### itertools.starmap(function, iterable)
```
function: 函数
iterable: 可迭代对象
```

创建一个迭代器，将 `iterable` 中的元素当作 `function` 的参数计算，并将结果保留在新迭代器中。`iterable` 中的每个元素都是元组，元组内包含了 `function` 所需的参数。

```python
'''得到同样结果的 starmap() 和 map()'''
temp = itertools.starmap(pow, [(1, 2), (3, 4), (5, 6)])
print(list(temp))
# [1, 81, 15625]

print(list(map(pow, (1, 3, 5), (2, 4, 6))))
# [1, 81, 15625]
```


###### itertools.tee(iterable, n=2)
```
iterable: 可迭代对象
n: 默认值为 2, 分裂的迭代器数量
```

从一个可迭代对象中分裂出 `n` 个独立的可迭代对象并返回。

```python
for i in itertools.tee([1, 2, 3, 4, 5, 6], 2):
    print(list(i))
# [1, 2, 3, 4, 5, 6]
# [1, 2, 3, 4, 5, 6]
```

###### itertools.zip_longest(*iterables, fillvalue=None)
```
*iterables: 一个或多个可迭代对象
fillvalue: 关键字参数, 填充值, 默认为 None
```

创建一个迭代器，从所有传入的可迭代对象中抽取一个元素，将它们组合成一个元组，然后添加到迭代器中。当传入的可迭代对象中的元素被读取完，并且还有其他的可迭代对象的元素并未读取完时，将根据参数 `fillvalue` 的值填补缺失。当所有传入的可迭代对象被读取完毕后，返回新迭代器。

```python
temp = itertools.zip_longest('xyz', '123456', '暗语寒飞', fillvalue='*')
print(list(temp))
# [('x', '1', '暗'), ('y', '2', '语'), ('z', '3', '寒'), ('*', '4', '飞'), ('*', '5', '*'), ('*', '6', '*')]
```

#### 排列组合

###### product(*iterables, repeat=1)
```sh
*iterables: 一个或多个可迭代对象
repeat: 关键字参数, 重复次数, 默认为 1
```

对一个或多个可迭代对象计算笛卡尔积。即为前一个可迭代对象的元素与后一个可迭代对象的元素的所有组合，每个组合生成一个元组并添加到新迭代器中。

参数 `repeat` 是传入的可迭代对象的重复次数, 例如: `product('AB', repeat=2)` 等同于 `product('AB', 'AB')`。
```python
temp = itertools.product('ABC', 'XY')
print(list(temp))
# [('A', 'X'), ('A', 'Y'), ('B', 'X'), ('B', 'Y'), ('C', 'X'), ('C', 'Y')]
temp = itertools.product('AB', 'XY', repeat=2)
print(list(temp))
# [('A', 'X', 'A', 'X'), ('A', 'X', 'A', 'Y'), ('A', 'X', 'B', 'X'), ('A', 'X', 'B', 'Y'), 
#  ('A', 'Y', 'A', 'X'), ('A', 'Y', 'A', 'Y'), ('A', 'Y', 'B', 'X'), ('A', 'Y', 'B', 'Y'), 
#  ('B', 'X', 'A', 'X'), ('B', 'X', 'A', 'Y'), ('B', 'X', 'B', 'X'), ('B', 'X', 'B', 'Y'), 
#  ('B', 'Y', 'A', 'X'), ('B', 'Y', 'A', 'Y'), ('B', 'Y', 'B', 'X'), ('B', 'Y', 'B', 'Y')]
```
###### permutations(iterable, r=None)
```
iterable: 可迭代对象
r: 关键字参数, 新元素的长度, 默认为 None, 即为新元素的长度就是元素个数
```

将 `iterable` 中的元素以长度为 `r` 进行排列。每个排列组合生成一个元组并添加到新迭代器中。

排列时，排列顺序是按照 `iterable` 中元素的顺序进行的。因此，未排序的 `iterable` 和已排序的 `iterable` 排列后的结果是不同的。

若 `iterable` 中有相同值的元素，但是它们的位置是不同的，排列时也就会会认为它们是不同的。

**PS**: 排列即为数学上的排列，从 `n` 个元素中取出 `m` 个元素的无重复排列或直线排列。

```python
'''将4个元素以2的长度排列'''
temp = itertools.permutations('ABCD', 2)
print(list(temp))
# [('A', 'B'), ('A', 'C'), ('A', 'D'), ('B', 'A'), ('B', 'C'), ('B', 'D'), ('C', 'A'), ('C', 'B'), ('C', 'D'), ('D', 'A'), ('D', 'B'), ('D', 'C')

'''将3个元素以3的长度排列'''
temp = itertools.permutations('ABC')
print(list(temp))
# [('A', 'B', 'C'), ('A', 'C', 'B'), ('B', 'A', 'C'), ('B', 'C', 'A'), ('C', 'A', 'B'), ('C', 'B', 'A')]
```

###### combinations(iterable, r)
```
iterable: 可迭代对象
r: 新元素的长度
```

将 `iterable` 中的元素以长度为 `r` 进行排列。每个排列组合生成一个元组并添加到新迭代器中。与 `permutations()` 函数基本一致，但是 `combinations()` 函数会过滤掉元素值一致的元组。

```python
'''筛选掉元素值一样的元组'''
temp = itertools.combinations('ABC', 2)
print(list(temp))
# [('A', 'B'), ('A', 'C'), ('B', 'C')]

'''对照组'''
temp = itertools.permutations('ABC', 2)
print(list(temp))
# [('A', 'B'), ('A', 'C'), ('B', 'A'), ('B', 'C'), ('C', 'A'), ('C', 'B')]
```

###### combinations_with_replacement(iterable, r)
```
iterable: 可迭代对象
r: 关键字参数, 新元素的长度, 默认为 None, 即为新元素的长度就是元素个数
```

`combinations()` 函数的变种，在保留过滤元素值一致的元组这个功能的基础上，此函数在排列时元素自己也会与自己所在的可迭代对象进行排列。

```python
'''会与自己所在的可迭代对象排列'''
temp = itertools.combinations_with_replacement('ABC', 2)
print(list(temp))
# [('A', 'A'), ('A', 'B'), ('A', 'C'), ('B', 'B'), ('B', 'C'), ('C', 'C')]

'''对照组'''
temp = itertools.permutations('ABC', 2)
print(list(temp))
# [('A', 'B'), ('A', 'C'), ('B', 'C')]
```

#### 参考资料

**官方文档**: https://docs.python.org/zh-cn/3/library/itertools.html


公众号 : 「python库详解」，专注于 python 标准库与 python 第三方库的解析教程，以及其他 python 的相关内容。发掘更多原创文章，期待您的关注。