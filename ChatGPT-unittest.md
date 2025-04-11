好的，作为一名专业的测试工程师，我将为你详细讲解 Python 内建的 `unittest` 框架。这篇指南将遵循从 0 到 1 的原则，结合大量实例，力求让你在学习后能够掌握 `unittest` 的核心用法，并能将其应用于实际项目中。

---

**Unittest 框架超详细从 0 到 1 指南**

**目录**

1.  **什么是单元测试 (Unit Testing)？**
2.  **为什么需要单元测试？**
3.  **Unittest 框架介绍**
    *   核心概念
4.  **开始第一个 Unittest 测试**
    *   准备被测试代码
    *   编写第一个测试用例
    *   运行测试
    *   理解测试结果 (OK, FAIL, ERROR)
5.  **Unittest 核心组件详解**
    *   `TestCase`：测试用例的基础
    *   测试方法命名规则
    *   `Assertions`：断言（关键！）
    *   `Test Fixtures`：测试固件 (Setup / Teardown)
        *   `setUp()` 和 `tearDown()`
        *   `setUpClass()` 和 `tearDownClass()`
        *   执行顺序
    *   `TestSuite`：组织测试用例
    *   `TestLoader`：加载测试用例
    *   `TestRunner`：执行测试并输出结果
6.  **测试发现 (Test Discovery)**
7.  **跳过测试 (Skipping Tests) 和预期失败 (Expected Failures)**
8.  **组织你的测试代码**
9.  **结合实例：测试一个稍微复杂的类**
10. **编写良好单元测试的最佳实践**
11. **总结**

---

**1. 什么是单元测试 (Unit Testing)？**

单元测试是指对软件中的最小可测试单元（通常是函数、方法或类）进行检查和验证的过程。目标是隔离程序的每个部分，并证明这些独立的、隔离的部分是正确工作的。

**2. 为什么需要单元测试？**

*   **及早发现 Bug**：在开发早期就能发现问题，修复成本更低。
*   **提高代码质量**：促使开发者编写更模块化、可测试的代码。
*   **提供代码文档**：测试用例本身就是一种说明代码如何使用的文档。
*   **安全重构**：有测试覆盖，你可以更有信心地修改或重构代码，不用担心破坏现有功能。
*   **回归测试**：确保新的改动没有影响到旧的功能。
*   **促进更好的设计**：难以测试的代码通常意味着设计上存在问题。

**3. Unittest 框架介绍**

`unittest` 是 Python 标准库中自带的一个单元测试框架（有时也称为 PyUnit），它的灵感来源于 Java 的 JUnit。它提供了创建测试用例、组织测试套件以及运行测试所需的所有基本功能。

*   **核心概念:**
    *   **Test Case (测试用例)**：一个包含多个测试方法的类，是测试的基本单元。
    *   **Test Fixture (测试固件)**：执行一个或多个测试前所需的准备工作（如创建数据库连接、设置临时文件）以及测试执行后的清理工作。
    *   **Test Suite (测试套件)**：将多个测试用例或测试套件组合在一起的集合，可以一次性运行。
    *   **Test Runner (测试运行器)**：负责执行测试并向用户呈现结果的组件。
    *   **Test Loader (测试加载器)**：负责从类和模块中查找并加载测试用例。
    *   **Assertions (断言)**：在测试中检查条件是否满足的方法（例如，检查两个值是否相等，某个异常是否被抛出等）。

**4. 开始第一个 Unittest 测试**

让我们从一个最简单的例子开始。

**(1) 准备被测试代码**

假设我们有一个简单的计算器模块 `calculator.py`：

```python
# calculator.py
def add(a, b):
  """计算两个数的和"""
  if not (isinstance(a, (int, float)) and isinstance(b, (int, float))):
    raise TypeError("Operands must be integers or floats")
  return a + b

def subtract(a, b):
  """计算两个数的差"""
  return a - b
```

**(2) 编写第一个测试用例**

现在，我们为 `calculator.py` 编写测试。创建一个新文件，通常命名为 `test_` 开头，例如 `test_calculator.py`：

```python
# test_calculator.py
import unittest
from calculator import add # 导入需要测试的函数

class TestCalculatorAdd(unittest.TestCase): # 必须继承 unittest.TestCase

  def test_add_integers(self):
    """测试两个正整数相加"""
    result = add(3, 5)
    self.assertEqual(result, 8, "Test Failed: 3 + 5 should be 8") # 断言：检查结果是否等于预期值

  def test_add_floats(self):
    """测试两个浮点数相加"""
    result = add(3.0, 5.5)
    self.assertAlmostEqual(result, 8.5, places=5, msg="Test Failed: 3.0 + 5.5 should be approximately 8.5") # 对浮点数使用 assertAlmostEqual

  def test_add_negative_numbers(self):
    """测试包含负数的相加"""
    self.assertEqual(add(-1, -1), -2)
    self.assertEqual(add(-1, 1), 0)

  def test_add_raises_type_error(self):
    """测试非数字类型相加是否抛出 TypeError"""
    # 使用上下文管理器的方式检查异常
    with self.assertRaises(TypeError, msg="Adding non-numeric types should raise TypeError"):
        add('3', 5)
    with self.assertRaises(TypeError):
        add(3, '5')
    # 也可以这样写，但不推荐用于检查异常，因为不够清晰
    # self.assertRaises(TypeError, add, '3', 5)

# 这段代码使得你可以直接运行这个 .py 文件来执行测试
if __name__ == '__main__':
  unittest.main()
```

