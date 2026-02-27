---
title: Python程序设计习题——函数
date: 2026-01-18 00:15:37
tags:
- Python
categories:
- Python语言程序设计
description: " "
---

# 函数

# 选择题

## 1. 题目

```python
函数如下：
def showNnumber(numbers):
    for n in numbers:
         print(n)
下面哪些在调用函数时会报错（  ）

A.showNumer([2,4,5])
B.showNnumber(‘abcesf’)
C.showNnumber(3.4)
D.showNumber((12,4,5))
```

**答案：C**

**选项A**：传入列表[2,4,5]，列表是可迭代对象，能够被for循环遍历，不报错。

**选项B**：传入字符串'abcesf'，字符串是可迭代对象，能够被for循环遍历，不报错。

**选项C**：传入浮点数3.4，浮点数是不可迭代对象，无法被for循环遍历，会报错，这是唯一会报错的选项。

**选项D**：传入元组(12,4,5)，元组是可迭代对象，能够被for循环遍历，不报错。

&nbsp;

## 2. 题目

```python
函数如下： 
def chanageInt(number2):
    number2 = number2+1
    print("changeInt: number2= ",number2)
#调用
number1 = 2
chanageInt(number1)
print("number:",number1)
显示结果哪项是正确的（   ）

A.
number: 2
changeInt: number2= 3
B.
changeInt: number2= 3
number: 2
C.
changeInt: number2= 3
number: 3
D.
number: 2
changeInt: number2= 2
```

**答案：B**

**选项A**：执行顺序错误，应该先调用函数打印函数内部信息，再打印全局变量，错误。

**选项B**：执行顺序正确，函数内部打印changeInt: number2= 3，然后打印全局变量number: 2（整数是不可变类型，函数内修改不影响外部变量），正确。

**选项C**：错误认为外部变量number1会变为3，整数是不可变类型，函数内修改不影响外部变量，错误。

**选项D**：执行顺序错误，且函数内部number2的值错误，错误。

&nbsp;

## 3. 题目

```python
调用以下函数返回的值(  ) 
def myfun(): 
	pass 

A. 空字符串 
B. 0 
C. 出错不能运行 
D. None
```

**答案：D**

**选项A**：空字符串是明确的字符串类型值，函数未显式返回，不会默认返回空字符串，错误。

**选项B**：0是整数类型值，函数未显式返回，不会默认返回0，错误。

**选项C**：pass语句让函数定义合法，调用函数不会出错，错误。

**选项D**：Python函数无显式return语句时，默认返回None，正确。

&nbsp;

## 4. 题目

```python
(多选)
已知函数定义 
def func(*p):
    return sum(p)
那么下列表达式能得到正确结果的是（  ）。

A.func(1, 2, 3, 4)
B.func(*{1, 2, 3, 4})
C.func([1, 2, 3, 4])
D.func(*(1, 2, 3, 4))
```

**答案：ABD**

**选项A**：直接传入4个整数位置实参，*p打包为元组(1,2,3,4)，sum计算结果为10，正确。

**选项B**：*对集合解包为4个独立整数实参，*p打包为元组，sum计算结果为10，正确。

**选项C**：直接传入列表[1,2,3,4]，*p打包为元组([1,2,3,4])，sum无法对列表求和，错误。

**选项D**：*对元组解包为4个独立整数实参，*p打包为元组，sum计算结果为10，正确。

&nbsp;

## 5. 题目

```python
(多选)
函数如下：
def chanageList(list): 
    list.append(" end")
    print("list",list) 
#调用
strs =['1','2'] 
chanageList(strs) 
print("strs",strs)
下面对 strs 和 list 的值输出正确的是(      )


A.list ['1','2',’end’]
B.strs  ['1','2',’end’]
C.list  ['1','2']
D.strs ['1','2']
```

**答案：AB**

**选项A**：函数内调用append(" end")后，列表变为['1','2','end']，print输出list ['1','2','end']，正确。

**选项B**：列表是可变对象，函数内修改会影响外部原始对象，因此strs的值也是['1','2','end']，正确。

**选项C**：函数内已经调用append(" end")，列表内容已修改，错误。

**选项D**：列表是可变对象，函数内修改会影响外部原始对象，strs的值已修改，错误。

&nbsp;

# 判断题

## 1. 题目

```python
在定义函数时，某个参数名字前面带有一个*符号表示可变长度参数，可以接收任意多个普通实参并存放于一个元组之中。  (   )
```

**答案：对**

**解析**：在函数定义中，参数名前加一个`*`，表示该参数用于接收任意数量的位置实参，将这些实参按顺序打包成一个元组，参数名指向这个元组。

&nbsp;

## 2. 题目

```python
定义函数时，即使该函数不需要接收任何参数，也必须保留一对空的圆括号来表示这是一个函数。(    )
```

**答案：对**

