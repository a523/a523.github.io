---
layout: post
title:  "如何在Python中对Class的方法进行单元测试, 但是避免提供所有 Class 的参数"
date:   2024-09-08 +0800
categories: python
---
为了测试 `MyClass` 中的 `method_to_test` 方法，同时避免提供 `c` 参数，可以使用 `unittest.mock.patch.object` 来模拟 `MyClass` 的初始化过程。这样，你可以在测试中只提供 `a` 和 `b` 参数，而忽略 `c` 参数。

下面是 `TestMyClass` 类的一个示例实现，它使用了 `patch.object` 来模拟 `MyClass` 的 `__init__` 方法，从而可以在测试中只提供需要的参数：

```python
from unittest.mock import patch
import unittest

class MyClass:
    def __init__(self, a, b, c):
        self.a = a
        self.b = b
        self.c = c

    def method_to_test(self):
        return self.a + self.b

class TestMyClass(unittest.TestCase):
    def setUp(self):
        self.a = 3
        self.b = 4

    @patch.object(MyClass, '__init__', lambda self, a, b: None)
    def test_method_to_test(self):
        # 由于我们已经patch了__init__方法，我们可以不传递c参数
        my_class_instance = MyClass(self.a, self.b)
        # 手动设定a和b的值
        my_class_instance.a = self.a
        my_class_instance.b = self.b

        # 调用method_to_test方法并断言结果是否正确
        result = my_class_instance.method_to_test()
        self.assertEqual(result, self.a + self.b)

if __name__ == '__main__':
    unittest.main()
```

这里的关键是使用 `patch.object` 装饰器来模拟 `MyClass` 的 `__init__` 方法，使其不执行任何操作（通过提供一个什么也不做的lambda函数作为新的初始化方法）。然后，在 `test_method_to_test` 方法中，我们手动设置 `my_class_instance` 的 `a` 和 `b` 属性，以便 `method_to_test` 能够正常工作。最后，我们调用 `method_to_test` 并使用 `assertEqual` 来验证结果是否符合预期。
