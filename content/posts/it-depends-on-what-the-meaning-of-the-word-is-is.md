---
title: "is vs == and Stumbling on Python Optimization"
date: 2023-07-07T06:25:29-04:00
draft: false
---

If you're relatively new to Python like me you've probably been advised to stick with the `==` operator for comparisons in Python without thinking about why. In my attempt to duive a bit deeper, I immediately jumped into the repl to grasp the differences between `==` and the `is` operator.

Before we get started, the docs tell us we use `is` to check if two objects are identical, meaning they exist at the same memory location. Conversely, `==` is used to compare the values of two objects, regardless of their memory address.

Consider the following examples:

```python
>>> [] is []
False
>>> [] == []
True
```

As expected, while the two empty lists hold the same value, they aren't the same object in memory. We can verify an object's memory location using Python's `id()` function:

```python
>>> print(id([]))
140613516575744
```

This understanding, however, was challenged when I stumbled upon this curious case:

```python
>>> id([]) == id([])
True
```

This raises an intriguing question: why does `id([]) == id([])` return `True`? It would appear that Python is either reusing the same object on both sides of the comparison or creating a new object at the exact same memory address. This is particularly puzzling when you look at this:

```python
>>> list() is list()
False
>>> list() == list()
True
>>> id(list()) == id(list())
False
```

Others have asked similar questions on Stack Overflow. An answer mentioning CPython's performance optimization caught my eye. So, I decided to investigate further, using [Compiler Explorer](https://godbolt.org/):

`[]`
```
  1           2 BUILD_LIST               0
              4 POP_TOP
              6 LOAD_CONST               0 (None)
              8 RETURN_VALUE
```
`list()`
```
  1           2 PUSH_NULL
              4 LOAD_NAME                0 (list)
              6 PRECALL                  0
             10 CALL                     0
             20 POP_TOP
             22 LOAD_CONST               0 (None)
             24 RETURN_VALUE
```

Although `[]` and `list()` both result in an empty list, the former does so in fewer opcodes, hinting at the optimization process. But this doesn't completely clarify the unexpected `id()` comparisons.

My hypothesis is that `id([]) == id([])` returns True due to the immediate release and subsequent reuse of the memory location by Python's memory management system. This theory is further corroborated by a quote from the Python docs stating: 

>"Two objects with non-overlapping lifetimes may have the same `id()` value."

This side quest was mostly academic. In most practical scenarios, `is` and `==` operate as we'd expect:

```python
>>> list1 = []
>>> list2 = []
>>> id(list1)
140613516624000
>>> id(list2)
140613516575744
>>> list1 == list2
True
>>> list1 is list2
False
>>> 
```

In conclusion,`is` and `==` might give identical results for some immutable types due to Python's optimizations, but they are fundamentally different. `is` checks for object identity, while `==` checks for equality of value.