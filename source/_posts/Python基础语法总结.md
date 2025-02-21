---
title: Python基础语法总结
date: 2025-02-21
categories:
  - 语言
  - 编程语言
  - python
tags:
  - Python
  - 编程语言
  - 入门
  - 教程
  - 后端
  - 数据处理
---

# Python基础语法总结

## 基本概念

### 变量和数据类型

Python 是动态类型语言，变量无需声明类型：

```python
# 数字类型
number = 42          # 整数
float_number = 3.14  # 浮点数
complex_num = 1 + 2j # 复数

# 字符串
text = "Hello, Python!"
multi_line = """
多行
字符串
"""

# 布尔值
is_true = True
is_false = False

# 空值
empty = None

# 列表
my_list = [1, 2, 3, 4, 5]

# 字典
my_dict = {"name": "Python", "type": "语言"}
```

## 数据结构

### 列表(List)
可变序列，支持任意类型元素：

```python
# 列表创建
my_list = [1, 2, 3, 4, 5]
mixed_list = [1, "hello", 3.14, [1, 2]]

# 列表操作
my_list.append(6)        # 添加元素
my_list.extend([7, 8])   # 扩展列表
my_list.insert(0, 0)     # 插入元素
my_list.remove(3)        # 删除元素
popped = my_list.pop()   # 弹出元素
my_list.sort()           # 排序
my_list.reverse()        # 反转

# 列表切片
first_three = my_list[:3]    # 前三个元素
last_three = my_list[-3:]    # 后三个元素
step_two = my_list[::2]      # 步长为2

# 列表推导式
squares = [x**2 for x in range(10)]
even_nums = [x for x in range(10) if x % 2 == 0]
```

### 元组(Tuple)
不可变序列：

```python
# 元组创建
my_tuple = (1, 2, 3)
single_tuple = (1,)      # 单元素元组需要逗号

# 元组解包
x, y, z = my_tuple
a, *rest = (1, 2, 3, 4)  # rest = [2, 3, 4]

# 元组用作字典键
coord_dict = {(0, 0): 'origin', (1, 1): 'point'}
```

### 字典(Dictionary)
键值对映射：

```python
# 字典创建
my_dict = {'name': 'Python', 'version': 3.9}
dict_from_items = dict(name='Python', version=3.9)

# 字典操作
my_dict['new_key'] = 'value'     # 添加/更新
value = my_dict.get('key', 'default')  # 安全获取
my_dict.update({'key1': 'val1', 'key2': 'val2'})  # 批量更新
del my_dict['key']               # 删除键值对

# 字典遍历
for key in my_dict:              # 遍历键
    print(key)

for key, value in my_dict.items():  # 遍历键值对
    print(f"{key}: {value}")

# 字典推导式
square_dict = {x: x**2 for x in range(5)}
```

### 集合(Set)
无序不重复集合：

```python
# 集合创建
my_set = {1, 2, 3, 3}    # 重复元素会被去除
empty_set = set()        # 创建空集合

# 集合操作
my_set.add(4)           # 添加元素
my_set.remove(2)        # 删除元素(不存在会报错)
my_set.discard(2)       # 删除元素(不存在不报错)

# 集合运算
set1 = {1, 2, 3}
set2 = {3, 4, 5}
union = set1 | set2             # 并集
intersection = set1 & set2      # 交集
difference = set1 - set2        # 差集
symmetric_diff = set1 ^ set2    # 对称差集
```

## 数据类型转换

### 基本转换
```python
# 字符串转换
str_num = "123"
num = int(str_num)       # 字符串转整数
float_num = float("3.14")  # 字符串转浮点数
str_list = str([1, 2, 3])  # 列表转字符串

# 容器类型转换
my_list = list("hello")     # 字符串转列表
my_tuple = tuple([1, 2, 3]) # 列表转元组
my_set = set([1, 2, 2, 3])  # 列表转集合(去重)

# 字典转换
dict_items = dict([('a', 1), ('b', 2)])  # 二元组列表转字典
```

## 高级特性