**代码解释:**

*   `import unittest`: 导入 `unittest` 模块。
*   `from calculator import add`: 导入你要测试的代码。
*   `class TestCalculatorAdd(unittest.TestCase):`: 创建一个测试类，必须继承自 `unittest.TestCase`。类名通常以 `Test` 开头。
*   `def test_add_integers(self):`: 定义一个测试方法。**重要：测试方法必须以 `test_` 开头**，否则 `unittest` 不会将其识别为测试方法。
*   `self.assertEqual(result, 8, "...")`: 这是**断言**。`assertEqual(a, b)` 检查 `a` 是否等于 `b`。如果断言失败，测试将失败，并显示可选的 `msg` 信息。
*   `self.assertAlmostEqual(result, 8.5, places=5, ...)`: 由于浮点数精度问题，比较浮点数时应使用 `assertAlmostEqual`，它可以指定比较的小数位数。
*   `with self.assertRaises(TypeError, ...)`: 这是一个非常有用的断言，用于检查特定的代码块是否如预期那样抛出了指定的异常。
*   `if __name__ == '__main__': unittest.main()`: 这是一个常用的 Python 习惯用法。当你直接运行 `python test_calculator.py` 时，`unittest.main()` 会被调用，它会自动发现并运行该文件中所有 `unittest.TestCase` 子类中的 `test_` 方法。

**(3) 运行测试**

有两种主要方式运行测试：

*   **直接运行测试文件:**
    ```bash
    python test_calculator.py
    ```
    或者，如果你在 IDE (如 PyCharm, VS Code) 中，通常可以直接右键点击文件或测试方法选择 "Run 'Unittests in test_calculator.py'"。

*   **使用 `unittest` 命令行接口 (推荐用于自动化和大型项目):**
    切换到你的项目根目录（包含 `calculator.py` 和 `test_calculator.py` 的目录），然后运行：
    ```bash
    python -m unittest test_calculator.py
    ```
    或者使用测试发现功能 (后面会详细讲):
    ```bash
    python -m unittest discover
    ```
    这会自动查找当前目录及其子目录下所有 `test*.py` 文件并执行它们。

**(4) 理解测试结果**

运行测试后，你会看到类似以下的输出：

*   **全部通过 (OK):**
    ```
    ....
    ----------------------------------------------------------------------
    Ran 4 tests in 0.001s

    OK
    ```
    每个点 (`.`) 代表一个成功通过的测试。

*   **有失败 (FAIL):**
    假设我们在 `test_add_integers` 中错误地写了 `self.assertEqual(result, 9)`：
    ```
    F...
    ======================================================================
    FAIL: test_add_integers (__main__.TestCalculatorAdd)
    测试两个正整数相加
    ----------------------------------------------------------------------
    Traceback (most recent call last):
      File "test_calculator.py", line 10, in test_add_integers
        self.assertEqual(result, 9, "Test Failed: 3 + 5 should be 8") # 断言：检查结果是否等于预期值
    AssertionError: 8 != 9 : Test Failed: 3 + 5 should be 8

    ----------------------------------------------------------------------
    Ran 4 tests in 0.002s

    FAILED (failures=1)
    ```
    `F` 代表一个失败的测试。输出会清晰地指出哪个测试方法 (`test_add_integers`) 在哪一行 (`line 10`) 失败了，以及断言失败的原因 (`AssertionError: 8 != 9`) 和我们提供的消息。

*   **有错误 (ERROR):**
    错误 (Error) 不同于失败 (Failure)。失败是断言检查未通过，而错误是测试代码本身在执行过程中遇到了未预料的异常（不是通过 `assertRaises` 捕获的异常）。

    假设 `calculator.py` 的 `add` 函数没有类型检查，而我们在测试中传入了字符串：
    ```python
    # calculator.py (修改后，无类型检查)
    def add(a, b):
      return a + b

    # test_calculator.py (添加一个错误测试)
    def test_add_strings_error(self):
        """测试字符串相加（预期会产生 TypeError 错误）"""
        add('hello', 'world') # 这里会直接报错，而不是被 assertRaises 捕获
    ```
    运行结果：
    ```
    .E..
    ======================================================================
    ERROR: test_add_strings_error (__main__.TestCalculatorAdd)
    测试字符串相加（预期会产生 TypeError 错误）
    ----------------------------------------------------------------------
    Traceback (most recent call last):
      File "test_calculator.py", line 28, in test_add_strings_error
        add('hello', 'world') # 这里会直接报错，而不是被 assertRaises 捕获
      File "/path/to/your/project/calculator.py", line 3, in add
        return a + b
    TypeError: can only concatenate str (not "int") to str  # 或者类似的类型错误

    ----------------------------------------------------------------------
    Ran 4 tests in 0.002s

    FAILED (errors=1)
    ```
    `E` 代表一个错误的测试。Traceback 指出了错误发生在被测试代码 (`calculator.py`) 中，而不是测试断言本身。

