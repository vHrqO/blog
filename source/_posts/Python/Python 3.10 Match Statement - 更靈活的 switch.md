---
title: Python 3.10 | Match Statement - 更靈活的 switch 
date: 2025-3-5
tags:
    - Python
    - Python 3.10
categories:
    - Python · 基礎
keywords:
    - Python
    - Python 3.10
    - Match Statement
    - Structural Pattern Matching
cover: https://images.unsplash.com/photo-1559622214-f8a9850965bb?q=80&w=3294&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D
description:
---


# 概述
在我當初開始學 Python 時，發現沒有 switch 可用，令我感到有些驚訝，
還好從 Python 3.10 開始， Python 有了自己的 switch —— [Structural Pattern Matching](https://peps.python.org/pep-0636/)。

所以什麼是 Structural Pattern Matching ? 與 C++ 的 switch 相比，我覺得它更類似於 C# 的 Pattern Matching。
舉個簡單的例子:

```python
is_perform = False

match ("anon", "soyorin"):
    # Mismatch
    case 'tomorin':
        print('組一輩子樂團')

    # Mismatch: soyorin != rana
    case ("anon", "rana"):
        print('有趣的女人')

    # Successful match, but guard fails
    case  ("anon", "soyorin") if is_perform:
        print('為什麼要演奏春日影!')

    # Matches and binds y to "soyorin"
    case ("anon", y):
        print(f'愛音,{y} 強到靠北')

    # Pattern not attempted
    case _:
        print('我還是會繼續下去')


# 愛音,soyorin 強到靠北
```

> 📌 Note
與 [C++](https://www.learncpp.com/cpp-tutorial/switch-fallthrough-and-scoping/) 不同，Python 的 `match` **沒有** [**fallthrough**](https://peps.python.org/pep-0636/#matching-multiple-patterns)。
所以一個 `case` 完成就會離開 `match`，而不會繼續執行下一個 `case`。


# 前言
[文件](https://docs.python.org/3/reference/compound_stmts.html#the-match-statement)中把 `match` 後的部分 `("anon", "soyorin"):`，稱為 `subject_expr`，
為了方便理解，我們叫他 `match value`。


# Guards
> `case <pattern> if <expression>:`

如果我們在 pattern 成功 match 後，想進一步檢查，可以在 pattern 後面加上 `if` ，
也就是範例中的 `case  ("anon", "soyorin") if is_perform:`，
這種用法稱為 **Guard** 。

執行流程大致為
1. Pattern 成功 match，執行 Guard
2. Pattern Mismatch，不執行 Guard 
3. Guard 結果為 `True` → 該 `case` 執行
4. Guard 結果為 `False` → 跳過該 `case`，繼續檢查下一個 `case` 


# Irrefutable Case Blocks
指的是**一定** match 的情況，類似於 C++ 的 `default`，
但只能出現在 **最後一個** `case`，且整個 match 只能有 **一個** 這樣的 `case`。
至於甚麼 pattern 符合?
可以參考 [https://docs.python.org/3/reference/compound_stmts.html#irrefutable-case-blocks](https://docs.python.org/3/reference/compound_stmts.html#irrefutable-case-blocks)

舉例來說:
```python
match 2:
    case 1:
        print("value is 1")
    case x:
        print("Irrefutable Case Blocks")


# Irrefutable Case Blocks
```


# Patterns

## OR Patterns
就如同字面含意，就是個 `or`，
pattern 會逐一嘗試直到其中一個成功為止。

舉個簡單例子:
```python
match 1:
    case 1 | 2 | 3:
        print("value is 1 or 2 or 3")


# value is 1 or 2 or 3
```

## AS Patterns
前面 `or` 用的很開心，那我們如何取的原本的 value 呢?
此時我們可以用 `as` 來取得前面 match 到的值，
也就是`case <pattern> as <name>:`，在 pattern 成功的情況下，
match value 會 bind 到 name 上，`name = <match value>`。

接續前面的例子
```python
match 1:
    case 1 | 2 | 3 as x:
        print(f"value is {x}")


# value is 1
```

## Literal Patterns
前面我們已經用了不少，用來比對 Python 中的 [Literals](https://docs.python.org/3/reference/lexical_analysis.html#literals)，
如`int`、`string`、`None`、`bool`等等，

簡單來說，如果 `if <match value> == <Literal>` 就會比對成功，
若是遇到 Singletons ，如`None`, `True` ,`False` 則會透過 `is` 來比對

## Capture Patterns
用來將比對的值 bind 到變數上，

在 pattern 中，name 只能被 bind 一次
```python
match (1, 1):
    # SyntaxError
    case x, x:
        print(f"Matched: {x}")


#     case x, x:
#             ^
# SyntaxError: multiple assignments to name 'x' in pattern
```

在下面例子中，在成功 match 的同時，`"soyorin"` 會 bind 到變數 `y` 上
```python
match ("anon", "soyorin"):
    # Matches and binds y to "soyorin"
    case ("anon", y):
        print(f'愛音,{y} 強到靠北')


# 愛音,soyorin 強到靠北
```

## Wildcard Patterns
`_`，用來比對任意值，基本上就是當 `default` 來用。

比如
```python
match ("Raana", "soyorin"):

    # Matches and binds y to "soyorin"
    case ("anon", y):
        print(f'愛音,{y} 強到靠北')

    # Pattern not attempted
    case _:
        print('我還是會繼續下去')


# 我還是會繼續下去
```

## Value Patterns
Value Pattern，是指可以透過 [name resolution](https://docs.python.org/3/reference/executionmodel.html#resolve-names)，
也就是透過 `.` 存取的變數，例如 `enum`、`math.pi` 等。  
藉由 `==` 來比對，`<match value> == <NAME1.NAME2>`。 

例子
```python
from enum import Enum


class Color(Enum):
    RED = 1
    GREEN = 2
    BLUE = 3


match Color.RED:
    case Color.RED:
        print("RED")
    case Color.GREEN:
        print("GREEN")
    case Color.BLUE:
        print("BLUE")
    case _:
        print("unknown")


# RED
```

## Group Patterns
老實說，我覺得這不算 pattern，就是告訴你可以加 `()` 來加強可讀性

比如前面的例子
```python
match 1:
    # case 1 | 2 | 3 as x:
    case (1 | 2 | 3) as x:
        print(f"value is {x}")


# value is 1
```

## Sequence Patterns
用來比對 ["Sequence"](https://docs.python.org/3/reference/compound_stmts.html#id21)，如 `List` 和 `Tuple` 等。  
不過，`str`、`bytes`、`bytearray` 並不會被當作 Sequence Patterns。

> 📌 Note
> - Python 不區分 `(...)` 和 `[...]`，兩者作用相同。
> - 另需注意的是 `(3 | 4)` 會是 group pattern，但 `[3 | 4]` 依舊是 sequence pattern。

舉體的比對方式大致是
- 固定長度
    1. Match value 是 Sequence 
    2. `len(value) == len(patterns)`
    3. 從左到右依序比對
- 可變長度 (如`[first, *middle, last]`)
    1. Match value 是 Sequence 
    2. Sequence 長度小於扣除非 `*`(star pattern) 的數量 → 比對失敗
    3. 比對前面非 `*`(star pattern) 的部分(如同固定長度，也就是 first 部分)
    4. 如果前面成功，扣除 last 部分後，收集剩餘元素(變成`list`，對應 `*middle` )
    5. 最後比對剩餘部分，也就是 last (如同固定長度)
        
看幾個例子可能比較清楚
```python
# fixed-length

match [10, 20, 30]:
    # note that this match can also bind names
    case [x, y, z]:
        print(f"x={x}, y={y}, z={z}")


# x=10, y=20, z=30
```

```python
# variable-length

match [1, 2, 3, 4, 5]:
    case [first, *middle, last]:
        print(f"first={first}, middle={middle}, last={last}")


# first=1, middle=[2, 3, 4], last=5
```

## Mapping Patterns
用來比對 ["mapping"](https://docs.python.org/3/reference/compound_stmts.html#id22)，最常用的就是 `dict`

與前面類似，我們可以把 `**`(double_star_pattern) 放在最後，收集剩餘元素，
另外不可以有重複的 `key`，否則會 `SyntaxError`

舉體的比對流程
1. Match value 是 mapping
2. pattern 的每個 key 有存在於 Match value
3. key 對應的 value 與 pattern 相同

舉個例子
```python
match {"name": "Bob", "age": 30, "city": "NY"}:
    case {"name": n, "age": a}:
        print(f"name={n}, age={a}")


# name=Bob, age=30
```

## Class Patterns
用於比對 `class`，但其比對流程相對較複雜。
和 function arguments 一樣，分為 **positional arguments** 和 **keyword arguments** 兩種形式。

比對流程
1. Match value 是否是 [builtin type](https://docs.python.org/3/library/functions.html#type)
2. 檢查 Match value 是不是 Patterns 的 instance ，透過[`isinstance()`](https://docs.python.org/3/library/functions.html#isinstance)進行檢查。
3. 檢查 Class Patterns 是否含有 arguments ，沒有就直接比對成功

如果有則分成 keyword 或 positional argument 兩種情形
- **只有** Keyword arguments：  
    1. 檢查 attribute 是否存在於 Match value  
    2. 檢查 attribute value 是否和 Pattern 相同  
    3. 成功則往下個 keyword
- **如果有** Positional arguments：  
    1. 藉由 Match value 的 [`__match_args__`](https://docs.python.org/3/reference/datamodel.html#object.__match_args__)  attribute ，將 Positional arguments 轉換為 Keyword arguments

> 📌 Note      
> - `object.__match_args__`，若沒有定義，預設是一個 empty tuple `()`
> - 部分 [built-in types](https://docs.python.org/3/reference/compound_stmts.html#class-patterns)（如 `bool`、`int`、`list`、`str` 等），是比對接收 positional argument 後的**整個 object**。

例子
```python
# Keyword argument
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y


match Point(1, 2):
    case Point(x=1, y=y_value):
        print(f"Matched! y={y_value}")


# Matched! y=2
```

```python
# positional argument
class Point:
    # assigned a tuple of strings
    __match_args__ = ("x", "y")

    def __init__(self, x, y):
        self.x = x
        self.y = y


match Point(1, 2):
    # converted to keyword patterns using the __match_args__
    case Point(1, y_value):
        print(f"Matched! y={y_value}")


# Matched! y=2
```


# 結語
算是把 match statement 完整介紹了一遍，希望下篇別拖更 （￣︶￣）↗　
如果有任何問題，歡迎在下面留言。


# References
- https://docs.python.org/3/reference/compound_stmts.html#the-match-statement
- https://peps.python.org/pep-0636/
- https://peps.python.org/pep-0634/
- https://stackoverflow.com/questions/46701063/why-doesnt-python-have-switch-case-update-match-case-syntax-was-added-to-pyt


> Photo by *Mae Mu* on [Unsplash](https://unsplash.com/photos/pile-of-cookies-ppOPjqAJ3Mw)

<br />