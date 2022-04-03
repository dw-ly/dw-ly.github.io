---
title: Lua学习记录
tags:
- lua
categories:
- lua
---
一.字符串

1.从字符串里提取数字

```lua
res,_ = str:gsub("%D+","")
```
<!--more-->
2.将字符串以某某字符分隔开并放入table中

string库的gsub函数，共三个参数： 
1. str是待分割的字符串 
2. `'[^'..reps..']+'`是正则表达式，查找非reps字符，并且多次匹配 
3. 每次分割完的字符串都能通过回调函数获取到，w参数就是分割后的一个子字符串，把它保存到一个table中

```lua
function split( str,reps )
    local resultStrList = {}
    string.gsub(str,'[^'..reps..']+',function ( w )
        table.insert(resultStrList,w)
    end)
    return resultStrList
end
```

3.将字符串转化为table

```lua
str:gsub(".",function(c) table.insert(save,c) end)
```

其中str是待转换的字符串，save为转换后的table

4.table以某种格式拼接成一个字符串

```lua
str=table.concat(mtable,t)
```

str为返回的拼接好的字符串，mtable为待拼接table，t为拼接规则，如：t="-"字符串将以“-”连接每一个table元素而生成。

5.gmatch

5.1逐个返回字符串中字符

```lua
str:gmatch('.')
```

5.2逐个返回符合要求的字符

例：与任何字母匹配多次

```lua
str:gmatch('%a+')
```

二.运算符

实现其他语言中的condition ? result1 : result2三元运算符

```lua
local isAppel = false
print(isAppel and "苹果" or "梨")
```

三.table表

1.可以通过如下方式初始化一个多层的table，这样就不需要一层一层if not table  then table = {} end创建了 

```lua
local tmp_table = {
	a = {
		tmp1 = 1,
        tmp2 = 2
	}
}
```

2.lua中table和其他数据类型不一样的一点在于**table的赋值是传引用**，相当于指针a将地址传给指针b，而他们所指向的内容都是一样的

例：

```lua
local table_x = {
	tmp1 = "lua",
	tmp2 = 1
}
table_y = table_x
table_x.tmp1 = "test"
print(table_y.tmp1)
--输出结果为：test
```

3.震惊了，着实没有想到lua中可以进行的对于table的一系列操作，table也可以作为key值。

```lua
local m_table = {
    tmp1 = 1,
    tmp2 = 2
}
local test_table = {}
test_table[m_table] = 233
print(test_table[m_table])
--输出结果为：233
```

