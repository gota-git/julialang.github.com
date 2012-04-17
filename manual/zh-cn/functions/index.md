---
layout: manual
title:  Functions
---
<cn>
---
layout: manual
title:  Functions
---

</cn>

In Julia, a function is an object that maps a tuple of argument values to a return value.
Julia functions are not pure mathematical functions, in the sense that functions can alter and be affected by the global state of the program.
The basic syntax for defining functions in Julia is:

    function f(x,y)
      x + y
    end

This syntax is similar to MATLAB®, but there are some significant differences:

- In MATLAB®, this definition must be saved in a file, named `f.m`, whereas in Julia, this expression can appear anywhere, including in an interactive session.
- In MATLAB®, the closing `end` is optional, being implied by the end of the file.
In Julia, the terminating `end` is required.
- In MATLAB®, this function would print the value `x + y` but would not return any value, whereas in Julia, the last expression evaluated is a function's return value.
- Expression values are never printed automatically except in interactive sessions.
Semicolons are only required to separate expressions on the same line.

In general, while the function definition syntax is reminiscent of MATLAB®, the similarity is largely superficial.
Therefore, rather than continually comparing the two, in what follows, we will simply describe the behavior of functions in Julia directly.

There is a second, more terse syntax for defining a function in Julia.
The traditional function declaration syntax demonstrated above is equivalent to the following compact "assignment form":

    f(x,y) = x + y