**5. Unittest 核心组件详解**

**(1) `TestCase`：测试用例的基础**

*   所有测试类都必须继承 `unittest.TestCase`。
*   它提供了各种断言方法（下面会讲）。
*   它提供了 `setUp()` 和 `tearDown()` 等 Fixture 方法。

**(2) 测试方法命名规则**

*   测试类中的方法如果需要被 `unittest` 识别并执行，其名称**必须**以 `test_` 开头。
*   方法名应清晰描述其测试目的，例如 `test_login_with_valid_credentials`。

**(3) `Assertions`：断言（关键！）**

断言是单元测试的核心，用于验证代码的行为是否符合预期。`unittest.TestCase` 提供了许多断言方法，以下是一些最常用的：

| 方法                       | 检查                                 | 示例                                               |
| :------------------------- | :----------------------------------- | :------------------------------------------------- |
| `assertEqual(a, b, msg)`   | `a == b`                             | `self.assertEqual(add(2, 2), 4)`                   |
| `assertNotEqual(a, b, msg)` | `a != b`                             | `self.assertNotEqual(add(2, 2), 5)`                 |
| `assertTrue(x, msg)`       | `bool(x) is True`                    | `self.assertTrue(is_valid_email("a@b.com"))`       |
| `assertFalse(x, msg)`      | `bool(x) is False`                   | `self.assertFalse(is_valid_email("invalid"))`      |
| `assertIs(a, b, msg)`      | `a is b` (对象标识是否相同)          | `a = None; self.assertIs(a, None)`                 |
| `assertIsNot(a, b, msg)`   | `a is not b`                         | `a = []; b = []; self.assertIsNot(a, b)`           |
| `assertIsNone(x, msg)`     | `x is None`                          | `result = get_optional_value(); self.assertIsNone(result)` |
| `assertIsNotNone(x, msg)`  | `x is not None`                      | `user = find_user("id"); self.assertIsNotNone(user)` |
| `assertIn(a, b, msg)`      | `a in b` (成员检查)                  | `self.assertIn(key, my_dict)`                      |
| `assertNotIn(a, b, msg)`   | `a not in b`                         | `self.assertNotIn(item, forbidden_list)`           |
| `assertIsInstance(a, b, msg)` | `isinstance(a, b)` (类型检查)     | `self.assertIsInstance(user, User)`              |
| `assertNotIsInstance(a, b, msg)`| `not isinstance(a, b)`          | `self.assertNotIsInstance(data, str)`             |
| `assertRaises(exc, func, *args, **kwds)` | 调用 `func(*args, **kwds)` 抛出 `exc` 异常 | `self.assertRaises(ValueError, int, 'xyz')`      |
| `assertRaisesRegex(exc, regex, func, ...)` | 类似于 `assertRaises`，但还检查异常消息是否匹配正则表达式 `regex` | `self.assertRaisesRegex(ValueError, "invalid literal", int, 'xyz')` |
| `assertAlmostEqual(a, b, places=7, msg)` | `round(a-b, places) == 0` (浮点数近似相等) | `self.assertAlmostEqual(0.1 + 0.2, 0.3, places=7)` |
| `assertNotAlmostEqual(a, b, places=7, msg)`| `round(a-b, places) != 0`          | `self.assertNotAlmostEqual(0.1 + 0.2, 0.31)`       |
| `assertGreater(a, b, msg)` | `a > b`                              | `self.assertGreater(len(items), 0)`                |
| `assertGreaterEqual(a, b, msg)` | `a >= b`                           | `self.assertGreaterEqual(count, min_count)`      |
| `assertLess(a, b, msg)`      | `a < b`                              | `self.assertLess(response_time, max_time)`         |
| `assertLessEqual(a, b, msg)` | `a <= b`                           | `self.assertLessEqual(usage, limit)`               |
| `assertRegex(text, regex, msg)` | `regex.search(text)` 匹配成功      | `self.assertRegex(html, r'<title>.*</title>')`    |
| `assertNotRegex(text, regex, msg)` | `regex.search(text)` 匹配失败     | `self.assertNotRegex(log, r'ERROR')`               |
| `assertCountEqual(a, b, msg)` | a 和 b 包含相同元素，顺序不限 (Python 3.2+) | `self.assertCountEqual([1, 2, 3], [3, 1, 2])`     |

