---
title: "It Depends on What the Meaning of the Word Is Is"
date: 2023-07-07T06:25:29-04:00
draft: true
---

I've avoided using the `is` operator in Python without understanding why. I'm sure I read it in a tutorial or blog that advised me to always use `==` for comparison. While doing some code review with a colleague, the use of the operator in a comparison got my attention so I decided to dig into it with some examples in the repl:

```
>>> [] is []
False
>>> [] == []
True
```
So far based on my understanding this makes sense. They are at different locations in memory, right? To check a memory location we can use `id()`:

```
>>> print(id([]))
140613516575744
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

Other folks have asked a [similar question on stack overflow](https://stackoverflow.com/questions/10440792/why-does-false-evaluate-to-false-when-if-not-succeeds). I didn't see any sources, but an answer about CPython trying to optimize for performance and that resulting in sometimes getting the same empty object created make sense for now.

These examples are academic. In practice `is` and `==` will work the way we expect:

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