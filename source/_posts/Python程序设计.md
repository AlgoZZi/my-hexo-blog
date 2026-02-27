---
title: Python程序设计
date: 2026-01-16 22:25:33
tags:
- Python
categories:
- Python语言程序设计
description: "系统整理 Python 程序设计核心知识点，涵盖基础语法、数据类型、函数、面向对象、文件操作、异常处理及常用库"
---



## 目录

1. [字符串](#1-字符串)
   1.1 [字符串的创建与基本特性](#11-字符串的创建与基本特性)
   1.2 [字符串的索引与切片](#12-字符串的索引与切片)
   1.3 [字符串的内置处理函数](#13-字符串的内置处理函数)
   1.4 [字符串的常用方法](#14-字符串的常用方法)
   1.5 [字符串的格式化](#15-字符串的格式化)
   1.6 [字符串的连接与重复](#16-字符串的连接与重复)
   1.7 [字符串的成员关系判断](#17-字符串的成员关系判断)
2. [数据类型转换](#2-数据类型转换)
   2.1 [基本数据类型转换](#21-基本数据类型转换)
   2.2 [高级数据类型转换](#22-高级数据类型转换)
   2.3 [常用数学函数](#23-常用数学函数)
3. [列表](#3-列表)
   3.1 [列表的创建](#31-列表的创建)
   3.2 [列表的访问](#32-列表的访问)
   3.3 [列表的遍历](#33-列表的遍历)
   3.4 [列表元素的增加](#34-列表元素的增加)
   3.5 [列表元素的删除](#35-列表元素的删除)
   3.6 [列表元素的修改](#36-列表元素的修改)
   3.7 [列表元素的排序](#37-列表元素的排序)
   3.8 [列表的其他操作](#38-列表的其他操作)
4. [元组](#4-元组)
   4.1 [元组的基本特性](#41-元组的基本特性)
   4.2 [元组的常用操作](#42-元组的常用操作)
   4.3 [序列解包](#43-序列解包)
5. [字典](#5-字典)
   5.1 [字典的基本特性](#51-字典的基本特性)
   5.2 [字典的创建](#52-字典的创建)
   5.3 [字典元素的访问](#53-字典元素的访问)
   5.4 [字典元素的修改](#54-字典元素的修改)
   5.5 [字典的遍历](#55-字典的遍历)
6. [集合](#6-集合)
   6.1 [集合的基本特性](#61-集合的基本特性)
   6.2 [集合的创建](#62-集合的创建)
   6.3 [集合的常用方法及运算符](#63-集合的常用方法及运算符)
   6.4 [不可变集合](#64-不可变集合)
7. [函数](#7-函数)
   7.1 [内置函数](#71-内置函数)
   7.2 [自定义函数](#72-自定义函数)
   7.3 [函数的特殊参数](#73-函数的特殊参数)
   7.4 [lambda函数](#74-lambda函数)
   7.5 [变量作用域](#75-变量作用域)
   7.6 [递归函数](#76-递归函数)
   7.7 [main函数](#77-main函数)
8. [面向对象程序设计](#8-面向对象程序设计)
   8.1 [类与对象](#81-类与对象)
   8.2 [属性](#82-属性)
   8.3 [方法](#83-方法)
   8.4 [运算符重载](#84-运算符重载)
   8.5 [继承](#85-继承)
   8.6 [多态性](#86-多态性)
9. [文件和目录操作](#9-文件和目录操作)
   9.1 [文件类型与基本操作](#91-文件类型与基本操作)
   9.2 [文件打开模式](#92-文件打开模式)
   9.3 [文件读写方法](#93-文件读写方法)
   9.4 [CSV文件操作](#94-csv文件操作)
   9.5 [Excel文件操作](#95-excel文件操作)
   9.6 [图像文件操作](#96-图像文件操作)
   9.7 [JSON文件操作](#97-json文件操作)
   9.8 [os和os.path模块](#98-os和ospath模块)
   9.9 [shutil模块](#99-shutil模块)
10. [异常处理](#10-异常处理)
    10.1 [异常类](#101-异常类)
    10.2 [异常处理结构](#102-异常处理结构)
    10.3 [抛出异常](#103-抛出异常)
    10.4 [自定义异常](#104-自定义异常)
    10.5 [断言](#105-断言)
11. [常用库](#11-常用库)
    11.1 [库的导入方式](#111-库的导入方式)
    11.2 [math库](#112-math库)
    11.3 [random库](#113-random库)
    11.4 [datetime库](#114-datetime库)
    11.5 [NumPy库](#115-numpy库)
    11.6 [pandas库](#116-pandas库)
    11.7 [matplotlib库](#117-matplotlib库)

---

## 1. 字符串

### 1.1 字符串的创建与基本特性

#### 创建方式

```python
# 单引号创建
s1 = 'Hello'
# 双引号创建
s2 = "World"
# 三引号创建多行字符串
s3 = '''Hello
World''' # 包含换行符
s4 = """Hello
World""" # 效果同三单引号
```

#### 基本特性

- **不可变性**：字符串创建后不可修改，任何修改操作都会返回新字符串
- **有序性**：支持索引和切片操作
- **大小写敏感**：`'A' != 'a'`

#### 易错点

- 单引号字符串内可以包含双引号，双引号字符串内可以包含单引号
- 若字符串内需要包含相同引号，需使用转义字符 `\`
- 三引号字符串会保留所有格式，包括换行符和缩进



### 1.2 字符串的索引与切片

#### 索引

```python
s = "Python"
print(s[0])   # 'P'  正向索引，从0开始
print(s[-1])  # 'n'  反向索引，从-1开始
```

#### 切片

```python
s = "Python"
print(s[0:3])    # 'Pyt'  [开始:结束)，左闭右开区间
print(s[:3])     # 'Pyt'  省略开始，从0开始
print(s[3:])     # 'hon'  省略结束，到字符串末尾
print(s[0:6:2])  # 'Pto'  [开始:结束:步长]
print(s[::-1])   # 'nohtyP'  步长为-1，逆序字符串
```

#### 注意点

- 切片不会引发索引越界异常
- 切片结果不包含结束索引位置的字符
- 步长可以为负数，表示从右向左取值



### 1.3 字符串的内置处理函数

| 函数       | 说明               | 示例                  |
| ---------- | ------------------ | --------------------- |
| `str(obj)` | 将对象转换为字符串 | `str(123)` → `'123'`  |
| `len(s)`   | 返回字符串长度     | `len('Python')` → `6` |



### 1.4 字符串的常用方法

#### 1.4.1 split() - 分割字符串

```python
s = "Hello,World,Python"
print(s.split(','))    # ['Hello', 'World', 'Python']
print(s.split(',', 1)) # ['Hello', 'World,Python']  限制分割次数
```



#### 1.4.2 join() - 连接字符串

```python
lst = ['Hello', 'World', 'Python']
print(','.join(lst))   # 'Hello,World,Python'
```



#### 1.4.3 replace() - 替换字符串

```python
s = "Hello World"
print(s.replace('World', 'Python'))  # 'Hello Python'
print(s.replace('o', '0', 1))         # 'Hell0 World'  限制替换次数
```



#### 1.4.4 strip() / lstrip() / rstrip() - 去除空白字符

```python
s = "   Hello World   "
print(s.strip())   # 'Hello World'  去除两端空白
print(s.lstrip())  # 'Hello World   '  去除左端空白
print(s.rstrip())  # '   Hello World'  去除右端空白

# 补充内容：可以指定要去除的字符
s = "###Hello World###"
print(s.strip('#'))  # 'Hello World'
```



#### 1.4.5 其他常用方法

```python
s = "Hello World"
print(s.upper())      # 'HELLO WORLD'  转换为大写
print(s.lower())      # 'hello world'  转换为小写
print(s.find('o'))    # 4  查找子串首次出现的索引
print(s.rfind('o'))   # 7  查找子串最后出现的索引
print(s.startswith('Hello'))  # True  判断是否以指定子串开头
print(s.endswith('World'))    # True  判断是否以指定子串结尾
```



### 1.5 字符串的格式化

#### 1.5.1 format()方法

```python
# 位置参数
print("Hello, {0}! I'm {1}.".format("World", "Python"))  # 'Hello, World! I'm Python.'
# 关键字参数
print("Hello, {name}! I'm {lang}.".format(name="World", lang="Python"))  # 'Hello, World! I'm Python.'
# 格式化数字
print("Pi is approximately {:.2f}".format(3.14159))  # 'Pi is approximately 3.14'  保留两位小数
print("Number: {:05d}".format(123))  # 'Number: 00123'  补零至5位
print("Percent: {:.1%}".format(0.75))  # 'Percent: 75.0%'  百分比格式
```



#### 1.5.2 f-string（Python 3.6+）

```python
name = "World"
lang = "Python"
print(f"Hello, {name}! I'm {lang}.")  # 'Hello, World! I'm Python.'
pi = 3.14159
print(f"Pi is approximately {pi:.2f}")  # 'Pi is approximately 3.14'
```



#### 1.5.3 老式格式化（%运算符）

```python
print("Hello, %s! I'm %s." % ("World", "Python"))  # 'Hello, World! I'm Python.'
print("Pi is approximately %.2f" % 3.14159)  # 'Pi is approximately 3.14'
```

#### 注意点

- 推荐使用f-string，语法更简洁，性能更好
- format()方法更灵活，支持位置参数、关键字参数等
- 老式格式化（%）在Python 3中仍支持，但不推荐使用



### 1.6 字符串的连接与重复

```python
# 连接
print("Hello" + " " + "World")  # 'Hello World'
# 重复
print("Hello" * 3)  # 'HelloHelloHello'
```



### 1.7 字符串的成员关系判断

```python
s = "Hello World"
print('Hello' in s)    # True
print('Python' in s)   # False
print('Hello' not in s)  # False
```



### 1.8 字符串小结

- 字符串是不可变的有序序列
- 支持索引和切片操作
- 提供丰富的内置方法处理字符串
- 多种格式化方式，推荐使用f-string

---



## 2. 数据类型转换

### 2.1 基本数据类型转换

```python
# int() - 转换为整数
print(int(3.14))     # 3  小数转整数，向下取整
print(int("123"))     # 123  字符串转整数
print(int("0x10", 16))  # 16  指定基数

# float() - 转换为浮点数
print(float(3))       # 3.0
print(float("3.14"))   # 3.14

# bool() - 转换为布尔值
print(bool(0))        # False
print(bool(1))        # True
print(bool(""))       # False
print(bool("Hello"))  # True
```

#### 注意点

- `int()`转换小数时会截断小数部分，不是四舍五入
- 字符串转数字时，字符串必须是有效的数字表示
- 空字符串、0、None转换为布尔值都是False，其他值为True



### 2.2 高级数据类型转换

```python
# complex() - 转换为复数
print(complex(1))     # (1+0j)
print(complex(1, 2))  # (1+2j)

# str() - 转换为字符串
print(str(123))       # '123'
print(str([1, 2, 3])) # '[1, 2, 3]'

# repr() - 转换为表达式字符串
print(repr('Hello'))  # "'Hello'"

# eval() - 执行字符串表达式
print(eval('1 + 2'))  # 3
print(eval('"Hello" + "World"'))  # 'HelloWorld'

# chr() - ASCII码转字符
print(chr(65))        # 'A'

# ord() - 字符转ASCII码
print(ord('A'))       # 65

# hex() - 转换为十六进制字符串
print(hex(16))        # '0x10'

# oct() - 转换为八进制字符串
print(oct(8))         # '0o10'
```

#### 注意点

- `eval()`函数会执行字符串中的任意代码，存在安全风险，不要用于处理用户输入
- `repr()`和`str()`的区别：`repr()`返回对象的“官方”字符串表示，适合调试；`str()`返回对象的“友好”字符串表示，适合用户查看



### 2.3 常用数学函数

```python
import math

# 数值函数
print(abs(-10))      # 10  绝对值
print(min(1, 2, 3))  # 1  最小值
print(max(1, 2, 3))  # 3  最大值
print(math.sqrt(16))  # 4.0  平方根
print(math.ceil(3.14))  # 4  向上取整
print(math.floor(3.14))  # 3  向下取整

# 字符串函数
s = "Hello World"
print(len(s))         # 11  字符串长度
print(s.upper())      # 'HELLO WORLD'  转换为大写
print(s.lower())      # 'hello world'  转换为小写
print(s.find('o'))    # 4  查找子串首次出现的索引
print(s.replace('o', '0'))  # 'Hell0 W0rld'  替换字符串
print(s.strip())      # 'Hello World'  去除两端空白
```



### 2.4 数据类型转换小结

- Python提供丰富的数据类型转换函数
- 转换过程中需注意数据类型的兼容性
- 高级转换函数（如eval()）存在安全风险，需谨慎使用

---



## 3. 列表

### 3.1 列表的创建

#### 3.1.1 定义法

```python
lst1 = [1, 2, 3]
lst2 = [1, 'a', True, [1, 2]]  # 元素可以是任何类型，类型可不同
```



#### 3.1.2 list()函数

```python
lst1 = list()         # 空列表
lst2 = list("Python")  # 从字符串创建：['P', 'y', 't', 'h', 'o', 'n']
lst3 = list((1, 2, 3))  # 从元组创建：[1, 2, 3]
```



#### 3.1.3 列表生成式

```python
# 基本形式
lst1 = [x for x in range(5)]  # [0, 1, 2, 3, 4]

# 带条件
lst2 = [x for x in range(10) if x % 2 == 0]  # [0, 2, 4, 6, 8]

# 嵌套
lst3 = [x + y for x in 'AB' for y in '12']  # ['A1', 'A2', 'B1', 'B2']
```



### 3.2 列表的访问

```python
lst = [1, 2, 3, 4, 5]
print(lst[0])    # 1  正向索引
print(lst[-1])   # 5  反向索引
print(lst[1:4])  # [2, 3, 4]  切片
```

#### 注意点

- 索引越界会引发IndexError异常
- 切片不会引发索引越界异常



### 3.3 列表的遍历

#### 3.3.1 for循环

```python
lst = [1, 2, 3, 4, 5]
for item in lst:
    print(item)
```



#### 3.3.2 for循环配合enumerate()函数

```python
lst = ['a', 'b', 'c']
for index, item in enumerate(lst):
    print(index, item)
# 输出：
# 0 a
# 1 b
# 2 c
```



#### 3.3.3 while循环

```python
lst = [1, 2, 3, 4, 5]
i = 0
while i < len(lst):
    print(lst[i])
    i += 1
```



### 3.4 列表元素的增加

#### 3.4.1 append() - 在列表末尾添加元素

```python
lst = [1, 2, 3]
lst.append(4)
print(lst)  # [1, 2, 3, 4]
```



#### 3.4.2 extend() - 在列表末尾添加另一个列表的所有元素

```python
lst1 = [1, 2, 3]
lst2 = [4, 5, 6]
lst1.extend(lst2)
print(lst1)  # [1, 2, 3, 4, 5, 6]
```



#### 3.4.3 insert() - 在指定位置插入元素

```python
lst = [1, 2, 3]
lst.insert(1, 'a')  # 在索引1位置插入'a'
print(lst)  # [1, 'a', 2, 3]
```

#### 注意点

- `append()`只能添加一个元素，即使添加的是列表，也会作为单个元素添加
- `extend()`添加的是列表的所有元素，不是作为单个元素
- `insert()`的时间复杂度为O(n)，频繁在列表开头插入元素会影响性能



### 3.5 列表元素的删除

#### 3.5.1 del命令

```python
lst = [1, 2, 3, 4, 5]
del lst[0]      # 删除索引0位置的元素
print(lst)      # [2, 3, 4, 5]
del lst[1:3]    # 删除切片
print(lst)      # [2, 5]
```



#### 3.5.2 pop()方法

```python
lst = [1, 2, 3, 4, 5]
print(lst.pop())    # 5  删除并返回最后一个元素
print(lst)          # [1, 2, 3, 4]
print(lst.pop(1))   # 2  删除并返回指定位置的元素
print(lst)          # [1, 3, 4]
```



#### 3.5.3 remove()方法

```python
lst = [1, 2, 3, 2, 4]
lst.remove(2)    # 删除第一个出现的2
print(lst)       # [1, 3, 2, 4]
```



#### 3.5.4 clear()方法

```python
lst = [1, 2, 3, 4, 5]
lst.clear()
print(lst)  # []
```

#### 注意点

- `del`命令可以删除单个元素或切片
- `pop()`方法有返回值，`remove()`方法没有返回值
- `remove()`方法删除第一个匹配的元素，若元素不存在会引发ValueError异常



### 3.6 列表元素的修改

```python
lst = [1, 2, 3, 4, 5]
lst[0] = 'a'     # 修改单个元素
print(lst)       # ['a', 2, 3, 4, 5]
lst[1:3] = [6, 7]  # 修改切片
print(lst)       # ['a', 6, 7, 4, 5]
```



### 3.7 列表元素的排序

#### 3.7.1 sort()方法 - 原地排序

```python
lst = [3, 1, 4, 1, 5, 9]
lst.sort()          # 默认升序
print(lst)          # [1, 1, 3, 4, 5, 9]
lst.sort(reverse=True)  # 降序
print(lst)          # [9, 5, 4, 3, 1, 1]
```



#### 3.7.2 sorted()函数 - 返回新列表

```python
lst = [3, 1, 4, 1, 5, 9]
sorted_lst = sorted(lst)  # 返回新列表，原列表不变
print(sorted_lst)   # [1, 1, 3, 4, 5, 9]
print(lst)          # [3, 1, 4, 1, 5, 9]
```

#### 注意点

- `sort()`方法原地排序，修改原列表，无返回值
- `sorted()`函数返回新列表，原列表不变
- 都支持`reverse`参数控制排序方向
- 都支持`key`参数指定排序依据



### 3.8 列表的其他操作

```python
lst = [1, 2, 3, 4, 5]
print(len(lst))       # 5  列表长度
print(lst.count(1))   # 1  元素出现次数
print(lst.index(3))   # 2  元素首次出现的索引
print(sum(lst))       # 15  元素求和
print(min(lst))       # 1  最小值
print(max(lst))       # 5  最大值
print(lst.copy())     # [1, 2, 3, 4, 5]  浅拷贝
lst.reverse()         # 反转列表
print(lst)            # [5, 4, 3, 2, 1]
```

### 3.9 列表小结

- 列表是可变的有序序列
- 元素可以是任何类型，支持不同类型的元素
- 提供丰富的方法操作列表
- 支持切片、索引等操作

---



## 4. 元组

### 4.1 元组的基本特性

- **有序性**：支持索引和切片操作
- **不可变性**：创建后不可修改
- **元素类型多样性**：元素可以是任何类型



### 4.2 元组的常用操作

#### 创建

```python
# 圆括号创建
t1 = (1, 2, 3)
# 省略圆括号
t2 = 1, 2, 3
# 单个元素元组，必须加逗号
t3 = (1,)
# 使用tuple()函数
t4 = tuple([1, 2, 3])
```



#### 访问

```python
t = (1, 2, 3, 4, 5)
print(t[0])    # 1  索引访问
print(t[1:4])  # (2, 3, 4)  切片
```



#### 其他操作

```python
t = (1, 2, 3, 4, 5)
print(len(t))       # 5  长度
print(t.count(1))   # 1  元素出现次数
print(t.index(3))   # 2  元素首次出现的索引
print(sum(t))       # 15  求和
print(min(t))       # 1  最小值
print(max(t))       # 5  最大值
```

#### 注意点

- 创建单个元素的元组时，必须在元素后面加逗号，否则会被视为普通括号
- 元组是不可变的，但如果元组中包含可变对象（如列表），则可变对象的内容可以修改



### 4.3 序列解包

```python
# 基本解包
t = (1, 2, 3)
a, b, c = t
print(a, b, c)  # 1 2 3

# 扩展解包（Python 3+）
t = (1, 2, 3, 4, 5)
a, b, *rest = t
print(a, b, rest)  # 1 2 [3, 4, 5]

# 交换变量
a, b = 1, 2
a, b = b, a  # 交换a和b的值
print(a, b)  # 2 1
```

#### 注意点

- 解包时变量数量必须与元组长度匹配，否则会引发ValueError异常
- 扩展解包（*rest）可以接收任意数量的元素，返回列表
- 序列解包适用于所有序列类型（列表、元组、字符串等）



### 4.4 元组小结

- 元组是不可变的有序序列
- 适合存储不可变的数据集合
- 支持序列解包，方便变量赋值

---



## 5. 字典

### 5.1 字典的基本特性

- **键值对存储**：键必须唯一，值可以是任何类型
- **键的要求**：键必须是不可变类型（数字、字符串、元组）
- **可变容器**：创建后可以修改
- **无序性**：Python 3.7+中字典保持插入顺序，之前版本无序



### 5.2 字典的创建

#### 5.2.1 使用花括号直接创建

```python
d1 = {'name': 'Tom', 'age': 18}
d2 = {}  # 空字典
```



#### 5.2.2 利用dict函数生成字典

```python
# 创建空字典
d1 = dict()
# 利用序列创建字典
d2 = dict([('name', 'Tom'), ('age', 18)])
# 利用zip创建字典
d3 = dict(zip(['name', 'age'], ['Tom', 18]))
# 利用关键字参数创建字典
d4 = dict(name='Tom', age=18)
```



### 5.3 字典元素的访问

#### 5.3.1 使用方括号

```python
d = {'name': 'Tom', 'age': 18}
print(d['name'])  # 'Tom'
print(d['gender'])  # KeyError: 'gender'  键不存在会引发异常
```



#### 5.3.2 get()方法

```python
d = {'name': 'Tom', 'age': 18}
print(d.get('name'))       # 'Tom'
print(d.get('gender'))     # None  键不存在返回None
print(d.get('gender', 'Unknown'))  # 'Unknown'  键不存在返回默认值
```



### 5.4 字典元素的修改

#### 5.4.1 增加新元素

```python
d = {'name': 'Tom', 'age': 18}
d['gender'] = 'Male'
print(d)  # {'name': 'Tom', 'age': 18, 'gender': 'Male'}
```



#### 5.4.2 修改已有键值对

```python
d = {'name': 'Tom', 'age': 18}
d['age'] = 19
print(d)  # {'name': 'Tom', 'age': 19}
```



#### 5.4.3 删除键值对

```python
d = {'name': 'Tom', 'age': 18, 'gender': 'Male'}
# pop()方法 - 删除并返回值
age = d.pop('age')
print(age, d)  # 18 {'name': 'Tom', 'gender': 'Male'}
# clear()方法 - 清空字典
d.clear()
print(d)  # {}
# del命令 - 删除键值对或字典
d = {'name': 'Tom', 'age': 18}
del d['name']
print(d)  # {'age': 18}
del d
```

#### 注意点

- 字典的`sum()`、`max()`、`min()`操作针对的是键，不是值
- 键必须是不可变类型，否则会引发TypeError异常
- 使用方括号访问不存在的键会引发KeyError异常，推荐使用get()方法



### 5.5 字典的遍历

#### 5.5.1 遍历键

```python
d = {'name': 'Tom', 'age': 18, 'gender': 'Male'}
for key in d:
    print(key)  # name age gender
for key in d.keys():
    print(key)  # name age gender
```



#### 5.5.2 遍历值

```python
d = {'name': 'Tom', 'age': 18, 'gender': 'Male'}
for value in d.values():
    print(value)  # Tom 18 Male
```



#### 5.5.3 遍历键值对

```python
d = {'name': 'Tom', 'age': 18, 'gender': 'Male'}
for key, value in d.items():
    print(key, value)  # name Tom  age 18  gender Male
```



### 5.6 字典小结

- 字典是键值对的可变容器
- 键必须是不可变类型，值可以是任何类型
- 提供多种方法访问和修改字典
- 支持遍历键、值、键值对

---



## 6. 集合

### 6.1 集合的基本特性

- **唯一性**：不允许重复元素
- **无序性**：不支持索引和切片操作
- **可哈希性**：元素必须是不可变类型



### 6.2 集合的创建

#### 6.2.1 可变集合创建

```python
# 花括号创建
set1 = {1, 2, 3}
# 注意：直接{}创建的是空字典，不是空集合
empty_set = set()
# set()函数创建
set2 = set([1, 2, 3, 3])  # {1, 2, 3}  自动去重
set3 = set("Hello")  # {'H', 'e', 'l', 'o'}  字符串转集合
```



### 6.3 集合的常用方法及运算符

#### 6.3.1 添加元素

```python
set1 = {1, 2, 3}
set1.add(4)  # {1, 2, 3, 4}
```



#### 6.3.2 删除元素

```python
set1 = {1, 2, 3, 4}
set1.remove(3)  # {1, 2, 4}  元素不存在会引发KeyError
set1.discard(5)  # {1, 2, 4}  元素不存在不会引发异常
set1.pop()  # 1  随机删除并返回一个元素
set1.clear()  # {}  清空集合
```



#### 6.3.3 集合运算

```python
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# 交集
print(set1 & set2)  # {3, 4}
print(set1.intersection(set2))  # {3, 4}

# 并集
print(set1 | set2)  # {1, 2, 3, 4, 5, 6}
print(set1.union(set2))  # {1, 2, 3, 4, 5, 6}

# 差集
print(set1 - set2)  # {1, 2}
print(set1.difference(set2))  # {1, 2}

# 对称差集
print(set1 ^ set2)  # {1, 2, 5, 6}
print(set1.symmetric_difference(set2))  # {1, 2, 5, 6}
```



#### 6.3.4 其他方法

```python
set1 = {1, 2, 3}
set2 = {2, 3}
print(len(set1))  # 3  集合长度
print(set2.issubset(set1))  # True  set2是否是set1的子集
print(set1.issuperset(set2))  # True  set1是否是set2的超集
print(set1.copy())  # {1, 2, 3}  浅拷贝
```



### 6.4 不可变集合

```python
# 使用frozenset()函数创建不可变集合
fs = frozenset([1, 2, 3])
# fs.add(4)  # AttributeError: 'frozenset' object has no attribute 'add'
```

#### 注意点

- 不可变集合没有添加、删除元素的方法
- 不可变集合可以作为字典的键或其他集合的元素



### 6.5 集合小结

- 集合是无序的不重复元素集合
- 分为可变集合和不可变集合
- 提供丰富的集合运算和方法
- 适合用于去重、交集、并集等操作

---



## 7. 函数

### 7.1 内置函数

```python
# 输入输出
print("Hello World")  # 输出
name = input("请输入姓名：")  # 输入

# 数值函数
print(abs(-10))      # 10  绝对值
print(divmod(10, 3))  # (3, 1)  商和余数
print(round(3.14159, 2))  # 3.14  四舍五入
print(pow(2, 3))     # 8  幂运算
print(sum([1, 2, 3]))  # 6  求和
print(min(1, 2, 3))  # 1  最小值
print(max(1, 2, 3))  # 3  最大值

# 类型转换
print(int(3.14))     # 3
print(float(3))       # 3.0
print(str(123))       # '123'
print(list((1, 2, 3)))  # [1, 2, 3]
print(tuple([1, 2, 3]))  # (1, 2, 3)
print(dict(name='Tom', age=18))  # {'name': 'Tom', 'age': 18}
print(set([1, 2, 3, 3]))  # {1, 2, 3}

# 序列函数
print(range(5))  # range(0, 5)
print(len([1, 2, 3]))  # 3
print(sorted([3, 1, 2]))  # [1, 2, 3]
print(list(filter(lambda x: x % 2 == 0, [1, 2, 3, 4])))  # [2, 4]
print(list(map(lambda x: x * 2, [1, 2, 3])))  # [2, 4, 6]

# 其他
print(id(1))  # 对象ID
print(type(1))  # <class 'int'>  对象类型
```



### 7.2 自定义函数

#### 定义与调用

```python
def greet(name):
    """这是一个问候函数"""
    return f"Hello, {name}!"

# 调用
print(greet("Tom"))  # 'Hello, Tom!'
```



#### 参数传递

```python
def func(a, b):
    a += 1
    b.append(4)
    return a, b

x = 1
y = [1, 2, 3]
x, y = func(x, y)
print(x)  # 2  不可变对象，值不变
print(y)  # [1, 2, 3, 4]  可变对象，值改变
```

#### 注意点

- Python中函数参数传递是“对象引用传递”
- 不可变对象（数字、字符串、元组）在函数内修改不会影响外部
- 可变对象（列表、字典、集合）在函数内修改会影响外部



### 7.3 函数的特殊参数

#### 7.3.1 默认参数

```python
def func(a, b=10):
    return a + b

print(func(5))    # 15  使用默认值b=10
print(func(5, 20))  # 25  使用传入值b=20
```

#### 注意点

- 默认参数必须放在非默认参数之后
- 默认参数的值在函数定义时计算，不是在调用时计算
- 默认参数应该使用不可变对象，避免使用可变对象（如列表）



#### 7.3.2 关键字参数

```python
def func(a, b):
    return a + b

print(func(a=5, b=10))  # 15
print(func(b=10, a=5))  # 15  关键字参数可以改变顺序
print(func(5, b=10))    # 15  混合使用位置参数和关键字参数
# print(func(a=5, 10))  # SyntaxError  位置参数必须在关键字参数之前
```

#### 注意点

- 关键字参数必须放在位置参数之后
- 可以通过关键字参数改变参数顺序
- 关键字参数提高了代码的可读性



#### 7.3.3 可变长参数

```python
# *args - 接收任意数量的位置参数，返回元组
def func(*args):
    return args

print(func(1, 2, 3))  # (1, 2, 3)

# **kwargs - 接收任意数量的关键字参数，返回字典
def func(**kwargs):
    return kwargs

print(func(name='Tom', age=18))  # {'name': 'Tom', 'age': 18}

# 组合使用
def func(a, *args, **kwargs):
    return a, args, kwargs

print(func(1, 2, 3, name='Tom', age=18))  # (1, (2, 3), {'name': 'Tom', 'age': 18})
```



### 7.4 lambda函数

```python
# 基本形式
add = lambda x, y: x + y
print(add(5, 10))  # 15

# 作为参数使用
lst = [1, 2, 3, 4, 5]
print(list(map(lambda x: x * 2, lst)))  # [2, 4, 6, 8, 10]
print(list(filter(lambda x: x % 2 == 0, lst)))  # [2, 4]
```

#### 注意点

- lambda函数只能包含一个表达式
- lambda函数没有函数名，是匿名函数
- 适合定义简单的一次性函数



### 7.5 变量作用域

#### 局部变量

```python
def func():
    x = 10  # 局部变量
    print(x)

func()  # 10
# print(x)  # NameError: name 'x' is not defined
```



#### 全局变量

```python
x = 10  # 全局变量

def func():
    print(x)  # 访问全局变量

func()  # 10

# 修改全局变量
def func():
    global x  # 声明x为全局变量
    x = 20

func()
print(x)  # 20
```

#### 注意点

- 局部变量只在函数内部有效
- 全局变量在整个程序中有效
- 在函数内修改全局变量需要使用`global`关键字
- `global`关键字不能同时赋值，如`global x = 20`是错误的



### 7.6 递归函数

```python
def factorial(n):
    """计算n的阶乘"""
    if n == 0:
        return 1
    else:
        return n * factorial(n - 1)

print(factorial(5))  # 120
```

#### 注意点

- 递归函数必须有终止条件
- 递归深度不能过大，否则会引发栈溢出
- 递归函数的执行效率较低，可考虑使用迭代代替



### 7.7 main函数

```python
def main():
    print("这是主函数")

if __name__ == "__main__":
    main()
```

#### 说明

- `__name__`是Python的内置变量
- 当直接运行脚本时，`__name__`的值为`__main__`
- 当作为模块导入时，`__name__`的值为模块名
- 使用`if __name__ == "__main__":`可以确保主函数只在直接运行脚本时执行



### 7.8 函数小结

- 函数是组织代码的基本单位
- Python提供丰富的内置函数
- 支持自定义函数，参数传递灵活
- 支持lambda函数、递归函数等高级特性

---

## 8. 面向对象程序设计

### 8.1 类与对象

#### 类的定义

```python
class Person:
    """人这个类"""
    # 类属性
    species = "人类"
    
    # 构造函数
    def __init__(self, name, age):
        # 实例属性
        self.name = name
        self.age = age
    
    # 实例方法
    def greet(self):
        return f"你好，我是{self.name}，今年{self.age}岁。"

# 创建对象
p = Person("Tom", 18)
print(p.greet())  # '你好，我是Tom，今年18岁。'
```

#### 类成员的可访问范围

- **公有成员**：默认，可在类内外访问
- **保护成员**：以单下划线开头（如`_name`），建议仅供类和子类内部使用
- **私有成员**：以双下划线开头（如`__name`），只能在类内部访问，外部无法直接访问
- **特殊成员**：以双下划线开头和结尾（如`__init__`），具有特殊意义



### 8.2 属性

#### 8.2.1 成员属性

```python
class Person:
    def __init__(self, name, age):
        self.name = name  # 公有属性
        self._age = age   # 保护属性
        self.__salary = 5000  # 私有属性
    
    # 访问私有属性的方法
    def get_salary(self):
        return self.__salary

p = Person("Tom", 18)
print(p.name)  # 'Tom'
print(p._age)  # 18  可以访问，但不推荐
# print(p.__salary)  # AttributeError  无法直接访问
print(p.get_salary())  # 5000  通过方法访问私有属性
```



#### 8.2.2 @property装饰器

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self._age = age
    
    @property
    def age(self):
        return self._age
    
    @age.setter
    def age(self, value):
        if value > 0 and value < 120:
            self._age = value
        else:
            raise ValueError("年龄必须在0-120之间")
    
    @age.deleter
    def age(self):
        del self._age

p = Person("Tom", 18)
print(p.age)  # 18  访问属性
p.age = 20    # 修改属性
print(p.age)  # 20
# p.age = 150  # ValueError: 年龄必须在0-120之间
del p.age     # 删除属性
# print(p.age)  # AttributeError: 'Person' object has no attribute '_age'
```



#### 8.2.3 类属性

```python
class Person:
    count = 0  # 类属性
    
    def __init__(self, name):
        self.name = name
        Person.count += 1

print(Person.count)  # 0
p1 = Person("Tom")
print(Person.count)  # 1
p2 = Person("Jerry")
print(Person.count)  # 2
```



#### 8.2.4 特殊属性

| 属性           | 说明           |
| -------------- | -------------- |
| `__dict__`     | 对象的属性字典 |
| `__class__`    | 对象所属的类   |
| `__name__`     | 类名           |
| `__qualname__` | 类的限定名称   |
| `__module__`   | 类所在的模块   |
| `__bases__`    | 类的基类元组   |
| `__mro__`      | 方法解析顺序   |
| `__doc__`      | 类的文档字符串 |



#### 8.2.5 动态添加/删除属性

```python
class Person:
    def __init__(self, name):
        self.name = name

p = Person("Tom")
p.age = 18  # 动态添加属性
print(p.age)  # 18

import types
p.greet = types.MethodType(lambda self: f"你好，我是{self.name}", p)  # 动态添加方法
print(p.greet())  # '你好，我是Tom'

# 动态删除属性
del p.age
# print(p.age)  # AttributeError

# 使用setattr和delattr
setattr(p, "gender", "男")
print(p.gender)  # '男'
delattr(p, "gender")
# print(p.gender)  # AttributeError
```



### 8.3 方法

#### 8.3.1 实例方法

```python
class Person:
    def __init__(self, name):
        self.name = name
    
    def greet(self):  # 实例方法，第一个参数是self
        return f"你好，我是{self.name}"

p = Person("Tom")
print(p.greet())  # '你好，我是Tom'
```



#### 8.3.2 类方法

```python
class Person:
    count = 0
    
    def __init__(self, name):
        self.name = name
        Person.count += 1
    
    @classmethod
    def get_count(cls):  # 类方法，第一个参数是cls
        return cls.count

p1 = Person("Tom")
p2 = Person("Jerry")
print(Person.get_count())  # 2  直接通过类调用
print(p1.get_count())  # 2  通过实例调用
```



#### 8.3.3 静态方法

```python
class Person:
    @staticmethod
    def say_hello():
        return "Hello!"

print(Person.say_hello())  # 'Hello!'  直接通过类调用
p = Person()
print(p.say_hello())  # 'Hello!'  通过实例调用
```



#### 8.3.4 特殊方法

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __str__(self):
        return f"Person(name='{self.name}', age={self.age})"
    
    def __repr__(self):
        return f"Person('{self.name}', {self.age})"
    
    def __len__(self):
        return self.age
    
    def __call__(self):
        return f"{self.name}被调用了"

p = Person("Tom", 18)
print(p)  # Person(name='Tom', age=18)  调用__str__
print(repr(p))  # Person('Tom', 18)  调用__repr__
print(len(p))  # 18  调用__len__
print(p())  # Tom被调用了  调用__call__
```



#### 8.3.5 动态添加/删除方法

```python
import types

class Person:
    def __init__(self, name):
        self.name = name

# 动态添加实例方法
def greet(self):
    return f"你好，我是{self.name}"

p = Person("Tom")
p.greet = types.MethodType(greet, p)
print(p.greet())  # '你好，我是Tom'

# 动态添加类方法
@classmethod
def get_class_name(cls):
    return cls.__name__

Person.get_class_name = get_class_name
print(Person.get_class_name())  # 'Person'

# 动态添加静态方法
@staticmethod
def say_hello():
    return "Hello!"

Person.say_hello = say_hello
print(Person.say_hello())  # 'Hello!'

# 动态删除方法
del p.greet
del Person.get_class_name
del Person.say_hello
```



### 8.4 运算符重载

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)
    
    def __sub__(self, other):
        return Vector(self.x - other.x, self.y - other.y)
    
    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)
    
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y
    
    def __str__(self):
        return f"Vector({self.x}, {self.y})"

v1 = Vector(1, 2)
v2 = Vector(3, 4)
v3 = v1 + v2  # 调用__add__
print(v3)  # Vector(4, 6)
v4 = v1 - v2  # 调用__sub__
print(v4)  # Vector(-2, -2)
v5 = v1 * 2  # 调用__mul__
print(v5)  # Vector(2, 4)
print(v1 == v2)  # False  调用__eq__
```



### 8.5 继承

#### 8.5.1 单继承

```python
class Animal:
    def __init__(self, name):
        self.name = name
    
    def eat(self):
        return f"{self.name}在吃东西"

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)  # 调用父类构造函数
        self.breed = breed
    
    def bark(self):
        return f"{self.name}在汪汪叫"

# 创建子类实例
dog = Dog("Tom", "金毛")
print(dog.eat())  # Tom在吃东西  继承父类方法
print(dog.bark())  # Tom在汪汪叫  子类自己的方法
```



#### 8.5.2 多继承

```python
class A:
    def method(self):
        return "A.method"

class B:
    def method(self):
        return "B.method"

class C(A, B):
    pass

class D(B, A):
    pass

c = C()
print(c.method())  # A.method  MRO顺序：C -> A -> B -> object

d = D()
print(d.method())  # B.method  MRO顺序：D -> B -> A -> object
```

#### 注意点

- Python支持单继承和多继承
- 多继承时，方法解析顺序（MRO）决定了调用哪个父类的方法
- 推荐使用`super()`调用父类方法，不推荐直接使用父类名



### 8.6 多态性

```python
class Animal:
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "汪汪汪"

class Cat(Animal):
    def speak(self):
        return "喵喵喵"

class Bird(Animal):
    def speak(self):
        return "叽叽喳喳"

def make_animal_speak(animal):
    return animal.speak()

# 多态性示例
dog = Dog()
cat = Cat()
bird = Bird()

print(make_animal_speak(dog))  # 汪汪汪
print(make_animal_speak(cat))  # 喵喵喵
print(make_animal_speak(bird))  # 叽叽喳喳
```



### 8.7 面向对象程序设计小结

- 面向对象程序设计的核心是类和对象
- 类封装了数据和方法
- 支持继承、多态等特性
- 提供丰富的特殊方法和装饰器

---



## 9. 文件和目录操作

### 9.1 文件类型与基本操作

#### 9.1.1 文件类型

- **文本文件**：由字符组成，如.txt、.py文件
- **二进制文件**：由字节组成，如图片、音频、视频文件



#### 9.1.2 文件基本操作

- **打开文件**：使用`open()`函数
- **读写文件**：使用文件对象的方法
- **关闭文件**：使用`close()`方法或`with`语句

```python
# 基本文件操作流程
f = open('file.txt', 'r')  # 打开文件
content = f.read()         # 读取文件内容
f.close()                  # 关闭文件

# 使用with语句（推荐）
with open('file.txt', 'r') as f:
    content = f.read()     # 自动关闭文件
```



### 9.2 文件打开模式

#### 9.2.1 基本模式

| 模式 | 含义             | 文件不存在时       |
| ---- | ---------------- | ------------------ |
| r    | 只读             | 报错               |
| w    | 只写             | 创建新文件         |
| a    | 追加             | 创建新文件         |
| +    | 读写             | 与其他模式组合使用 |
| b    | 二进制模式       | 与其他模式组合使用 |
| t    | 文本模式（默认） | 与其他模式组合使用 |



#### 9.2.2 常用组合模式

- **r+**：读写模式，文件指针位于起始位置
- **w+**：读写模式，先清空文件内容
- **a+**：读写模式，文件指针位于末尾
- **rb**：二进制只读模式
- **wb**：二进制只写模式
- **ab**：二进制追加模式
- **rb+**：二进制读写模式
- **wb+**：二进制读写模式，先清空内容
- **ab+**：二进制追加模式，指针位于末尾

#### 注意点

- `+`不能单独使用，必须与其他模式组合
- `r`、`r+`、`rb`、`rb+`要求文件必须存在
- `w`、`w+`、`wb`、`wb+`、`a`、`a+`、`ab`、`ab+`在文件不存在时会创建新文件



### 9.3 文件读写方法

#### 9.3.1 读取方法

```python
with open('file.txt', 'r') as f:
    content = f.read()         # 读取整个文件
    line = f.readline()        # 读取一行
    lines = f.readlines()      # 读取所有行，返回列表
```



#### 9.3.2 写入方法

```python
with open('file.txt', 'w') as f:
    f.write('Hello World')     # 写入字符串
    f.writelines(['Line 1\n', 'Line 2\n'])  # 写入多行
```



#### 9.3.3 其他方法

```python
with open('file.txt', 'r') as f:
    pos = f.tell()             # 获取当前指针位置
    f.seek(0)                  # 移动指针到文件开头
    f.seek(0, 2)               # 移动指针到文件末尾（仅二进制模式）
    f.flush()                  # 刷新缓冲区
```

#### 注意点

- 只有以二进制模式(`b`)打开的文件，才允许文件指针向后移动
- `seek()`方法的`whence`参数取1和2的用法只能在二进制文件中使用
- `write()`只能将字符串写入文件



#### 9.3.4 struct模块（二进制数据处理）

```python
import struct

# 将数据打包为二进制
packed_data = struct.pack('i f s', 1, 3.14, b'hello')
print(packed_data)

# 将二进制数据解包
unpacked_data = struct.unpack('i f s', packed_data)
print(unpacked_data)

# 计算格式字符串的大小
size = struct.calcsize('i f s')
print(size)
```



#### 9.3.5 pickle模块（对象序列化）

```python
import pickle

# 将对象序列化为二进制文件
data = {'name': 'Tom', 'age': 18, 'scores': [90, 85, 95]}
with open('data.pkl', 'wb') as f:
    pickle.dump(data, f)

# 从二进制文件反序列化对象
with open('data.pkl', 'rb') as f:
    loaded_data = pickle.load(f)
    print(loaded_data)
```



### 9.4 CSV文件操作

```python
import csv

# 写入CSV文件
with open('data.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['name', 'age', 'city'])  # 写入表头
    writer.writerows([
        ['Tom', 18, 'Beijing'],
        ['Jerry', 20, 'Shanghai']
    ])  # 写入多行

# 读取CSV文件
with open('data.csv', 'r') as f:
    reader = csv.reader(f)
    for row in reader:
        print(row)  # 每行是一个列表

# 使用DictWriter和DictReader
with open('data.csv', 'w', newline='') as f:
    fieldnames = ['name', 'age', 'city']
    writer = csv.DictWriter(f, fieldnames=fieldnames)
    writer.writeheader()  # 写入表头
    writer.writerow({'name': 'Tom', 'age': 18, 'city': 'Beijing'})
    writer.writerow({'name': 'Jerry', 'age': 20, 'city': 'Shanghai'})

with open('data.csv', 'r') as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row['name'], row['age'], row['city'])
```

#### 注意点

- 参数`newline=''`不能省略，否则会出现空行
- `csv.reader`返回的每个元素是一个列表



### 9.5 Excel文件操作

```python
import openpyxl

# 创建工作簿
wb = openpyxl.Workbook()
ws = wb.active  # 获取活动工作表
ws.title = 'Sheet1'  # 设置工作表名称

# 写入数据
ws['A1'] = 'Name'
ws['B1'] = 'Age'
ws['C1'] = 'City'
ws.append(['Tom', 18, 'Beijing'])
ws.append(['Jerry', 20, 'Shanghai'])

# 保存工作簿
wb.save('data.xlsx')

# 读取工作簿
wb = openpyxl.load_workbook('data.xlsx')
print(wb.sheetnames)  # 获取所有工作表名称
ws = wb['Sheet1']  # 获取指定工作表

# 读取单元格数据
print(ws['A1'].value)
print(ws.cell(row=1, column=1).value)

# 遍历行和列
for row in ws.iter_rows(min_row=1, max_row=3, min_col=1, max_col=3):
    for cell in row:
        print(cell.value, end=' ')
    print()
```



### 9.6 图像文件操作

```python
from PIL import Image

# 打开图像
img = Image.open('image.jpg')

# 查看图像信息
print(img.size)      # 图像尺寸
print(img.mode)      # 颜色模式
print(img.format)    # 图像格式

# 图像操作
img_resized = img.resize((200, 200))  # 调整大小
img_rotated = img.rotate(45)           # 旋转
img_cropped = img.crop((100, 100, 300, 300))  # 裁剪

# 保存图像
img_resized.save('image_resized.jpg')
img_rotated.save('image_rotated.jpg')
img_cropped.save('image_cropped.jpg')
```



### 9.7 JSON文件操作

```python
import json

# Python对象转JSON字符串
person = {'name': 'Tom', 'age': 18, 'city': 'Beijing'}
json_str = json.dumps(person, ensure_ascii=False)  # ensure_ascii=False保留中文

# 写入JSON文件
with open('person.json', 'w', encoding='utf-8') as f:
    json.dump(person, f, ensure_ascii=False, indent=4)  # indent=4美化输出

# 读取JSON文件
with open('person.json', 'r', encoding='utf-8') as f:
    person_dict = json.load(f)
    print(person_dict['name'], person_dict['age'])
```



### 9.8 os和os.path模块

```python
import os

# 获取当前工作目录
cwd = os.getcwd()
print(cwd)

# 改变工作目录
os.chdir('d:\\')

# 创建目录
os.mkdir('test_dir')
os.makedirs('test_dir1\\test_dir2')  # 创建多级目录

# 删除目录
os.rmdir('test_dir')
os.removedirs('test_dir1\\test_dir2')  # 删除多级目录（需为空）

# 列出目录内容
files = os.listdir('.')

# 遍历目录树
for root, dirs, files in os.walk('.'):
    for file in files:
        print(os.path.join(root, file))

# 文件路径操作
file_path = os.path.join('dir', 'file.txt')
print(os.path.exists(file_path))  # 检查文件/目录是否存在
print(os.path.isfile(file_path))  # 检查是否为文件
print(os.path.isdir(file_path))   # 检查是否为目录
print(os.path.abspath(file_path)) # 获取绝对路径
print(os.path.split(file_path))   # 分割路径和文件名
print(os.path.splitext(file_path))  # 分割文件名和扩展名

# 文件信息
print(os.path.getsize(file_path))  # 文件大小
print(os.path.getatime(file_path))  # 最后访问时间
print(os.path.getmtime(file_path))  # 最后修改时间
```



### 9.9 shutil模块

```python
import shutil

# 复制文件
shutil.copyfile('src.txt', 'dst.txt')
shutil.copy('src.txt', 'dst.txt')  # 保留元数据

# 复制目录
shutil.copytree('src_dir', 'dst_dir')

# 移动文件/目录
shutil.move('src.txt', 'dst_dir/')

# 删除目录（递归）
shutil.rmtree('dir_to_delete')

# 创建压缩包
shutil.make_archive('archive', 'zip', 'dir_to_archive')

# 解压压缩包
shutil.unpack_archive('archive.zip', 'dst_dir')
```

---



## 10. 异常处理

**异常处理**是Python中处理程序运行时错误的机制，通过捕获和处理异常，可以提高程序的健壮性和用户体验，防止程序因错误而意外终止。

### 10.1 异常类

Python中的异常类层次结构如下：

- `BaseException`：所有异常的基类
  - `Exception`：所有非系统退出异常的基类
    - `SyntaxError`：语法错误
    - `TypeError`：类型错误
    - `ValueError`：值错误
    - `NameError`：名称错误
    - `IndexError`：索引错误
    - `KeyError`：键错误
    - `IOError`/`OSError`：输入输出错误
    - `ZeroDivisionError`：除零错误



### 10.2 异常处理结构

**异常处理结构**是Python中捕获和处理异常的核心语法，通过`try-except-else-finally`结构可以灵活控制异常的处理流程和资源的释放。

```python
try:
    # 可能引发异常的代码
    result = 10 / 0
except ZeroDivisionError as e:
    # 处理特定异常
    print(f'除零错误：{e}')
except (TypeError, ValueError) as e:
    # 处理多个异常
    print(f'类型或值错误：{e}')
except Exception as e:
    # 处理所有其他异常
    print(f'发生错误：{e}')
else:
    # 没有异常时执行
    print('执行成功')
finally:
    # 无论是否发生异常都执行
    print('清理工作')
```



### 10.3 抛出异常

**抛出异常**是Python中主动触发异常的机制，通过`raise`语句可以在程序中自定义异常的触发条件，将错误信息传递给上层调用者。

```python
# 抛出内置异常
raise ValueError('值错误')

# 抛出异常并指定原因
raise ZeroDivisionError('除数不能为零')
```



### 10.4 自定义异常

**自定义异常**是Python中扩展异常体系的机制，通过创建自定义异常类，可以为特定业务场景定义专门的异常类型，提高代码的可读性和可维护性。

```python
# 自定义异常类
class MyException(Exception):
    def __init__(self, message):
        self.message = message
        super().__init__(self.message)

# 使用自定义异常
try:
    raise MyException('自定义异常')
except MyException as e:
    print(f'捕获自定义异常：{e}')
```



### 10.5 断言

**断言**是Python中用于调试的机制，通过`assert`语句可以在程序中插入检查点，验证某个条件是否为真，如果条件为假则触发`AssertionError`异常。

```python
# 断言语句
x = 5
assert x > 0, 'x必须大于0'

# 断言失败时会引发AssertionError异常
try:
    assert x < 0, 'x必须小于0'
except AssertionError as e:
    print(f'断言失败：{e}')
```

#### 注意点

- 断言主要用于调试阶段，不应该用于生产环境的错误处理
- 可以通过`-O`参数运行Python来禁用断言

---



## 11. 常用库

### 11.1 库的导入方式

**库的导入方式**是Python中使用外部库或模块的基础机制，通过不同的导入方式可以灵活控制模块中函数和变量的访问范围和命名空间。

#### 11.1.1 导入整个库

```python
import math
import random as rd
```



#### 11.1.2 导入特定函数

```python
from math import ceil, sqrt
from math import *  # 导入所有函数（不推荐）
from random import randint as ri
```



#### 11.1.3 第三方库安装

```bash
pip install numpy pandas matplotlib
pip list  # 查看已安装的库
```



### 11.2 math库

**math库**是Python的标准数学库，提供了丰富的数学函数和常量，用于进行各种数学计算。该库包含了基本运算、三角函数、指数对数、组合数学等多种功能，适用于科学计算、数据分析等场景。

```python
import math

print(math.ceil(3.14))      # 向上取整
print(math.floor(3.14))     # 向下取整
print(math.sqrt(16))        # 平方根
print(math.factorial(5))    # 阶乘
print(math.gcd(12, 18))     # 最大公约数
print(math.exp(1))          # e的1次方
print(math.log(10))         # 自然对数
print(math.pow(2, 3))       # 2的3次方
print(math.sin(math.pi/2))  # 正弦函数
```



### 11.3 random库

**random库**是Python的标准随机数生成库，提供了各种随机数生成和随机选择功能。该库可用于模拟、游戏开发、数据分析中的随机抽样等场景，能够生成不同分布的随机数。

```python
import random

# 设置随机数种子
random.seed(123)

print(random.randint(1, 10))        # 1-10之间的整数
print(random.randrange(0, 10, 2))   # 0-10之间的偶数
print(random.uniform(1, 10))        # 1-10之间的浮点数
print(random.choice(['a', 'b', 'c']))  # 随机选择一个元素

lst = [1, 2, 3, 4, 5]
random.shuffle(lst)                  # 打乱列表
print(lst)

print(random.sample([1, 2, 3, 4, 5], 3))  # 随机采样3个元素
```



### 11.4 datetime库

**datetime库**是Python的标准日期时间处理库，提供了用于处理日期、时间、时间间隔和时间戳的类和函数。该库能够满足各种日期时间的计算、格式化和转换需求，是处理时间相关数据的核心工具。

```python
import datetime

# 获取当前时间
now = datetime.datetime.now()
utc_now = datetime.datetime.utcnow()

# 创建datetime对象
dt = datetime.datetime(2023, 12, 25, 10, 30, 45)

# 获取日期和时间
print(dt.date())      # 日期部分
print(dt.time())      # 时间部分

# 格式化输出
print(dt.strftime('%Y-%m-%d %H:%M:%S'))  # 2023-12-25 10:30:45

# 时间差
delta = datetime.timedelta(days=7, hours=2)
new_dt = dt + delta
```



### 11.5 NumPy库

**NumPy库**（Numerical Python）是Python科学计算的核心库，提供了高性能的多维数组对象（ndarray）和丰富的数组操作函数。该库支持矢量化运算和广播机制，能够高效处理大规模数据，是数据分析、机器学习、图像处理等领域的基础依赖库。

```python
import numpy as np

# 创建数组
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.arange(0, 10, 2)
arr3 = np.linspace(0, 1, 5)
arr4 = np.zeros((2, 3))
arr5 = np.ones((2, 3))
arr6 = np.eye(3)  # 单位矩阵

# 数组属性
print(arr1.shape)   # 形状
print(arr1.ndim)    # 维度
print(arr1.size)    # 元素个数
print(arr1.dtype)   # 数据类型

# 数组运算
print(arr1 + 2)     # 加标量
print(arr1 * 2)     # 乘标量
print(arr1 + arr2)  # 数组相加
print(arr1 * arr2)  # 数组相乘（元素级）

# 索引和切片
print(arr1[0])      # 索引
print(arr1[1:4])    # 切片
print(arr1[arr1 > 2])  # 条件索引

# 数组操作
print(np.reshape(arr1, (5, 1)))  # 形状改变
print(np.transpose(arr4))        # 转置
print(np.concatenate((arr4, arr5), axis=0))  # 纵向拼接
```



### 11.6 pandas库

**pandas库**是Python用于数据处理和分析的强大库，提供了高效的数据结构（如Series和DataFrame）和丰富的数据操作功能。该库支持数据导入导出、数据清洗、数据转换、数据聚合和统计分析等操作，是数据分析和数据科学领域的必备工具。

```python
import pandas as pd

# Series对象
s = pd.Series([1, 2, 3, 4, 5], index=['a', 'b', 'c', 'd', 'e'])

# DataFrame对象
df = pd.DataFrame({
    'name': ['Tom', 'Jerry', 'Alice'],
    'age': [18, 20, 22],
    'city': ['Beijing', 'Shanghai', 'Guangzhou']
})

# 数据导入
# df = pd.read_csv('data.csv')
# df = pd.read_excel('data.xlsx')

# 数据导出
# df.to_csv('output.csv', index=False)
# df.to_excel('output.xlsx', index=False)

# 数据访问
print(df['name'])       # 列访问
print(df.loc[0])        # 行访问（标签）
print(df.iloc[0])       # 行访问（位置）
print(df.at[0, 'name']) # 单元格访问（标签）
print(df.iat[0, 0])     # 单元格访问（位置）

# 数据处理
print(df.isna())        # 检查缺失值
print(df.fillna(0))     # 填充缺失值
print(df.dropna())      # 删除缺失值
print(df.drop_duplicates())  # 删除重复行
print(df.replace(18, 19))    # 替换值

# 数据分析
print(df.info())        # 数据信息
print(df.describe())    # 描述性统计
print(df.head())        # 前5行
print(df.tail())        # 后5行
print(df['age'].value_counts())  # 值计数

# 分组聚合
grouped = df.groupby('city')
print(grouped['age'].mean())  # 按城市分组计算平均年龄
```



### 11.7 matplotlib库

**matplotlib库**是Python的绘图库，提供了丰富的绘图功能，可以创建各种类型的图表，如折线图、柱状图、散点图、直方图等。该库支持自定义图表样式、添加注释和图例，是数据可视化和科学绘图的重要工具。

```python
import matplotlib.pyplot as plt

# 设置中文显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 准备数据
x = [1, 2, 3, 4, 5]
y = [2, 4, 6, 8, 10]

# 创建图形
plt.figure(figsize=(8, 6))

# 绘制折线图
plt.plot(x, y, color='red', linestyle='--', linewidth=2, marker='o', label='折线图')

# 绘制柱状图
# plt.bar(x, y, color='blue', width=0.5)

# 设置坐标轴和标题
plt.xlabel('X轴', fontsize=12)
plt.ylabel('Y轴', fontsize=12)
plt.title('示例图表', fontsize=14)

# 设置坐标轴范围
plt.xlim(0, 6)
plt.ylim(0, 12)

# 添加网格线
plt.grid(True, linestyle='--', alpha=0.7)

# 添加图例
plt.legend()

# 显示图表
plt.show()
```

---



## 总结

本笔记系统地整理了Python的核心知识点，包括：

- 字符串、列表、元组、字典、集合等数据类型
- 数据类型转换和常用函数
- 函数的定义、调用和参数传递
- 面向对象程序设计的核心概念
- 文件和目录操作（包括文本/二进制文件、CSV、Excel、JSON、图像文件等）
- 异常处理机制（包括异常类、try-except结构、自定义异常等）
- 常用库（包括标准库和第三方库如NumPy、pandas、matplotlib等）
