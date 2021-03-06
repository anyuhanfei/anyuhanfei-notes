众所周知，以元素之间进行比较的方式进行排序的算法，在时间复杂度上最低也只能是 `O(nlog^n)`，归并排序算法的时间复杂度就是 `O(nlog^n)`。

归并排序是分治策略的一个典型应用。分治策略就是将大的问题进行多次分割，生成多个小的子问题，直到分割成最小单位。这些小问题是可以轻松解决的，递归合并已解决的小问题，最终解决原问题。

归并排序就是依照分治策略，将一个无序的序列多次分割，直到每个子序列都是最小单位(只有一个元素或空序列)，这些子序列都可以被当作有序序列。这时针对子序列的排序问题解决了，那么只需要注意在递归合并这些子序列时，合并生成的子序列也是有序的，最终得到的结果就必然是一个有序序列。思路清晰了，具体要怎么做呢?

#### 分

假设现在有一个由八个元素组成的序列，这八个元素的大小排列顺序是混乱的。根据分治策略，可以把这个序列一分为二，这样就分成了两个由四个元素组成的子序列。

但是这两个子序列依旧无法轻而易举的解决排序问题，那就继续分割，再次分割后得到了四个由两个元素组成的子序列，然后再次分割，分割成八个由一个元素组成的子序列。这时，每个子序列中只有一个元素，这也必然是有序的，那么子序列的排序问题解决了，分这一步也就结束了。

![image](https://github.com/anyuhanfei/anyuhanfei-notes/blob/master/附属图片/算法与数据结构/MergeSort%20--%20归并排序│算法与数据结构/归并排序-分.png?raw=true)

#### 合

八个由一个元素组成的有序序列两两合并，合并成四个由两个元素组成的有序序列，再次进行两两合并，直到合并成一个由八个元素组成的有序序列，问题解决。

![image](https://github.com/anyuhanfei/anyuhanfei-notes/blob/master/附属图片/算法与数据结构/MergeSort%20--%20归并排序│算法与数据结构/归并排序-合.png?raw=true)

说的轻轻松松，两个只有一个元素的有序序列合并成一个有序序列非常简单，对比它们中的那个唯一的元素即可，但是有多个元素的有序序列之间如何合并成一个大的有序序列呢?

![image](https://github.com/anyuhanfei/anyuhanfei-notes/blob/master/附属图片/算法与数据结构/MergeSort%20--%20归并排序│算法与数据结构/归并排序-原理.png?raw=true)

让我们先抛开之前讲述的例子，来到一个新的例子面前。假设有两个有序序列，需要把这两个有序序列合并成一个有序序列。

如上图，可以想象成两个人各持有一个有序序列进行游戏，两个人分别从自己持有的序列中拿出一个最小值的元素作为手牌，对比手牌大小，较小的一方就要将手牌丢弃到坟场中，然后从持有的序列中再拿出一个最小值的元素加入手牌。直到某一方失去全部元素，另一方将手中剩余的元素按顺序丢弃到坟场中。在游戏中，元素加入到坟场中的顺序是从最小到最大，如果将坟场理解为一个序列，那么现在就得到了一个合并后的有序序列。

如果将以上的排序方法整合到归并排序中，那么归并排序剩下的两个由多个元素组成的有序序列合并成一个有序序列的问题就解决了。

#### 代码

归并排序的原理和问题都已解决，上代码:

```python
def 归并排序(unsort_list):
    ''' 子序列的元素数为 1 或者是空序列时, 子序列自动成为有序序列, 直接返回子序列 '''
    if len(unsort_list) <= 1:
        return unsort_list
    ''' 将序列(元素数大于 1)一分为二 '''
    中值索引 = int(len(unsort_list) / 2)
    左序列 = 归并排序(unsort_list[:中值索引])
    右序列 = 归并排序(unsort_list[中值索引:])
    ''' 两个有序序列合并成一个有序序列 '''
    左指针, 右指针, 指针, 左边界, 右边界 = 0, 0, 0, len(左序列), len(右序列)
    while (左指针 < 左边界) or (右指针 < 右边界):  # 任何一个子序列的指针未到达边界, 就继续执行
        if (右指针 < 右边界) and ((左指针 >= 左边界) or (左序列[左指针] >= 右序列[右指针])):
            # 如果右边未到达边界, 继续判断; 左边到达边界 或者 左边第一个元素大于右边第一个元素, 右边小, 进入主列表
            unsort_list[指针] = 右序列[右指针]
            右指针, 指针 = 右指针 + 1, 指针 + 1
        if (左指针 < 左边界) and ((右指针 >= 右边界) or (右序列[右指针] > 左序列[左指针])):
            # 如果左边未到达边界, 继续判断; 右边到达边界 或者 右边第一个元素大于左边第一个元素, 左边小, 进入主列表
            unsort_list[指针] = 左序列[左指针]
            左指针, 指针 = 左指针 + 1, 指针 + 1
    return unsort_list


if __name__ == "__main__":
    print(归并排序([6, 3, 2, 7, 1, 5, 8, 4]))
```

在上述代码中，`while` 语句部分的代码段中的判断都合并在了一起，很不好理解，需要花费时间去整理思路，其实可以将这些判断全部拆分出来，这样更简单的去理解归并排序:

```python
'''注: 这里仅写了 while 语句部分的代码, 其他部分的代码段相同'''

def 归并排序(unsort_list):
    # 省略

    ''' 两个子序列都有未比较的元素, 任意一个子序列的指针到达边界, 都会结束此循环 '''
    左指针, 右指针, 指针, 左边界, 右边界 = 0, 0, 0, len(左序列), len(右序列)
    while (左指针 < 左边界) and (右指针 < 右边界):
        if 左序列[左指针] > 右序列[右指针]:  # 比元素的值的大小, 注意指针的移动
            unsort_list[指针] = 右序列[右指针]
            右指针 += 1
        else:
            unsort_list[指针] = 左序列[左指针]
            左指针 += 1
        指针 += 1
    ''' 这时必有一个子序列的指针到达边界, 另一个子序列的指针未到达边界, 将这个子序列剩余的元素加入到合并序列中 '''
    while 左指针 < 左边界:
        unsort_list[指针] = 左序列[左指针]
        左指针 += 1
        指针 += 1
    while 右指针 < 右边界:
        unsort_list[指针] = 右序列[右指针]
        右指针 += 1
        指针 += 1

    # 省略
```

#### 参考资料

- 视频 [《清华大学邓俊辉数据结构与算法【完】》](https://www.bilibili.com/video/BV1jt4y117KR?p=80)


公众号 : 「python库详解」，专注于 python 标准库与 python 第三方库的解析教程，以及其他 python 的相关内容。发掘更多原创文章，期待您的关注。

