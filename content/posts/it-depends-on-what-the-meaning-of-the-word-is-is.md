---
title: "is vs == and Stumbling on Python Optimization"
date: 2023-07-07T06:25:29-04:00
draft: true
---

I've avoided using the `is` operator in Python without understanding why. I'm sure a tutorial or blog advised me to always use `==` for comparison. 

To recap: use `is` to check if objects are the same by their reference in memory. Use `==` to compare the values of two objects.

While doing some code review with a colleague, the use of the operator in a comparison got my attention so I decided to dig into it with some examples in the repl:

```
>>> [] is []
False
>>> [] == []
True
```
Based on my understanding this makes sense. They are at different locations in memory. To check a memory location we can use `id()`:

```
>>> print(id([]))
140613516575744
```

Which led me to try something else:

```
>>> id([]) == id([])
True
```

Wait a minute, why is `id([]) == id([])` = `True`? At this point either Python is using the same object on each side of the comparison or it's created a new object in the exact same place? That doesn't make sense, especially when I try something like this:

```
>>> list() is list()
False
>>> list() == list()
True
>>> id(list()) == id(list())
False
```

Other folks have asked a [similar question on stack overflow](https://stackoverflow.com/questions/10440792/why-does-false-evaluate-to-false-when-if-not-succeeds). I didn't see any sources, but an answer about CPython trying to optimize for performance caught my attention so I decided to investigate using (Compiler Explorer)[https://godbolt.org/]:

```
[]

  1           2 BUILD_LIST               0
              4 POP_TOP
              6 LOAD_CONST               0 (None)
              8 RETURN_VALUE

```

```
list()

  1           2 PUSH_NULL
              4 LOAD_NAME                0 (list)
              6 PRECALL                  0
             10 CALL                     0
             20 POP_TOP
             22 LOAD_CONST               0 (None)
             24 RETURN_VALUE
```

Calling `[]` or `list()` both result in an empty list but one does it in way fewer op codes. This explains the optimization answer, but doesn't explain the wonky results. My current guess is that `id([]) is id([])` is happening quick enough that the same location is being used.

Of course these examples are academic. In practice `is` and `==` will work the way we expect:

```
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