---
layout: post
title: Debug python c/c++ extensions
---

It's common to use c/c++ extension in python program for better performance. But it's hard to debug c/c++ code when it's called from python.

The obvious solution is to debug the c/c++ code alone. But usually the c/c++ code recieves a lot of arguments from python, some of them may be of complicated types like numpy.ndarray, making this process difficult. To do that, you have to write a wrapper application, dump python objects into pickle or npz files in your python code, and read them from the c/c++ wrapper application, then call your c/c++ extension code. It's worth to do it when you have complicated logic bugs to inspect.

Sometimes I don't even need a step by step debug environment. I just want some clues where the error (segmentation fault) arises. Then we can use the [faulthandler](https://docs.python.org/3/library/faulthandler.html) module.

```python
import faulthandler
faulthandler.enable()
```

Simple and working.

### References

* https://docs.python.org/3/library/faulthandler.html