**注意:**
*   所有断言方法都有一个可选的 `msg` 参数，当断言失败时，这个消息会显示出来，有助于快速定位问题。强烈建议使用有意义的消息。
*   检查异常时，推荐使用 `with self.assertRaises(ExceptionType): ...` 的上下文管理器形式，代码更清晰。

**(4) `Test Fixtures`：测试固件 (Setup / Teardown)**

通常，你的测试需要一些前置条件（比如创建一个数据库连接、生成测试数据、创建一个临时目录）和一些收尾工作（关闭连接、删除数据、删除目录）。这就是 Fixtures 的作用。

*   **`setUp()` 和 `tearDown()`**
    *   `setUp()`: 在**每个**测试方法 (`test_*`) 执行**之前**运行。
    *   `tearDown()`: 在**每个**测试方法 (`test_*`) 执行**之后**运行，**即使测试方法失败或出错也会执行**。

    **示例:**

    ```python
    import unittest
    import tempfile
    import os

    class FileHandler:
        def __init__(self, filepath):
            self.filepath = filepath
            self.file = None

        def open_file(self, mode='w'):
            self.file = open(self.filepath, mode)

        def write_line(self, line):
            if self.file:
                self.file.write(line + '\n')

        def close_file(self):
            if self.file:
                self.file.close()
                self.file = None

        def read_content(self):
            with open(self.filepath, 'r') as f:
                return f.read()

    class TestFileHandler(unittest.TestCase):

        def setUp(self):
            """在每个测试方法运行前创建临时文件"""
            print("\nRunning setUp...")
            # 创建一个临时文件，返回文件描述符和路径
            self.fd, self.temp_path = tempfile.mkstemp()
            self.handler = FileHandler(self.temp_path)
            print(f" Temporary file created: {self.temp_path}")

        def tearDown(self):
            """在每个测试方法运行后清理临时文件"""
            print("Running tearDown...")
            if self.handler and self.handler.file: # 确保文件已关闭
                self.handler.close_file()
            os.close(self.fd) # 关闭文件描述符
            os.remove(self.temp_path) # 删除文件
            print(f" Temporary file removed: {self.temp_path}")

        def test_write_and_read(self):
            """测试写入和读取"""
            print(" Running test_write_and_read...")
            self.handler.open_file('w')
            self.handler.write_line("Hello")
            self.handler.write_line("World")
            self.handler.close_file()

            content = self.handler.read_content()
            self.assertEqual(content, "Hello\nWorld\n")

        def test_write_overwrites(self):
            """测试再次写入是否覆盖"""
            print(" Running test_write_overwrites...")
            # 第一次写入
            self.handler.open_file('w')
            self.handler.write_line("First Line")
            self.handler.close_file()

            # 第二次写入 (覆盖)
            self.handler.open_file('w')
            self.handler.write_line("Second Line")
            self.handler.close_file()

            content = self.handler.read_content()
            self.assertEqual(content, "Second Line\n")

    if __name__ == '__main__':
        unittest.main(verbosity=2) # verbosity=2 会显示更详细的输出，包括测试方法名
    ```

    运行这个测试，你会看到 `setUp` 和 `tearDown` 在每个 `test_` 方法前后都被调用了。

*   **`setUpClass()` 和 `tearDownClass()`**
    *   `setUpClass()`: 在**整个测试类**的所有测试方法执行**之前**运行**一次**。
    *   `tearDownClass()`: 在**整个测试类**的所有测试方法执行**之后**运行**一次**。
    *   **重要:** 这两个方法必须声明为类方法，使用 `@classmethod` 装饰器，并且第一个参数是 `cls` (类本身) 而不是 `self`。

    **使用场景:** 当某些设置或清理操作非常耗时，且对于类中的所有测试都是共享的时（例如，建立数据库连接池、启动一个模拟服务器），使用 `setUpClass/tearDownClass` 比在每个测试中重复执行 `setUp/tearDown` 更高效。

    **示例:**

    ```python
    import unittest

    class ExpensiveResource:
        """模拟一个昂贵的资源，如数据库连接"""
        def __init__(self):
            print("\nInitializing Expensive Resource...")
            self.data = {"status": "initialized"}

        def close(self):
            print("Closing Expensive Resource...")
            self.data = {}

    class TestWithClassFixture(unittest.TestCase):
        resource = None # 类级别的变量来存储资源

        @classmethod
        def setUpClass(cls):
            """在所有测试开始前，初始化一次资源"""
            print("Running setUpClass...")
            cls.resource = ExpensiveResource()

        @classmethod
        def tearDownClass(cls):
            """在所有测试结束后，清理一次资源"""
            print("Running tearDownClass...")
            if cls.resource:
                cls.resource.close()
            cls.resource = None

        def setUp(self):
            """每个测试方法前运行 (如果需要单独设置)"""
            print(" Running setUp (method)...")
            # 注意：这里可以访问 cls.resource
            self.resource_status = self.resource.data.get("status")

        def tearDown(self):
            """每个测试方法后运行 (如果需要单独清理)"""
            print(" Running tearDown (method)...")

        def test_resource_is_initialized(self):
            print("  Running test_resource_is_initialized...")
            self.assertEqual(self.resource_status, "initialized")
            self.assertIn("status", self.resource.data)

        def test_resource_can_be_used(self):
            print("  Running test_resource_can_be_used...")
            # 模拟使用资源
            self.resource.data["usage"] = "tested"
            self.assertEqual(self.resource.data["usage"], "tested")

    if __name__ == '__main__':
        unittest.main(verbosity=2)
    ```

    运行这个例子，你会看到 `setUpClass` 和 `tearDownClass` 只分别运行了一次，而 `setUp` 和 `tearDown` 仍然在每个测试方法前后运行。

