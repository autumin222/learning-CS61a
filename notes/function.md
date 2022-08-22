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

## 高阶函数

接受其他函数作为参数，或将函数作为返回值的函数

#### 将函数作为参数接受

多个函数拥有相似的结构时，则存在这样的抽象可以

```py
def summation(n, term):
2        total, k = 0, 1
3        while k <= n:
4            total, k = total + term(k), k #term 为传入的函数参数
5        return total
6    
7    def cube(x):
8        return x*x*x
9    
10    def sum_cubes(n):
11        return summation(n, cube)
12    
13    result = sum_cubes(3)
```

此时我们拥有的一般化的求和函数，只需要传入特定的求和处理就可解决求和问题

#### 作为通用方法的函数

一些函数表达了一般的计算方法，独立于它们调用的特定函数。

```py
def improve(update, close, guess=1):
        while not close(guess):
            guess = update(guess)
        return guess
```

这个`improve`函数是重复细化的一般表达。

它没有说明要解决什么问题：这些细节留给作为参数传入的`update`和`close`函数。

以下是一个估计黄金比例的函数：

```py
1 def improve(update, close, guess=1):
2        while not close(guess):
3            guess = update(guess)
4        return guess
5    
6    def golden_update(guess):
7        return 1/guess + 1
8    
9    def square_close_to_successor(guess):
10        return approx_eq(guess * guess,
11                         guess + 1)
12    
13    def approx_eq(x, y, tolerance=1e-3):
14        return abs(x - y) < tolerance
15    
16    phi = improve(golden_update,
17                  square_close_to_successor)
```

#### 嵌套定义

许多细小的函数定义会**污染全局环境**，并且由于函数需要作为内部参数调用，它的**参数形式**会受到约束。

这两个问题的解决方案是**将函数定义放在其他定义的主体中。**

```py
def sqrt(a):
        def sqrt_update(x):
            return average(x, a/x)
        def sqrt_close(x):
            return approx_eq(x * x, a)
        return improve(sqrt_update, sqrt_close)
```

- 这些函数仅在评估 sqrt 时在范围内。这些本地 def 语句甚至在调用 sqrt 之前都不会被评估。

- 至关重要的是，内部函数可以访问定义它们的环境(父环境)中的名称（而不是调用它们的位置）。

- 在嵌套定义中，我们创建了一个框架链：调用 `sqrt_update` 的环境由三个框架组成：本地 `sqrt_update` 框架、定义 `sqrt_update` 的 sqrt 框架（标记为 f1）和全局框架。

- 

#### 将函数作为返回值

可以实现动态创建函数

```py
def make_adder(n):
    """
    >>>add_three = make_adder(3)
    >>>add_three(4)
    7
    >>>make_adder(2)(3)
    5
    """
    def adder(k):
        return k + n
    return adder
```

#### lambda 表达式

即匿名函数，像是函数的简便声明实现方式

```py
square = lambda x: x * x
square(10)  #100
```

- 可以在实现后赋值给一个变量名

- 也可以立即调用，但由于没有名字，只能立即调用，且只能调用一次

- 与使用`def` 声明函数的不同：内部不能使用自己的函数名称，即不存在函数内在名称

#### 函数装饰器

应用高阶函数作为执行 def 语句的一部分，称为装饰器。

```py
>>> def trace(fn):
        def wrapped(x):
            print('-> ', fn, '(', x, ')')
            return fn(x)
        return wrapped
>>> @trace
    def triple(x):
        return 3 * x
>>> triple(12)
->  <function triple at 0x102a39848> ( 12 )
36
```

定义函数时使用装饰器，则调用该函数时，装饰器会进行预处理

使用装饰器的步骤等价于:

```py
>>> def triple(x):
        return 3 * x
>>> triple = trace(triple)
```

使用包装函数将定义的函数包装起来，这样调用被包装的函数时，则会在包装函数的环境里执行

## 函数递归

当递归通过事实函数的连续应用“展开”到更简单的问题实例时，**最终从基本案例开始构建结果。**

事实上，我们将函数递归视为一种抽象，我们只是简单的在函数内部使用这个抽象，相信它会返回我们希望的结果

#### 相互递归

- 递归过程由两个函数相互调用，

- 通过打破两个函数之间的抽象边界，可以将相互递归的函数变成一个单一的递归函数。

- 因此，相互递归并不比简单递归更神秘或更强大，它提供了一种在复杂递归程序中维护抽象的机制。

#### 树递归

函数不止一次地调用自己,在调用过程中会形成树状结构

- **斐波那契数列**

```py
def fib(n):
    if n == 1:
        return 0
    elif n == 2:
        return 1
    else:
        return fib(n - 1) + fib(n - 2)
```

- 这并不是一个有效的解决方案，因为计算过程出现了大量的重复

- **汉诺伊塔**

- 如果我们要将三个饼移动到第二个柱子上，则需先将两个饼移动到第三个柱子上，再将剩下的一个移动到第二个，最后将剩下的两个移动到第二个
  
  ```py
  hanoi(3, 1, 2)
      hanoi(2, 1, 3)  # 移除上层
      move(3, 1, 2)   # 移动最下面的一个
      hanoi(2, 3, 1)  # 移回上层
  ```
