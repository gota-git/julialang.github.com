---
layout: manual
title:  Variables and Scoping
---
<cn>
---
layout: manual
title:  Variables and Scoping
---

</cn>

Until now, we have simply used variables without any explanation.
Julia's usage of variables closely resembles that of other dynamic languages, so we have hopefully gotten away with this liberty.
In what follows, however, we address this oversight and provide details of how variables are used, declared, and scoped in Julia.

The *scope* of a variable is the region of code within which a variable
is visible.
Variable scoping helps avoid variable naming conflicts.
The concept is intuitive:
two functions can both have arguments called `x` without the two `x`'s referring to the same thing.
Similarly there are many other cases where different blocks of code can use the same name without referring to the same thing.
The rules for when the same variable name does or doesn't refer to the same thing are called scope rules;
this section spells them out in detail.

Certain constructs in the language introduce *scope blocks*, which are regions of code that are eligible to be the scope of some set of variables.
The scope of a variable cannot be an arbitrary set of source lines, but will always line up with one of these blocks.
The constructs introducing such blocks are:

- `function` bodies (either syntax)
- `while` loops
- `for` loops
- `try` blocks
- `catch` blocks
- `let` blocks
- `type` blocks.

<cn>
直到现在，我值仅仅使用了变量，却没有对其进行过多的介绍。
Julia对变量的使用和其他动态语言对变量的使用非常相似，所以我们希望得到这个自由。
然而，几下了我们将阐述在Julia中变量的是怎么使用，声明以及有怎样的作用域的。

变量的*域*是一个变量的可见范围。
变量域使得可以避免命名的混乱。
这个概念非常直观：
两个函数可以同时拥有变量`x`，使得两个`x`指向不同的地方。
相似的，在很多其他情况下不同的代码块会用相同的名字指向不同的地方。
同样的名字是否指向同样的地方的规则就是这里讨论的域规则。
本节详细讨论了该主题。

语言中的介绍*域块*的结构是能表示的变量域的代码的范围。
变量的域不是任意的代码行集，但是会是连接这些代码块中的一个。
引进这些结构块有：

- `函数`体
- `while` 循环
- `for` 循环
- `try` 块
- `catch`块
- `let` 块
- `type` 块