*   **执行顺序:**
    1.  `setUpClass()`
    2.  `setUp()`
    3.  `test_method_1()`
    4.  `tearDown()`
    5.  `setUp()`
    6.  `test_method_2()`
    7.  `tearDown()`
    8.  ... (对类中所有 `test_*` 方法重复 2-4)
    9.  `tearDownClass()`

**(5) `TestSuite`：组织测试用例**

`TestSuite` 允许你将多个 `TestCase` 类或单独的测试方法组合在一起。虽然 `unittest` 的测试发现机制（后面会讲）使得手动创建 `TestSuite` 不那么常用，但了解它有助于理解 `unittest` 的工作原理，并在需要更精细控制测试执行顺序或组合时派上用场。

**示例：手动创建和运行 TestSuite**

假设我们有两个测试文件：`test_calculator.py` 和 `test_filehandler.py` (上面例子中的)。

```python
import unittest
from test_calculator import TestCalculatorAdd # 假设 TestCalculatorAdd 在这个文件里
from test_filehandler import TestFileHandler  # 假设 TestFileHandler 在这个文件里

def create_suite():
    """创建一个包含多个测试类的测试套件"""
    suite = unittest.TestSuite()

    # 添加整个测试类中的所有测试方法
    suite.addTest(unittest.makeSuite(TestCalculatorAdd))
    suite.addTest(unittest.makeSuite(TestFileHandler))

    # 也可以添加单个测试方法 (需要类名和方法名字符串)
    # suite.addTest(TestCalculatorAdd('test_add_floats'))

    return suite

if __name__ == '__main__':
    # 创建一个测试运行器
    runner = unittest.TextTestRunner(verbosity=2)

    # 创建测试套件
    my_suite = create_suite()

    # 运行测试套件
    print("Running custom test suite...")
    runner.run(my_suite)
```

**(6) `TestLoader`：加载测试用例**

`TestLoader` 负责根据不同的标准（如模块、类、方法名）查找并加载测试用例，并将它们组装成 `TestSuite`。`unittest.main()` 和 `unittest discover` 内部就使用了 `TestLoader`。

一些 `TestLoader` 的常用方法：

*   `loadTestsFromTestCase(testCaseClass)`: 从一个 `TestCase` 子类加载所有 `test_*` 方法。
*   `loadTestsFromModule(module)`: 从一个模块加载所有 `TestCase` 子类中的 `test_*` 方法。
*   `loadTestsFromName(name, module=None)`: 根据点分名称加载测试，可以是模块名、类名或 `module.Class.method`。
*   `discover(start_dir, pattern='test*.py', top_level_dir=None)`: 测试发现的核心方法。

你通常不需要直接与 `TestLoader` 交互，除非你需要非常定制化的测试加载逻辑。

**(7) `TestRunner`：执行测试并输出结果**

`TestRunner` 负责执行 `TestSuite` 并将结果呈现给用户。`unittest` 自带的主要 `TestRunner` 是 `TextTestRunner`，它将结果输出到控制台。

你可以创建自定义的 `TestRunner` 来改变输出格式（例如，生成 HTML 报告、XML 报告）或者添加额外的功能。一些第三方库（如 `unittest-xml-reporting`, `HTMLTestRunner`) 提供了不同的 Runner。

**6. 测试发现 (Test Discovery)**

这是 `unittest` 推荐的运行测试的方式，尤其是在项目中测试文件分散在不同目录下时。

**用法:**

在你的项目**根目录**下，打开终端，运行：

```bash
python -m unittest discover [options]
```

**默认行为:**

*   它会从当前目录 (`.`) 开始查找。
*   它会递归地查找所有子目录。
*   它会查找所有匹配 `test*.py` 模式的文件。
*   在这些文件中，它会加载所有继承自 `unittest.TestCase` 的类，并执行其中所有以 `test_` 开头的方法。

**常用选项:**

*   `-s <start_dir>`: 指定开始搜索的目录 (默认为 `.`)。
    ```bash
    python -m unittest discover -s tests # 只在 tests 目录下搜索
    ```
*   `-p <pattern>`: 指定匹配测试文件的模式 (默认为 `test*.py`)。
    ```bash
    python -m unittest discover -p "*_test.py" # 查找以 _test.py 结尾的文件
    ```