### 遍历技巧
```python
# enumerate 用于同时获取索引和值
for i, value in enumerate(['a', 'b', 'c']):
    print(f"索引 {i}: 值 {value}")

# zip 用于同时遍历多个序列
names = ['Alice', 'Bob', 'Charlie']
ages = [24, 50, 18]
for name, age in zip(names, ages):
    print(f"{name} is {age} years old")

# sorted 排序遍历
# key 参数指定排序依据
data = [('Alice', 24), ('Bob', 50), ('Charlie', 18)]
for name, age in sorted(data, key=lambda x: x[1]):  # 按年龄排序
    print(f"{name}: {age}")

# filter 过滤遍历
numbers = [1, 2, 3, 4, 5, 6]
even_numbers = filter(lambda x: x % 2 == 0, numbers)
for num in even_numbers:
    print(num)

# map 转换遍历
squares = map(lambda x: x**2, numbers)
for square in squares:
    print(square)
```

### 控制流
```python
# if 语句
if condition:
    do_something()
elif other_condition:
    do_other_thing()
else:
    do_default()

# for 循环
for item in items:
    process(item)
    if should_break:
        break           # 提前退出循环
    if should_skip:
        continue        # 跳过当前迭代

# while 循环
while condition:
    do_something()
```

### 函数定义
```python
def greet(name, greeting="Hello"):
    """函数文档字符串：描述函数功能"""
    return f"{greeting}, {name}!"

# 带类型提示的函数定义
def add_numbers(a: int, b: int) -> int:
    return a + b

# 可变参数函数
def print_args(*args, **kwargs):
    for arg in args:        # 位置参数
        print(arg)
    for key, value in kwargs.items():  # 关键字参数
        print(f"{key}: {value}")
```

## 常用方法总结

### 列表方法
```python
# 创建示例列表
fruits = ['apple', 'banana', 'orange']
numbers = [1, 2, 3, 4, 5]

# append(x): 在列表末尾添加元素
fruits.append('grape')               # ['apple', 'banana', 'orange', 'grape']

# extend(iterable): 使用可迭代对象扩展列表
fruits.extend(['mango', 'pear'])     # ['apple', 'banana', 'orange', 'grape', 'mango', 'pear']

# insert(i, x): 在指定位置插入元素
fruits.insert(1, 'kiwi')            # ['apple', 'kiwi', 'banana', 'orange', 'grape', 'mango', 'pear']

# remove(x): 删除首次出现的指定元素
fruits.remove('banana')              # ['apple', 'kiwi', 'orange', 'grape', 'mango', 'pear']

# pop([i]): 删除并返回指定位置的元素
last_fruit = fruits.pop()            # 返回 'pear'
second_fruit = fruits.pop(1)         # 返回 'kiwi'

# clear(): 清空列表
numbers.clear()                      # []

# index(x): 返回首次出现的指定元素的索引
position = fruits.index('orange')    # 返回 'orange' 的索引

# count(x): 返回指定元素出现的次数
fruits.extend(['apple', 'apple'])
apple_count = fruits.count('apple')  # 统计 'apple' 出现次数

# sort(): 对列表进行排序
fruits.sort()                        # 按字母顺序排序
fruits.sort(reverse=True)            # 按字母顺序降序排序

# reverse(): 反转列表
fruits.reverse()                     # 反转当前列表顺序

# copy(): 返回列表的浅复制
fruits_copy = fruits.copy()          # 创建新的列表副本
```

### 字典方法
```python
# 创建示例字典
student = {
    'name': 'Alice',
    'age': 20,
    'grades': {'math': 90, 'english': 85}
}

# get(key[, default]): 安全地获取值
name = student.get('name')           # 返回 'Alice'
score = student.get('score', 0)      # 键不存在，返回默认值 0

# setdefault(key[, default]): 如果键不存在则设置默认值
email = student.setdefault('email', 'alice@example.com')  # 添加新键值对并返回值

# update([other]): 更新字典
student.update({'phone': '123-456-7890', 'age': 21})  # 更新多个键值对

# pop(key[, default]): 删除并返回指定键的值
phone = student.pop('phone')         # 删除并返回 phone 值
score = student.pop('score', None)   # 键不存在，返回 None

# popitem(): 删除并返回最后插入的键值对
last_item = student.popitem()        # 返回并删除最后一项

# clear(): 清空字典
student.clear()                      # {}

# 视图操作
student = {'name': 'Bob', 'age': 22, 'major': 'CS'}
keys = student.keys()                # 获取所有键
values = student.values()            # 获取所有值
items = student.items()              # 获取所有键值对

# 字典视图会随字典变化而更新
student['grade'] = 'A'               # 视图自动更新

# copy(): 返回字典的浅复制
student_copy = student.copy()        # 创建新的字典副本
```