</cn>
Notably missing from this list are [`begin`](../control-flow#Compound+Expressions) blocks, which do *not* introduce a new scope block.

Certain constructs introduce new variables into the current innermost scope.
When a variable is introduced into a scope, it is also inherited by all inner scopes unless one of those inner scopes explicitly overrides it.
These constructs which introduce new variables into the current scope are as follows:

- A declaration `local x` introduces a new local variable.
- A declaration `global x` makes `x` in the current scope
and inner scopes refer to the global variable of that name.
- A function's arguments are introduced as new local variables into the function's body scope.
- An assignment `x = y` introduces a new local variable `x` only if `x` is neither declared global nor explicitly introduced as local by any enclosing scope, before or *after* the current line of code.

In the following example, there is only one `x` assigned both inside and outside a loop:

    function foo(n)
      x = 0
      for i = 1:n
        x = x + 1
      end
      x
    end

    julia> foo(10)
    10

In the next example, the loop has a separate `x` and the function always returns zero:

    function foo(n)
      x = 0
      for i = 1:n
        local x
        x = i
      end
      x
    end

    julia> foo(10)
    0

<cn>
上面列表还少了个 [`begin`](../control-flow#Compound+Expressions)块,他*没有*引入一个新的域块。

这样的结构将新的变量引入了当前最深的域。
当一个变量被引入一个域时，它会被内部的域继承，除非内部域指明忽视它。
下面是将新变量引入到当前与的结构：

- `local x`声明引入一个新的局部变量。
- `global x`声明使得`x`在当前域。域内的变量指向全局变量。
- 一个函数的参数为函数体引入了一个新的局部变量。
- 如果在当前行的前面或*后面*都没用对变量`x`声明为全局变量或引入为局部变量，那么赋值表达式 `x = y`引入了一个新的局部变量`x`。

在下面的例子中，在内层和外层循环中只有一个`x`：

    function foo(n)
      x = 0
      for i = 1:n
        x = x + 1
      end
      x
    end

    julia> foo(10)
    10

下个例子中，循环体有个不同的`x`，函数总是返回`0`:

    function foo(n)
      x = 0
      for i = 1:n
        local x
        x = i
      end
      x
    end

    julia> foo(10)
    0

在这个例子中，`x`只存在在内循环中，因此函数在计算最后一行时会得到错误（除非在全局域中有个变量`x`才不会有错）：

    function foo(n)
      for i = 1:n
        x = i
      end
      x
    end

    julia> foo(10)
    in foo: x not defined

一个变量要么没有被分配，要么默认的将全局作用域里面的变量，因此上面的函数最终返回一个全局变量`x`或者没有全局变量`x`时会产生一个错误.
因此，只有这样才能将在非顶层域内明确某个变量是全局变量，否则的话将会引入一个新的作用域而不是使用全局变量。
这个规则在实践中表现的很好，想一下，在函数内部的大量变量都是局部变量，而使用全局变量则是一个意外，尤其是给全局变量赋值。

最后一个示例演示了对`x`的内部使用无需一个的外层分配来引入`x`：

    function foo(n)
      f = y -> n + x + y
      x = 1
      f(2)
    end

    julia> foo(10)
    13

最后一个示例中的变量看上去有点不同，但是对于一个命名函数-存储一个函数对象的变量-在声明它之前使用它。
这使得函数的定义可以被安排在直观和方便的位置而不是想C里面看到的那样强制在底部按顺序定义，或者要求前向声明。
作为一个示例，这里用了个效率不高的使用了递归的判断整数为奇数还是偶数的例子：

    even(n) = n == 0 ? true  :  odd(n-1)
    odd(n)  = n == 0 ? false : even(n-1)

    julia> even(3)
    false

    julia> odd(3)
    true

Julia体哦那个了内建的高效的函数测整数是否为偶数或者奇数，上面只是个示例。

函数可以在定义之前使用，直到他们被定义的时候才会被真正的调用，无需前向声明，因此可以随意的定义函数的顺序。

在交互式会话环境下，变量作用域也遵循上述规则。
交互式会话行为就像有个大块包含了你所有键入的内容，并将该块认为是全局块。尤其是在赋值的时候：
This is especially apparent in the case of assignments:

    julia> for i = 1:1; y = 10; end

    julia> y
    y not defined

    julia> y = 0
    0

    julia> for i = 1:1; y = 10; end

    julia> y
    10

</cn>
In the former case, `y` only exists inside of the `for` loop.
In the latter case, an outer `y` has been introduced and so is inherited within the loop.
Due to the special identification of the prompt's scope block with the global scope, it is not necessary to declare `global y` inside the loop.
However, in code not entered into the interactive prompt this declaration would be necessary in order to modify a global variable.

The `let` statement provides a different way to introduce variables.
Unlike assignments to local variables, `let` statements allocate new variable bindings each time they run.
An assignment modifies an existing value location, and `let` creates new locations.
This difference is usually not important, and is only detectable in the case of variables that outlive their scope via closures.
The `let` syntax accepts a comma-separated series of assignments and variable names:

    let var1 = value1, var2, var3 = value3
        code
    end

Unlike local variable assignments, the assignments do not occur in order.
Rather, all assignment right-hand sides are evaluated in the scope outside the `let`, then the `let` variables are assigned "simultaneously".
In this way, `let` operates like a function call. Indeed, the following code:

    let a = b, c = d
      body
    end

is equivalent to `((a,c)->body)(b, d)`.
Therefore it makes sense to write something like `let x = x` since the two `x` variables are distinct and have separate storage.
Here is an example where the behavior of `let` is needed:

    Fs = cell(2);
    for i = 1:2
      Fs[i] = ()->i
    end

    julia> Fs[1]()
    2

    julia> Fs[2]()
    2

<cn>
在之前的情况下，`y`只在`for`循环中。在后面的情况里，一个外层`y`被引入使得其继承了内部的循环。
由于交互式会话特殊的全局作用域，因此没有必要在内层循环中区声明 `global y`。
如果不是在交互式会话中，那么就必须要声明全局变量。

`let`语句提供了另一个引入变量的方法。
不像分配局部变量，`let`语句会在他们每次运行时都产生一个新的变量。
一次分配修改了已存在变量的位置，`let`会创建一个新的位置。
这通常不是问题，需要注意的是在变量通过作用域被关闭的情况。
`let`语法由逗号分隔的变量名：

    let var1 = value1, var2, var3 = value3
        code
    end

不想局部变量的赋值，赋值的顺序是不确定的。
然而，等号右边的表达式都是在`let`域外进行计算的，然后在右`let`进行同步赋值。
因此，`let`操作符就像一个函数调用：

    let a = b, c = d
      body
    end

上面的代码等价于`((a,c)->body)(b, d)`.
因此，将代码写成`let x = x`是有意义的，因为`x`会被区别对待，并单独存储。
下面是需要使用`let`的示例：

    Fs = cell(2);
    for i = 1:2
      Fs[i] = ()->i
    end

    julia> Fs[1]()
    2

    julia> Fs[2]()
    2

</cn>
Here we create and store two closures that return variable `i`. However, it is always the same variable `i`, so the two closures behave identically. We can use `let` to create a new binding for `i`:

    Fs = cell(2);
    for i = 1:2
      let i = i
        Fs[i] = ()->i
      end
    end

    julia> Fs[1]()
    1

    julia> Fs[2]()
    2

Since the `begin` construct does not introduce a new block, it can be useful to use the zero-argument let to just introduce a new scope block without creating any new bindings:

    julia> begin
             local x = 1
             begin
               local x = 2
             end
             x
           end
    syntax error: local x declared twice

    julia> begin
             local x = 1
             let
               local x = 2
             end
             x
           end
    1

<cn>
这里我们创建并存储了两个返回变量`i`的语句块，然而他们始终是同样的变量`i`，因此需要区别这两个`i`。这里可以使用`let`来创建一个新的`i`的绑定:

    Fs = cell(2);
    for i = 1:2
      let i = i
        Fs[i] = ()->i
      end
    end

    julia> Fs[1]()
    1

    julia> Fs[2]()
    2

由于`begin`不会引入一个新的块，这里使用let的空语句产生一个新的块：

    julia> begin
             local x = 1
             begin
               local x = 2
             end
             x
           end
    syntax error: local x declared twice

    julia> begin
             local x = 1
             let
               local x = 2
             end
             x
           end
    1


第一个例子是非法的，因为不能在同一个作用域里定义一个变量两次。
第二个示例是合法的，`let`引入了一个新的域块，使得内部的局部变量`x`和外面的不同。
</cn>