*   `-t <top_level_dir>`: 指定项目的顶层目录，用于正确处理 Python 的 import。通常是包含你的源代码和测试代码的根目录。
    ```bash
    # 假设项目结构:
    # my_project/
    #   src/
    #     calculator.py
    #   tests/
    #     test_calculator.py
    # 在 my_project 目录下运行:
    python -m unittest discover -s tests -t .
    ```
*   `-v`: 详细输出 (Verbose)，等同于 `verbosity=2`。

测试发现使得组织测试变得非常灵活，你只需要遵循命名约定即可，无需手动维护 `TestSuite`。

**7. 跳过测试 (Skipping Tests) 和预期失败 (Expected Failures)**

有时，你可能想临时禁用某个测试（例如，因为它依赖的功能尚未完成，或者在特定环境下无法运行），或者你知道某个测试目前会失败（例如，修复一个已知 Bug 前）。`unittest` 提供了装饰器来处理这些情况：

*   `@unittest.skip(reason)`: 无条件跳过测试。`reason` 字符串会显示在测试报告中。
*   `@unittest.skipIf(condition, reason)`: 如果 `condition` 为 True，则跳过测试。
*   `@unittest.skipUnless(condition, reason)`: 除非 `condition` 为 True，否则跳过测试 (即 `condition` 为 False 时跳过)。
*   `@unittest.expectedFailure`: 标记一个测试为预期失败。如果这个测试运行失败了，`unittest` 不会将其计为失败 (会标记为 `x` 或 `expected failure`)；但如果这个测试意外地通过了，`unittest` 会将其计为失败 (会标记为 `U` 或 `unexpected success`)。

**示例:**

```python
import unittest
import sys

class TestSkipping(unittest.TestCase):

    @unittest.skip("Demonstrating skipping a test")
    def test_nothing(self):
        self.fail("This should not be executed") # fail() 会立即让测试失败

    @unittest.skipIf(sys.version_info < (3, 8), "Requires Python 3.8 or higher")
    def test_format_value(self):
        # 使用 Python 3.8+ 的 f-string 特性
        value = 123
        self.assertEqual(f"{value=}", "value=123")

    @unittest.skipUnless(sys.platform.startswith("win"), "Requires Windows")
    def test_windows_specific_feature(self):
        # 只有在 Windows 上才执行的测试
        self.assertTrue(True) # 假设这里是 Windows 相关操作

    @unittest.expectedFailure
    def test_fail_expectedly(self):
        # 这个测试目前已知会失败
        self.assertEqual(1, 0, "This is an expected failure")

if __name__ == '__main__':
    unittest.main(verbosity=2)
```

**运行结果可能类似:**

```
test_fail_expectedly (test_skipping.TestSkipping) ... expected failure
test_format_value (test_skipping.TestSkipping) ... skipped 'Requires Python 3.8 or higher'
test_nothing (test_skipping.TestSkipping) ... skipped 'Demonstrating skipping a test'
test_windows_specific_feature (test_skipping.TestSkipping) ... skipped 'Requires Windows' (如果在非 Windows 环境运行) 或 ok (如果在 Windows 环境运行)

----------------------------------------------------------------------
Ran 4 tests in 0.001s

OK (skipped=3, expected failures=1)
```

**8. 组织你的测试代码**

随着项目变大，如何组织测试代码变得很重要。推荐的做法是：

*   **将测试代码与源代码分开:** 通常创建一个 `tests` 目录与你的源代码目录 (如 `src` 或项目同名目录) 并列。
    ```
    my_project/
    ├── src/             # 或者你的包名 my_package/
    │   ├── __init__.py
    │   ├── calculator.py
    │   └── filehandler.py
    ├── tests/
    │   ├── __init__.py  # 让 tests 成为一个包，有助于 import
    │   ├── test_calculator.py
    │   └── test_filehandler.py
    └── README.md
    ```
*   **测试文件名与模块名对应:** 为 `calculator.py` 创建 `test_calculator.py`。
*   **测试类名与类名对应:** 如果测试 `Calculator` 类，测试类可以命名为 `TestCalculator`。
*   **使用子目录:** 如果测试很多，可以在 `tests` 目录下按功能或模块创建子目录。
    ```
    tests/
    ├── __init__.py
    ├── core/
    │   ├── __init__.py
    │   └── test_calculator.py
    └── io/
        ├── __init__.py
        └── test_filehandler.py
    ```
    在这种情况下，使用 `python -m unittest discover -s tests -t .` 依然可以找到所有测试。

**9. 结合实例：测试一个稍微复杂的类**

让我们为之前的 `FileHandler` 类添加更多功能，并编写更全面的测试。

