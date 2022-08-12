1. 使用函数前需要了解函数的定义域，值域以及两者对应关系（*domain, range, inten*）

## 函数设计

#### 好函数是怎样的

1. 有具体确定的功能。实现多个复杂功能的函数应该被拆分成更具体的函数

2. 增加代码复用性，不要一直复制粘贴，用函数封装重复使用的逻辑

3. 定义更一般性的功能，更特殊的功能应该用其衍生出来

#### 使用注释（文档字符串）描述函数功能，用法

1. 第一行描述函数的作用

2. 接下来的描述函数参数并明确函数的行为
   
   **使用help指令会提示函数的文档字符串**

3. 文档字符串使用三个双引号
   
   ```python
   def pressure(v, t, n):
           """Compute the pressure in pascals of an ideal gas.
   
           Applies the ideal gas law: http://en.wikipedia.org/wiki/Ideal_gas_law
   
           v -- volume of gas, in cubic meters
           t -- absolute temperature in degrees kelvin
           n -- particles of gas
           """
           k = 1.38e-23  # Boltzmann's constant
           return n * k * t / v
   ```

#### 使用默认参数简化函数使用

- 函数体中使用的大多数数据值应表示为命名参数的默认值，以便它们易于检查并且可以由函数调用者更改。

- 一些常量（如玻尔兹曼常数k），可以绑定在函数体或全局框架中

#### 测试函数

- 测试函数是验证函数的行为是否符合预期的行为。

- 断言语句：断言语句在布尔上下文中有一个表达式，后跟一个带引号的文本行（单引号或双引号都可以，但要一致），如果表达式的计算结果为假值，则将显示该行。
  
  ```py
  assert fib(8) == 13, 'The 8th Fibonacci number should be 13'
  ```

- 测试函数应该测试多种参数，包括极值
  
  ```py
  def fib_test():
          assert fib(2) == 1, 'The 2nd Fibonacci number should be 1'
          assert fib(3) == 1, 'The 3rd Fibonacci number should be 1'
          assert fib(50) == 7778742049, 'Error at the 50th Fibonacci number'
  ```

- 测试函数应该写在同一个文件中或后缀为 _text.py 的文件中

- 使用文档测试将简单的测试直接放在函数的文档字符串中，通过示例交互式会话
  
  ```py
  def sum_naturals(n):
          """Return the sum of the first n natural numbers.
  
          >>> sum_naturals(10)
          55
          >>> sum_naturals(100)
          5050
          """
          total, k = 0, 1
          while k <= n:
              total, k = total + k, k + 1
          return total
  ```

- 导入测试模块
  
  1. `testmod()`简单测试
     
     ```py
     from doctest import testmod
     testmod()
     ```
  
  2. `run_docstring_examples`详细测试
     
     三个参数，第一个参数为测试函数名，第二个总是`globals()`的结果，第三个是 True 表示我们想要“详细”输出：所有测试运行的目录。
     
     ```py
     from doctest import run_docstring_examples
     run_docstring_examples(sum_naturals, globals(), True)
     ```

- 有效测试的关键是在实现新功能后立即编写（并运行）测试。

- 在实现之前编写一些测试甚至是一种很好的做法，以便在您的脑海中有一些示例输入和输出。