### 集合方法
```python
# 创建示例集合
fruits = {'apple', 'banana', 'orange'}
numbers = {1, 2, 3, 4, 5}

# add(elem): 添加元素
fruits.add('grape')                  # {'apple', 'banana', 'orange', 'grape'}

# remove(elem): 删除元素
fruits.remove('banana')              # 元素不存在会报错
try:
    fruits.remove('pear')           # 会抛出 KeyError
except KeyError:
    print("Element not found")

# discard(elem): 删除元素(如果存在)
fruits.discard('pear')              # 元素不存在也不会报错

# pop(): 随机删除并返回一个元素
random_fruit = fruits.pop()          # 删除并返回任意元素

# clear(): 清空集合
numbers.clear()                      # set()

# 集合运算示例
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# update(others): 更新集合
set1.update([4, 5, 6, 7])           # {1, 2, 3, 4, 5, 6, 7}

# intersection(others): 返回交集
common = set1.intersection(set2)     # {3, 4}
# 使用运算符
common = set1 & set2                 # 同上

# union(others): 返回并集
all_nums = set1.union(set2)         # {1, 2, 3, 4, 5, 6}
# 使用运算符
all_nums = set1 | set2              # 同上

# difference(others): 返回差集
diff = set1.difference(set2)         # {1, 2}
# 使用运算符
diff = set1 - set2                   # 同上

# symmetric_difference(others): 返回对称差集
sym_diff = set1.symmetric_difference(set2)  # {1, 2, 5, 6}
# 使用运算符
sym_diff = set1 ^ set2              # 同上

# 子集和超集判断
set3 = {1, 2}
# issubset(others): 判断是否为子集
is_subset = set3.issubset(set1)     # True
# 使用运算符
is_subset = set3 <= set1            # 同上

# issuperset(others): 判断是否为超集
is_superset = set1.issuperset(set3) # True
# 使用运算符
is_superset = set1 >= set3          # 同上

# 实际应用示例
# 1. 去除列表中的重复元素
numbers = [1, 2, 2, 3, 3, 4, 5, 5]
unique_numbers = list(set(numbers))  # [1, 2, 3, 4, 5]

# 2. 查找共同元素
list1 = [1, 2, 3, 4, 5]
list2 = [4, 5, 6, 7, 8]
common_elements = set(list1) & set(list2)  # {4, 5}

# 3. 集合运算在数据处理中的应用
authorized_users = {'alice', 'bob', 'charlie'}
active_users = {'bob', 'charlie', 'dave'}

# 找出未授权的活跃用户
unauthorized = active_users - authorized_users  # {'dave'}

# 找出所有相关用户
all_users = authorized_users | active_users  # {'alice', 'bob', 'charlie', 'dave'}

# 找出既授权又活跃的用户
active_authorized = authorized_users & active_users  # {'bob', 'charlie'}
```

这些示例展示了Python内置数据结构的常用方法及其实际应用场景。通过这些例子，你可以更好地理解如何在实际编程中使用这些方法。记住，Python的内置数据结构设计得非常直观和易用，掌握这些基本操作将大大提高你的编程效率。

## 参考资料

1. [Python 官方文档 - 数据结构](https://docs.python.org/zh-cn/3/tutorial/datastructures.html) - Python官方教程中关于数据结构的详细说明
2. [Python 标准库文档](https://docs.python.org/zh-cn/3/library/index.html) - 包含了所有内置类型和函数的完整参考
3. [Real Python - Python数据类型和数据结构](https://realpython.com/python-data-types/) - 深入解析Python数据类型和结构的特点
4. [Python编程之禅](https://www.python.org/dev/peps/pep-0020/) - 了解Python的设计哲学
5. [Python 内置函数官方文档](https://docs.python.org/zh-cn/3/library/functions.html) - 所有内置函数的详细说明和用法

更多内容将在后续文章中详细介绍。