In the assignment form, the body of the function must be a single expression, although it can be a compound expression (see [Compound Expressions](../control-flow#Compound+Expressions)).
Short, simple function definitions are common in Julia.
The short function syntax is accordingly quite idiomatic, considerably reducing both typing and visual noise.

A function is called using the traditional parenthesis syntax:

    julia> f(2,3)
    5

Without parentheses, the expression `f` refers to the function object, and can be passed around like any value:

    julia> g = f;

    julia> g(2,3)
    5

<cn>
Julia中一个函数是一个将一组参数值的元组映射到一个返回值的对象。Julia的函数不是准正意义上的数学函数，因为函数可以被修改或者被程序中的全局全局状态影响。
定义函数的基本语法为：

    function f(x,y)
      x + y
    end

这个语法和 MATLAB®非常相似,但是也有部分重要的不同之处：

- 在MATLAB®中, 这个函数必须保存在文件名为 `f.m`文件中,而在Julia中，该表达式可以出现在任何地方，甚至在交互式会话中。
- 在MATLAB®中,函数结束处的`end`是可选的，因为在文件的结尾处会自动加上。而在Julia中必须加上`end`。
- 在MATLAB®中, 这个函数会打印 `x +
  y`的值，但是不会返回任何值；而在Julia中，最后一个表达式的结果作为返回值。
- 除了在交互式会话中，表达式的值不会自动打印处理。
分号只用于分隔同一行含有的多个表达式。

通常情况下，定义函数的语法参照了MATLAB®,但是主要只是外形上相似。
因此，我们将只描述Julia函数的行为，而不将其于MATLAB®做比较叙述。

还有一种定义函数的第二种更简洁的方法。
上述的传统的函数定义语法等价于下面的紧凑的 "assignment form":

    f(x,y) = x + y

在`assignment form`中，函数体必须是单个表达式，也可以是符合表达式，参见( [Compound Expressions](../control-flow#Compound+Expressions)).
在Julia中，短小，简单的函数随处可见。
短小的函数被惯用的原因之一是他可以减少打字和阅读时的干扰。

调用函数的语法为：

    julia> f(2,3)
    5

当不传递参数时，表达式`f`指向一个函数对象，并且可以像其他值一样被传递：

    julia> g = f;

    julia> g(2,3)
    5

</cn>
There are two other ways that functions can be applied:
using special operator syntax for certain function names
(see [Operators Are Functions](#Operators+Are+Functions) below), or with the `apply` function:

    julia> apply(f,2,3)
    5

The `apply` function applies its first argument — a function object — to its remaining arguments.

## The "return" Keyword

The value returned by a function is the value of the last expression evaluated, which, by default, is the last expression in the body of the function definition.
In the example function, `f`, from the previous section this is the value of the expression `x + y`.
As in C and most other imperative or functional languages, the `return` keyword causes a function to return immediately, providing an expression whose value is returned:

    function g(x,y)
      return x * y
      x + y
    end

Since functions definitions can be entered into interactive sessions, it is easy to compare these definitions:

    f(x,y) = x + y

    function g(x,y)
      return x * y
      x + y
    end

    julia> f(2,3)
    5

    julia> g(2,3)
    6

<cn>
还有另为两种执行函数的方法：
为函数名使用特殊的操作语法，见( [Operators Are Functions](#Operators+Are+Functions) below),或者用`apply`函数:

    julia> apply(f,2,3)
    5

`apply`函数的第一个参数为一个函数对象，后面的是该函数对象的参数。 

## "return"关键字

默认情况下，函数会把函数体内最后一条表达式的计算结果作为返回值。
在示例函数中，前面描述的函数 `f`返回 `x + y`的计算结果.
在C或者其他大多数命令和函数语言中，`return`关键字使得函数立即返回其后表达式计算的结果:

    function g(x,y)
      return x * y
      x + y
    end

由于函数定义可以在交互式会话中执行，因此可以很容易的组合这些函数定义：

    f(x,y) = x + y

    function g(x,y)
      return x * y
      x + y
    end

    julia> f(2,3)
    5

    julia> g(2,3)
    6


</cn>
Of course, in a purely linear function body like `g`, the usage of `return` is pointless since the expression `x + y` is never evaluated and we could simply make `x * y` the last expression in the function and omit the `return`.
In conjunction with other control flow, however, `return` is of real use.
Here, for example, is a function that computes the hypotenuse length of a right triangle with sides of length *x* and *y*, avoiding overflow:

    function hypot(x,y)
      x = abs(x)
      y = abs(y)
      if x > y
        r = y/x
        return x*sqrt(1+r*r)
      end
      if y == 0
        return zero(x)
      end
      r = x/y
      return y*sqrt(1+r*r)
    end

There are three possible points of return from this function, returning the values of three different expressions, depending on the values of *x* and *y*.
The `return` on the last line could be omitted since it is the last expression.

## Operators Are Functions

In Julia, most operators are just functions with support for special syntax.
The exceptions are operators with special evaluation semantics like `&&` and `||`.
These operators cannot be functions since short-circuit evaluation (see [Short-Circuit Evaluation](../control-flow#Short-Circuit+Evaluation)) requires that their operands are not evaluated before evaluation of the operator.
Accordingly, you can also apply them using parenthesized argument lists, just as you would any other function:

    julia> 1 + 2 + 3
    6

    julia> +(1,2,3)
    6

<cn>
当然，在顺序结构函数体函数中，如`g`,使用`return`是多余的因为`x + y
`永远不会被执行，因此我门可以直接让`x * y`作最后一条语句而省略`return`。
当和其他控制流连接使用时，`return`就会发挥它真正的作用了。
下面示例，通过边长`x`,`y`计算了直角三角形的斜边的长度，并避免了溢出：

    function hypot(x,y)
      x = abs(x)
      y = abs(y)
      if x > y
        r = y/x
        return x*sqrt(1+r*r)
      end
      if y == 0
        return zero(x)
      end
      r = x/y
      return y*sqrt(1+r*r)
    end

该函数根据*x*和*y*的值的不同，有三个返回情况分别返回不同的表达式计算结果。
最后一个`return`可以省略不写。

## 操作符都是函数

在Julia中，大多数操作符仅仅是支持特殊语法的函数。除了一些特殊的执行语义如`&&`和`||`。
这些操作符不能成为函数的原因是因为他们的短路作用(参见 [Short-Circuit Evaluation](../control-flow#Short-Circuit+Evaluation))要求他们的参数在执行操作符之前不被执行。
因此，可以在操作符后面加上圆括号来使用他们，就像使用其他函数一样：

    julia> 1 + 2 + 3
    6

    julia> +(1,2,3)
    6

</cn>
The infix form is exactly equivalent to the function application form — in fact the former is parsed to produce the function call internally.
This also means that you can assign and pass around operators such as `+` and `*` just like you would with other function values:

    julia> f = +;

    julia> f(1,2,3)
    6

Under the name `f`, the function does not support infix notation, however.

## Anonymous Functions

Functions in Julia are first-class objects:
they can be assigned to variables, called using the standard function call syntax from the variable they have been assigned to.
They can be used as arguments, and they can be returned as values.
They can also be created anonymously, without giving them a name:

    julia> x -> x^2 + 2x - 1
    #<function>

This creates an unnamed function taking one argument and returning the value of the polynomial *x*^2 + 2*x* - 1 at that value.
The primary use for anonymous functions is passing them to functions which take other functions as arguments.
A classic example is the `map` function, which applies a function to each value of an array and returns a new array containing the resulting values:

    julia> map(round, [1.2,3.5,1.7])
    [1.0,4.0,2.0]

This is fine if a named function effecting the transform one wants already exists to pass as the first argument to `map`.
Often, however, a ready-to-use, named function does not exist.
In these situations, the anonymous function construct allows easy creation of a single-use function object without needing a name:

    julia> map(x -> x^2 + 2x - 1, [1,3,-1])
    [2,14,-2]


<cn>
中缀式等同于函数应用形式，事实上前者将在内部产生函数调用。
因此这也意味着可以将操作符如`+`,`*`像其他含素值一样传递和赋值。

    julia> f = +;

    julia> f(1,2,3)
    6

但是在函数名`f`中不支持中缀表达式。

## 匿名函数

函数是Julia中的第一类对象：
他们可以被赋值，使用标准的函数调用语法在他们被赋值的变量上被调用，作为参数，或者返回值。它们也可以被匿名创建：

    julia> x -> x^2 + 2x - 1
    #<function>

上述代码创建了一个匿名函数，他接受一个参数并返回多项式` *x*^2 + 2*x* - 1`的计算结果。
最开始使用匿名函数的场合是使用其他函数作为参数的函数。
一个经典的示例是`map`函数，它将对一个数组上的所有元素调用给定函数，并返回结果数组： 

    julia> map(round, [1.2,3.5,1.7])
    [1.0,4.0,2.0]

当已经存在的函数被作为第一参数传递给`map`时，上述代码将会正常工作，然而一个已经存在的命名函数不总是存在。
这时就需要匿名函数的创建一个特别用途的没有名字的函数来帮忙了:

    julia> map(x -> x^2 + 2x - 1, [1,3,-1])
    [2,14,-2]

</cn>
## Multiple Return Values

In Julia, one returns a tuple of values to simulate returning multiple values.
However, tuples can be created and destructured without needing parentheses, thereby providing an illusion that multiple values are being returned, rather than a single tuple value.
For example, the following function returns a pair of values:

    function foo(a,b)
      a+b, a*b
    end

If you call it in an interactive session without assigning the return value anywhere, you will see the tuple returned:

    julia> foo(2,3)
    (5,6)

A typical usage of such a pair of return values, however, extracts each value into a variable.
Julia supports simple tuple "destructuring" that facilitates this:

    julia> x, y = foo(2,3);

    julia> x
    5

    julia> y
    6

You can also return multiple values via an explicit usage of the `return` keyword:

    function foo(a,b)
      return a+b, a*b
    end

This has the exact same effect as the previous definition of `foo`.

<cn>
## 返回值多个值

在Julia中, 通过返回值的元组来模拟返回多个值。
然而，元组在创建和销毁时可以不用圆括号，因此使得可以在返回时直接返回多个值，而不用将他们放在一个元组中返回。
一下代码返回了一组值：

    function foo(a,b)
      a+b, a*b
    end

如果在交互式会话中执行上述代码并且没有将结果赋予任何变量，将会看见返回一个元组：

    julia> foo(2,3)
    (5,6)

将每个值赋予单个变量是返回一组值典型的应用。Julia提供了简单的元组“非构造化”机制：

    julia> x, y = foo(2,3);

    julia> x
    5

    julia> y
    6

也可以通过指明`return `来返回多个值:

    function foo(a,b)
      return a+b, a*b
    end

上述代码和前面的`foo`的作用一样。
</cn>
## Varargs Functions

It is often convenient to be able to write functions taking an arbitrary number of arguments.
Such functions are traditionally known as "varargs" functions, which is short for "variable number of arguments".
You can define a varargs function by following the last argument with an ellisis:

    bar(a,b,x...) = (a,b,x)

The variables `a` and `b` are bound to the first two argument values as usual, and the variable `x` is bound to an iterable collection of the zero or more values passed to `bar` after its first two arguments:

    julia> bar(1,2)
    (1,2,())

    julia> bar(1,2,3)
    (1,2,(3,))

    julia> bar(1,2,3,4)
    (1,2,(3,4))

    julia> bar(1,2,3,4,5,6)
    (1,2,(3,4,5,6))

In all these cases, `x` is bound to a tuple of the trailing values passed to `bar`.

On the flip side, it is often handy to "splice" the values contained in an iterable collection into a function call as individual arguments.
To do this, one also uses `...` but in the function call instead:

    julia> x = (3,4)
    (3,4)

    julia> bar(1,2,x...)
    (1,2,(3,4))

In this case a tuple of values is spliced into a varargs call precisely where the variable number of arguments go.
This need not be the case, however:

    julia> x = (2,3,4)
    (2,3,4)

    julia> bar(1,x...)
    (1,2,(3,4))

    julia> x = (1,2,3,4)
    (1,2,3,4)

    julia> bar(x...)
    (1,2,(3,4))

Furthermore, the iterable object spliced into a function call need not be a tuple:

    julia> x = [3,4]
    [3,4]

    julia> bar(1,2,x...)
    (1,2,(3,4))

    julia> x = [1,2,3,4]
    [1,2,3,4]

    julia> bar(x...)
    (1,2,(3,4))

Also, the function that arguments are spliced into need not be a varargs function (although it often is):

    baz(a,b) = a + b

    julia> args = [1,2]
    [1,2]

    julia> baz(args...)
    3

    julia> args = [1,2,3]
    [1,2,3]

    julia> baz(args...)
    no method baz(Int64,Int64,Int64)

As you can see, if the wrong number of elements are in the spliced container, then the function call will fail, just as it would if too many arguments were given explicitly.

## Further Reading

We should mention here that this is far from a complete picture of defining functions.
Julia has a sophisticated type system and allows multiple dispatch on argument types.
None of the examples given here provide any type annotations on their arguments, meaning that they are applicable to all types of arguments.
The type system is described in [Types](../types) and defining a function in terms of methods chosen by multiple dispatch on run-time argument types is described in [Methods](../methods).

<cn>
## 可变参数函数

有时书写可变参数函数是方便的，可变参数函数指的是参数的个数是可变的函数。
通过将最后一个参数写成省略号，可以定义可变参数函数（译者注：省略号用三个句点表示，`a,b,x...`，和`a,b,x,...`都可以，后者至少3个参数）：

    bar(a,b,x...) = (a,b,x)

变量`a` 和 `b`
和通常的一样绑定到前两个参数，变量`x`则绑定到一个含有零或传递到`bar`中前两个参数的参数个数迭代器容器：

    julia> bar(1,2)
    (1,2,())

    julia> bar(1,2,3)
    (1,2,(3,))

    julia> bar(1,2,3,4)
    (1,2,(3,4))

    julia> bar(1,2,3,4,5,6)
    (1,2,(3,4,5,6))

在以上情形中，`x`绑定到前两个参数外参数组成的元组。

相反的，也可以将一个迭代器容器里面的参数连接后传递给一个接受可变参数的函数。
这时，需要在函数调用时使用 `...` :

    julia> x = (3,4)
    (3,4)

    julia> bar(1,2,x...)
    (1,2,(3,4))

在此情况下，值的元组被连接后，被传递到可变参数该区的地方。
然而有时也并非这样：

    julia> x = (2,3,4)
    (2,3,4)

    julia> bar(1,x...)
    (1,2,(3,4))

    julia> x = (1,2,3,4)
    (1,2,3,4)

    julia> bar(x...)
    (1,2,(3,4))

此外，拼接成参数的迭代器对象不是必需为元组：

    julia> x = [3,4]
    [3,4]

    julia> bar(1,2,x...)
    (1,2,(3,4))

    julia> x = [1,2,3,4]
    [1,2,3,4]

    julia> bar(x...)
    (1,2,(3,4))

同样的，接受拼接成参数的函数也不必非要是可变参赛的函数：

    baz(a,b) = a + b

    julia> args = [1,2]
    [1,2]

    julia> baz(args...)
    3

    julia> args = [1,2,3]
    [1,2,3]

    julia> baz(args...)
    no method baz(Int64,Int64,Int64)

如你所见，如果传递错误数目的参数给参数拼接器，将会使得函数调用出错，就像传递过多参数一样。

## 深入阅读

我们需要提醒的是，这里还不是定义函数的完整的描述。Julia有一个复杂的类型系统,允许多个调度参数类型。
这里的例子中没有在参数中给出任何类型注释，意味着它可以用于任何类型的参数。
类型系统在这里描述[Types](../types)在运行时参数类型的多重调度的方法的术语定义函数见 [Methods](../methods).

</cn>