```python
# filehandler.py (扩展后)
import os

class FileHandlerError(Exception):
    """自定义文件处理器异常"""
    pass

class FileHandler:
    def __init__(self, filepath):
        if not isinstance(filepath, str) or not filepath:
            raise ValueError("Filepath must be a non-empty string")
        self.filepath = filepath
        self.file = None
        self._is_open = False

    def open_file(self, mode='r'):
        """以指定模式打开文件"""
        if self._is_open:
            raise FileHandlerError("File is already open")
        try:
            self.file = open(self.filepath, mode)
            self._is_open = True
            print(f"File '{self.filepath}' opened in '{mode}' mode.")
        except FileNotFoundError:
            raise FileHandlerError(f"File not found: {self.filepath}")
        except Exception as e:
            raise FileHandlerError(f"Error opening file: {e}")

    def write_line(self, line):
        """写入一行，需要文件以写入模式打开"""
        if not self._is_open or 'w' not in self.file.mode and 'a' not in self.file.mode:
            raise FileHandlerError("File must be open in write or append mode to write")
        if not isinstance(line, str):
            raise TypeError("Line must be a string")
        self.file.write(line + '\n')

    def read_content(self):
        """读取所有内容，需要文件以读取模式打开"""
        if not self._is_open or 'r' not in self.file.mode:
             # 检查 'r' 或 '+' (读写模式)
            if '+' not in self.file.mode:
                raise FileHandlerError("File must be open in read mode to read")
            # 如果是 '+' 模式，可能需要先 seek 回文件头
            self.file.seek(0)

        return self.file.read()

    def close_file(self):
        """关闭文件"""
        if self.file and not self.file.closed:
            self.file.close()
            self._is_open = False
            print(f"File '{self.filepath}' closed.")
        self.file = None # 清理引用

    def __del__(self):
        """确保对象销毁时文件被关闭 (作为保险)"""
        self.close_file()

    @property
    def is_open(self):
        return self._is_open
```

**对应的测试 `test_filehandler.py`:**

```python
# test_filehandler.py
import unittest
import os
import tempfile
from filehandler import FileHandler, FileHandlerError # 确保从正确位置导入

class TestFileHandler(unittest.TestCase):

    def setUp(self):
        # 使用 tempfile.mkstemp 创建临时文件，更安全
        self.fd, self.temp_path = tempfile.mkstemp(prefix='test_fh_', suffix='.txt')
        os.close(self.fd) # 关闭 mkstemp 打开的文件描述符，以便 FileHandler 可以打开它
        self.handler = FileHandler(self.temp_path)
        # print(f"\nsetUp: Created temp file {self.temp_path}")

    def tearDown(self):
        if self.handler and self.handler.is_open:
            self.handler.close_file()
        if os.path.exists(self.temp_path):
            os.remove(self.temp_path)
        # print(f"tearDown: Removed temp file {self.temp_path}")

    def test_init_valid_path(self):
        """测试使用有效路径初始化"""
        self.assertEqual(self.handler.filepath, self.temp_path)
        self.assertFalse(self.handler.is_open)

    def test_init_invalid_path(self):
        """测试使用无效路径初始化是否抛出 ValueError"""
        with self.assertRaisesRegex(ValueError, "Filepath must be a non-empty string"):
            FileHandler("")
        with self.assertRaisesRegex(ValueError, "Filepath must be a non-empty string"):
            FileHandler(None)
        with self.assertRaisesRegex(ValueError, "Filepath must be a non-empty string"):
            FileHandler(123)

    def test_open_close(self):
        """测试基本的文件打开和关闭"""
        self.assertFalse(self.handler.is_open)
        self.handler.open_file('w')
        self.assertTrue(self.handler.is_open)
        self.assertIsNotNone(self.handler.file)
        self.assertFalse(self.handler.file.closed)
        self.handler.close_file()
        self.assertFalse(self.handler.is_open)
        self.assertTrue(self.handler.file is None or self.handler.file.closed) # close_file 会设为 None

    def test_double_open_raises_error(self):
        """测试重复打开文件是否抛出异常"""
        self.handler.open_file('w')
        with self.assertRaisesRegex(FileHandlerError, "File is already open"):
            self.handler.open_file('r')
        self.handler.close_file()

    def test_open_nonexistent_file_read_raises_error(self):
        """测试打开不存在的文件进行读取是否抛出异常"""
        non_existent_path = "surely_this_file_does_not_exist.txt"
        handler = FileHandler(non_existent_path)
        # 注意：FileNotFoundError 被包装在 FileHandlerError 中
        with self.assertRaisesRegex(FileHandlerError, f"File not found: {non_existent_path}"):
            handler.open_file('r')

    def test_write_read(self):
        """测试写入、关闭、再打开读取"""
        lines_to_write = ["Line 1", "Line 2", "Another Line"]
        expected_content = "\n".join(lines_to_write) + "\n"

        # 写入
        self.handler.open_file('w')
        self.assertTrue(self.handler.is_open)
        for line in lines_to_write:
            self.handler.write_line(line)
        self.handler.close_file()
        self.assertFalse(self.handler.is_open)

        # 读取
        reader_handler = FileHandler(self.temp_path) # 创建新实例或重新打开
        reader_handler.open_file('r')
        self.assertTrue(reader_handler.is_open)
        content = reader_handler.read_content()
        reader_handler.close_file()

        self.assertEqual(content, expected_content)

    def test_write_to_read_only_file_raises_error(self):
        """测试向只读模式打开的文件写入是否抛出异常"""
        self.handler.open_file('r') # 以只读模式打开
        with self.assertRaisesRegex(FileHandlerError, "File must be open in write or append mode"):
            self.handler.write_line("Should fail")
        self.handler.close_file()

    def test_read_from_write_only_file_raises_error(self):
        """测试从只写模式打开的文件读取是否抛出异常 (某些模式如 'w' 会清空文件)"""
         # 先写入一些内容，否则 'w' 模式打开后直接读是空的
        with open(self.temp_path, 'w') as f:
            f.write("Initial content\n")

        self.handler.open_file('w') # 以只写模式打开 (会清空文件)
        with self.assertRaisesRegex(FileHandlerError, "File must be open in read mode"):
            self.handler.read_content()
        self.handler.close_file()

    def test_write_non_string_raises_type_error(self):
        """测试写入非字符串是否抛出 TypeError"""
        self.handler.open_file('w')
        with self.assertRaises(TypeError):
            self.handler.write_line(123)
        with self.assertRaises(TypeError):
            self.handler.write_line(None)
        with self.assertRaises(TypeError):
            self.handler.write_line(["list"])
        self.handler.close_file()

    def test_append_mode(self):
        """测试追加模式"""
        # 第一次写入
        self.handler.open_file('w')
        self.handler.write_line("First")
        self.handler.close_file()

        # 第二次追加
        appender = FileHandler(self.temp_path)
        appender.open_file('a') # 追加模式
        appender.write_line("Second")
        appender.close_file()

        # 读取验证
        reader = FileHandler(self.temp_path)
        reader.open_file('r')
        content = reader.read_content()
        reader.close_file()
        self.assertEqual(content, "First\nSecond\n")

    # 可以添加更多测试，如读写模式 ('r+'), 大文件处理 (如果需要), 错误的文件模式等

if __name__ == '__main__':
    unittest.main(verbosity=2)
```

