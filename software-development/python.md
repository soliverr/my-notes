---
id: 2ePNIGTK1DjU14xZww6oP
title: Python
desc: ''
updated: 1645786425046
created: 1629875851539
stub: true
---

## Python

https://www.python.org

## PEP

[PEPs](https://www.python.org/dev/peps/) - Python Enhancement Proposals.

> This PEP contains the index of all Python Enhancement Proposals, known as PEPs. PEP numbers are assigned by the PEP editors, and once assigned are never changed [1]. The version control history [2] of the PEP texts represent their historical record.
>
> [1] [PEP 1](https://www.python.org/dev/peps/pep-0001): PEP Purpose and Guidelines
[2] View PEP history online: https://github.com/python/peps

## Installation

### CentOS

#### Python3.8 on CentOS 7

https://wiki.centos.org/AdditionalResources/Repositories/SCL:

```
$ yum install centos-release-scl
$ yum search python38
```

## Learning

* Обучающие ресурсы 
    * https://www.pythontutorial.net

### [Python args and kwargs: Demystified](https://realpython.com/python-kwargs-and-args/)

This is where `*args` can be really useful, because it allows you to pass a varying number of positional arguments. Take the following example:

```python
# sum_integers_args.py
def my_sum(*args):
    result = 0
    # Iterating over the Python args tuple
    for x in args:
        result += x
    return result

print(my_sum(1, 2, 3))
```

Note that args is just a name. You’re not required to use the name args. You can choose any name that you prefer, such as integers:

# sum_integers_args_2.py
def my_sum(*integers):
    result = 0
    for x in integers:
        result += x
    return result

print(my_sum(1, 2, 3))

The function still works, even if you pass the iterable object as integers instead of args. All that matters here is that you use the unpacking operator (*).

Bear in mind that the iterable object you’ll get using the unpacking operator * is not a list but a tuple. A tuple is similar to a list in that they both support slicing and iteration. However, tuples are very different in at least one aspect: lists are mutable, while tuples are not.

**kwargs works just like *args, but instead of accepting positional arguments it accepts keyword (or named) arguments. Take the following example:

# concatenate.py
def concatenate(**kwargs):
    result = ""
    # Iterating over the Python kwargs dictionary
    for arg in kwargs.values():
        result += arg
    return result

print(concatenate(a="Real", b="Python", c="Is", d="Great", e="!"))

Ordering Arguments in a Function

To recap, the correct order for your parameters is:

    Standard arguments
    *args arguments
    **kwargs arguments

# correct_function_definition.py
def my_function(a, b, *args, **kwargs):
    pass


# wrong_function_definition.py
def my_function(a, b, **kwargs, *args):
    pass


airf


