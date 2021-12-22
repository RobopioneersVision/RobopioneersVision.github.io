---
title: Python中的那些不为人知的奇技淫巧 (2).md
toc: true
date: 2021-12-22 09:25:00
---
# Python中的那些不为人知的奇技淫巧  (2)

Hello everyone！我是秦良秦影。

![](Python中的那些不为人知的奇技淫巧 (2)/1.png)

上次我们说到了列表生成式，今天我们来继续唠唠其他的pyhon奇技淫巧，走起~

## 列表转字符串

在实际应用中我们可能会遇到需要将字符串数组转为整个字符串的问题，老规矩，我们先来看看用C来怎么写：

```cpp
char string_array[4][10] = {"I ","am "," Antony",". "};
char target_string[50] = "\0";
for (int  i = 0; i < 4; i++)
{
	strcat(target_string,string_array[i]);
}
```

上述C代码用去了我们整整6行的空间！！！天呐！6行！~~要是用python写，6行都足够我们写一个小游戏了！~~

![](Python中的那些不为人知的奇技淫巧 (2)/2.png)

那么接下来，让我们来看一下用python怎么写：

```python
string_array = ["I ","am "," Antony",". "]
str_ = " ".join(string_array)
```

good, 仅有两行，这简直太python了~

![](Python中的那些不为人知的奇技淫巧 (2)/2.png)

## 几种连接列表的方式

### 1.最直观的相加：

使用 + 对多个列表进行相加，大家都懂，不多bb👇

```python
list1 = [1,2,3]
list2 = [4,5,6]
list3 = [7,8,9]
list_all = list1+list2+list3
```

### 2.itertools：

itertools 是 Python 中的一个非常强大的内置模块，它专门用于操作可迭代对象。

在使用之前，要先用 itertools.chain() 将函数先可迭代对象（在这里指的是列表）串联起来，组成一个更大的可迭代对象,最后再利用 list 将其转化为 列表👇

```python
from itertools import chain
list1 = [1,2,3]
list2 = [4,5,6]
list3 = [7,8,9]
list(chain(list01, list02, list03))
```

哦豁，这样一写，代码整体瞬间b格起来了有木有~

![](Python中的那些不为人知的奇技淫巧 (2)/3.png)

### 3.使用 extend

在python中，我们可以通过调用extend方法来拓展一个列表👇

```python
list1 = [1,2,3]
list2 = [4,5,6]
list1.extend(list2)
```

### 4.利用*解包

使用 * 可以解包列表，再将解包后的列表合并（解包就是将容器里面的元素逐个取出来放在其它地方，好比老婆去菜市场买了一袋苹果回来分别发给家里的每个成员，这个过程就是解包）：

```python
list1 = [1,2,3]
list2 = [4,5,6]
list3 = [7,8,9]
print([*list1,*list2,*list3])
```

输出结果为：

```python
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 5.使用 heapq

heapq 是 Python 的一个标准模块，它提供了堆排序算法的实现。

该模块里有一个 merge 方法，可以用于合并多个列表，如下所示👇

```python
from heapq import merge
list1 = [1,2,3]
list2 = [4,5,6]
list3 = [7,8,9]
l = list(merge(list1, list2, list3))
print(l)
```

输出结果为

```python
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

要注意的是，heapq.merge 除了合并多个列表外，它还会将合并后的最终的列表进行排序👇

```python
from heapq import merge
list1 = [2,5,3]
list2 = [1,4,6]
list3 = [7,9,8]
l = list(merge(list1, list2, list3))
print(l)
```

输出结果为

```python
[1, 2, 4, 5, 3, 6, 7, 9, 8]
```

到这里有的小伙伴可能已经注意到了，上面的输出结果并没有排好序。

![](Python中的那些不为人知的奇技淫巧 (2)/4.png)

没错！盲生你发现了华点——merge仅支持对输入的有序数据进行排序，由于我们输入的 list1 , list3 都是乱序的，所以对应的 2, 5, 3, 7, 9, 8的元素顺序是错误的~

![](Python中的那些不为人知的奇技淫巧 (2)/5.png)

因此，如果想要使用merge进行归并列表并排序的话，注意要输入有序列表~

```python
from heapq import merge
list1 = [2,3,5]
list2 = [1,4,6]
list3 = [7,8,9]
l = list(merge(list1, list2, list3))
print(l)
```

输出结果为

```python
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

它的效果等价于下面这行代码

```python
sorted(itertools.chain(list1+list2+list3))
```

## and 和 or 的短路效应

and 和 or 是我们再熟悉不过的两个逻辑运算符，在 Python 也有它有妙用👇

- 当一个 **or 表达式**中所有值都为真，Python会选择第一个值
- 当一个 **and 表达式** 所有值都为真，Python 会选择最后一个值。

例如，我们要自定义这样一个函数：传入两个数字a, b ，如果a与b的比值小于0.5，则返回True, 否则返回False，那么必然会涉及到一个问题——是否为0，而利用我们今天的短路效应，便可以很简便的解决这一问题：

```python
def func(a,b):
	if b and a/b>0.5:
		return True
	return False
```

![](Python中的那些不为人知的奇技淫巧 (2)/6.png)

今天的小技巧就介绍到这里了，我是秦良秦影，让我们下次再见~