这个例子展示了如何测试类的初始化、方法调用、异常处理以及不同状态下的行为，并有效地使用了 `setUp` 和 `tearDown` 来管理临时文件资源。

**10. 编写良好单元测试的最佳实践**

*   **快 (Fast):** 单元测试应该运行得非常快。避免耗时的操作，如真实的数据库访问、网络请求（可以使用 Mock 技术替代，`unittest.mock` 是你的好朋友）。
*   **独立 (Independent/Isolated):** 每个测试应该可以独立运行，并且不依赖于其他测试的执行顺序或结果。`setUp/tearDown` 对此至关重要。
*   **可重复 (Repeatable):** 测试在任何环境下都应该产生相同的结果。避免依赖外部环境（如当前时间、特定文件系统结构）除非那是测试的一部分，并且要妥善处理。
*   **自验证 (Self-Validating):** 测试应该有明确的成功或失败结果（通过断言），不需要人工检查输出。
*   **及时 (Timely):** 最好在编写代码的同时或之前（TDD - 测试驱动开发）编写测试。不要等到最后才补测试。
*   **一个测试只测一件事:** 每个 `test_` 方法应该专注于验证一个具体的行为或场景。这使得测试失败时更容易定位问题。
*   **清晰的命名:** 测试类名和方法名应该清晰地描述它们测试的内容。
*   **测试边界条件和错误情况:** 不仅要测试正常情况 (happy path)，还要测试边缘情况（空值、零值、最大/最小值）、无效输入和预期的错误处理。
*   **保持测试代码简洁可读:** 测试代码也需要维护，写得清晰易懂很重要。
*   **利用 Fixtures:** 合理使用 `setUp/tearDown` 和 `setUpClass/tearDownClass` 来减少代码重复并确保资源管理。

**11. 总结**

`unittest` 是 Python 中进行单元测试的基础。通过掌握 `TestCase`、断言、Fixtures、测试发现等核心概念，你就可以开始为你的代码编写有效的单元测试了。

**关键回顾:**

*   测试类继承 `unittest.TestCase`。
*   测试方法以 `test_` 开头。
*   使用 `self.assert*` 方法进行断言验证。
*   使用 `setUp/tearDown` 管理每个测试的资源。
*   使用 `setUpClass/tearDownClass` 管理整个类的共享资源。
*   使用 `python -m unittest discover` 运行测试。
*   利用 `@unittest.skip*` 和 `@unittest.expectedFailure` 控制测试执行。
*   遵循最佳实践编写高质量的测试。

单元测试是保证软件质量、提高开发效率和信心的重要手段。虽然初学时可能会觉得有些额外工作量，但长远来看，它的收益是巨大的。现在，动手为你自己的项目添加 `unittest` 测试吧！实践是最好的学习方式。