**解析**：在Python中，函数的定义格式为`def 函数名(参数列表):`，其中圆括号`()`是语法必需项，用于明确标识这是一个函数定义，即使函数不需要接收任何参数，也必须保留空的圆括号。

&nbsp;

# 填空题

## 1. 题目

```python
表达式 list(filter(lambda x:x>2, [0,1,2,3,0,0])) 的值为______
```

**答案：[3]**

**解析**：`filter(lambda x:x>2, [0,1,2,3,0,0])`过滤出大于2的元素（只有3），返回迭代器；`list()`将迭代器转换为列表[3]。

&nbsp;

## 2. 题目

```python
已知formatter = 'good {0}'.format，那么表达式list(map(formatter, ['morning','afternoon']))的值为_______
```

**答案：['good morning','good afternoon']**

**解析**：`formatter`绑定了字符串'good {0}'的format方法，作用是接收一个字符串参数并返回格式化字符串；`map()`将formatter应用到['morning','afternoon']的每个元素，得到['good morning','good afternoon']。

&nbsp;

## 3. 题目

```python
已知函数定义 def func(**p): return sum(p.values())， 那么表达式 func(x=1, y=2, z=3) 的值为_______
```

**答案：6**

**解析**：`**p`将关键字参数收集为字典{'x':1, 'y':2, 'z':3}；`sum(p.values())`对字典的值求和，结果为1+2+3=6。

&nbsp;

## 4. 题目

```python
已知 g = lambda x, y=3, z=5: x*y*z，则语句 print(g(1)) 的输出结果为______
```

**答案：15**

**解析**：lambda函数定义了默认参数y=3和z=5，调用g(1)时仅传入x=1，y和z使用默认值；计算1*3*5=15。

&nbsp;

## 5. 题目

```python
已知函数定义 def func(**p): return ''.join(sorted(p)) 那么表达式 func(x=1, z=3, y=2)的值为____
```

**答案：'xyz'**

**解析**：`**p`将关键字参数收集为字典{'x':1, 'z':3, 'y':2}；`sorted(p)`对字典的键进行排序得到['x','y','z']；`''.join()`将排序后的键拼接为字符串'xyz'。

&nbsp;

## 6. 题目

```python
如果函数中没有return语句或者return语句不带任何返回值，那么该函数的返回值为____
```

**答案：None**

**解析**：Python规定，所有函数都有返回值，无论是否显式书写return语句。如果函数中没有return语句或return语句不带任何返回值，函数执行结束后都会默认返回None。

&nbsp;

## 7. 题目

```python
表达式 list(map(list,zip(*[[1, 2, 3], [4, 5, 6]]))) 的值为_____
```

**答案：[[1,4],[2,5],[3,6]]**

**解析**：`*[[1,2,3],[4,5,6]]`解包为[1,2,3]和[4,5,6]；`zip()`按位置打包为((1,4),(2,5),(3,6))；`map(list, ...)`将每个元组转换为列表；`list()`转换为最终的二维列表[[1,4],[2,5],[3,6]]。

&nbsp;

## 8. 题目

```python
下面的代码输出结果为_______。
def demo():
    x = 5
x = 3
demo()
print(x)
```

**答案：3**

**解析**：函数内部的x是局部变量，作用域仅局限于函数内部；外部的x是全局变量，值为3；函数调用结束后，打印的是全局变量x的值3。

&nbsp;

## 9. 题目

```python
表达式 list(map(lambda x: x+5, [1, 2, 3, 4, 5])) 的值为______
```

**答案：[6,7,8,9,10]**

**解析**：`lambda x: x+5`定义了给每个元素加5的规则；`map()`将该规则应用到[1,2,3,4,5]的每个元素，得到[6,7,8,9,10]。

&nbsp;

## 10. 题目

```python
已知formatter = 'good {0}'.format,那么表达式list(map(formatter, ['morning']))的值为_______
```

**答案：['good morning']**

**解析**：`formatter`绑定了字符串'good {0}'的format方法；`map()`将formatter应用到['morning']的元素，得到['good morning']。

&nbsp;

## 11. 题目

```python
已知 f = lambda x: 5，那么表达式 f(3)的值为_______
```

**答案：5**

**解析**：该lambda函数无论传入什么参数，都固定返回5，因此f(3)的值为5。

&nbsp;

## 12. 题目

```python
已知函数定义 def demo(x, y, op): return eval(str(x)+op+str(y)) 那么表达式demo(3, 5, '*')的值为______
```

**答案：15**

**解析**：`str(x)+op+str(y)`拼接为字符串'3*5'；`eval()`执行该字符串表达式，计算3\*5=15。

&nbsp;

## 13. 题目

```python
已知有函数定义 def demo(*p): return sum(p) 那么表达式 demo(1, 2, 3, 4) 的值为_______
```

**答案：10**

**解析**：`*p`将位置参数收集为元组(1,2,3,4)；`sum()`对元组的元素求和，结果为1+2+3+4=10。

&nbsp;
