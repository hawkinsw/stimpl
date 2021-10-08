# STIMPL

<img src="./assets/stimpy.png" align="left">

STIMPL is a Turing-complete imperative programming language -- it includes dynamically typed variables, mathematical expressions, (basic) console IO, loops, and conditionals. Though the binding of variables to types is done at runtime (in particular, the time of the first assignment), the language is strongly typed -- type errors are always detected! STIMPL has no scopes and no functions.

Here is a short example STIMPL program that assigns the value of 4 to a variable named _four_:

```
Program(Assign(Variable("four"), Add(IntLiteral(2), IntLiteral(2))))
```

You can _read_ that program like this:

> Assign variable _four_ to the result of the addition of the integer literal 2 with the integer literal of 2. 

Everything in STIMPL is an expression. In other words, everything in STIMPL has a type and a value. The most basic expression in STIMPL is the _ren_ \-- it has no value and a _unit_ type. In STIMPL you generate a ren like

```
Ren()
```

In STIMPL, you reference variables like

```
Variable("i")
```

Variable names are case sensitive in STIMPL.

A STIMPL program is a sequence of expressions:

```
Program(Ren(), Ren(), Ren())
```

The program above simply does nothing three times. In STIMPL, a sequence is synonymous with a program:

```
Sequence(Ren(), Ren(), Ren())
```

In general, the syntax for a program or sequence is `Program(_expression_[, _expression_[,...]])` or `Sequence(_expression_[, _expression_[,...]])`, respectively.

The value and type of a sequence of expressions (program, respectively) are the value and type of the final expression in the sequence. For example:

```
Program(Assign(Variable("five"), IntLiteral(10)),\
      IntLiteral(1))
```

and

```
Sequence(Assign(Variable("five"), IntLiteral(10)),\
      IntLiteral(1))
```

have a value of 1 and type of integer.

It stands to reason that because every expression in STIMPL has a value and a type, an assignment expression has a value and a type. An assignment expression's value and type are the value assigned and its type. For example, the assignment expression

`Assign(Variable("five"), IntLiteral(10))`

has a value of 10 and a type of integer.

In STIMPL, it's easy to print the value of an expression to the screen:

`Print(Ren())`

prints the value of the _ren_ to the screen. In general, the syntax for printing an expression is `Print(expr)` where `expr` is any expression.

STIMPL has _boolean_, _string_, _floating-point number,_ _integer_, and _unit_ types. You can perform the normal mathematical operations on integers and floating-point numbers:

```
Sequence(\
      Add(FloatingPointLiteral(5.0), FloatingPointLiteral(5.0)),\
      Subtract(IntLiteral(5), IntLiteral(5)),\
      Multiply(IntLiteral(5), IntLiteral(5)),\
      Divide(FloatingPointLiteral(25.0), FloatingPointLiteral(25.0)))
```

_**All operands are evaluated left-to-right.**_

You can also perform "addition" on strings -- concatenation:

```
Add(StringLiteral("testing"), StringLiteral(", one two three.")),
```

And, we can't forget about booleans!

```
BooleanLiteral(True)
BooleanLiteral(True)
```

You can operate on booleans with and, or and not:

```
And(BooleanLiteral(True), BooleanLiteral(False))  
Or(BooleanLiteral(True), BooleanLiteral(False))  
Not(BooleanLiteral(True))
```

And, you can create booleans with relational operators:

```
Lt(BooleanLiteral(False), BooleanLiteral(True))  
Lte(IntLiteral(5), IntLiteral(5))  
Eq(StringLiteral("testing"), StringLiteral("testing"))  
Ne(StringLiteral("t3sting"), StringLiteral("testing"))  
Gt(StringLiteral("beta"), StringLiteral("alpha"))  
Gte(IntLiteral(5), IntLiteral(5))
```

The relational operators are defined on all types (see below for the exact details)!

That's all well and good and gives us the power to write sequential programs. But, what about programs that operate selectively? STIMPL has if expressions:

```
If(And(BooleanLiteral(False), BooleanLiteral(True)),\
      Print(StringLiteral("Then")),\
      Print(StringLiteral("Else")))
```

That expression will print

```
Else
```

and have a value of `"Else"` and string type. In general, the syntax for an if expression is `If(condition, then, else)` where `condition` is any expression whose type is boolean and `then` and `else` are expressions. If you don't want to do anything in the case that `condition` is false, use `Ren()` as the `else` expression.

And, don't forget loops:

```
Program(\\
      Assign(Variable("i"), IntLiteral(0)),\
      While(Lt(Variable("i"), IntLiteral(10)),\
        Sequence(\
          Assign(Variable("i"), Add(Variable("i"), IntLiteral(1))),\
          Print(Variable("i")))\
        )\
      )
```

That program will print:

```
1  
2  
3  
4  
5  
6  
7  
8  
9  
10
```

In general, the format of a while loop is `While(condition, body)` where `condition` is any expression with a boolean type and `body` is any expression. The value and type of a while loop are false and boolean.

# STIMPL Requirements

## Type Requirements

Any time that there is a type error, STIMPL will raise an `InterpTypeError`. STIMPL has compile time and runtime type errors. Here are the compile-time type rules:

1.  Literals must be the appropriate type:
    1.  An `IntLiteral` must be created from a Python `int`.
    2.  A `FloatingPointLiteral` must be a Python `float`.
    3.  A `StringLiteral` must be a Python `str`.
    4.  A `BooleanLiteral` must be a Python `bool`.

If these rules are violated, STIMPL raises an `InterpTypeError` at the time the program is defined.

Here are the runtime type rules:

1.  The first assignment to a variable defines that variable's type.
2.  Once a variable's type has been defined, only expressions of matching type can be assigned to that variable.
3.  Operands to binary operators must have the same type.
4.  Relational operators are defined for (matching) operands of every type (see below for the exact details).
5.  And/or/not operators are only defined for (matching) operands of boolean type.
6.  Add, Subtract, Multiply and Divide operators are defined for (matching) operands of integer and floating-point types.
7.  The add operator is defined for (matching) operands of string types.
8.  The condition expression in if/while expressions must have a boolean type.

If any of these these rules is violated, STIMPL raises an `InterpTypeError` at runtime.

**Examples**

By compile-time type rule (1),

```
FloatingPointLiteral(10)
IntLiteral(1.0)
StringLiteral(True)
BooleanLiteral("False")
```

will all cause `InterpTypeError`s. By runtime type rules (1) and (2),

```
Program(\
      Assign(Variable("i"), IntLiteral(10)),\
      Assign(Variable("i"), FloatingPointLiteral(10.0))\
    )
```

will cause a `InterpTypeError`. By runtime type rule (3)

```
Add(IntLiteral(5), FloatingPointLiteral(10.0))
```

will cause a `InterpTypeError`. By runtime type rule (5)

```
Program(Not(IntLiteral(5)))
```

will cause an `InterpTypeError`. By rule (8),

```
If(IntLiteral(1),
   IntLiteral(0),\
   IntLiteral(1))
```

will cause an `InterpTypeError`.

## Syntax

Because STIMPL programs are syntactically correct Python programs, most syntax errors (e.g., mismatched parenthesis) will be caught by the Python interpreter. However, there are two syntax errors that STIMPL handles explicitly:

1.  It is a syntax error to assign to an expression that is not a variable. If this is detected, STIMPL raises an `InterpSyntaxError` at compile time.
2.  It is a syntax error to read from a variable that does not have a value. If this is detected, STIMPL raises an `InterpSyntaxError` at runtime.

By rule (1),

```
Program(Assign(IntLiteral(10), IntLiteral(10)))
```

will cause an `InterpSyntaxError` at compile time. By rule (2),

```
Program(Variable("i"))
```

will raise an `InterpSyntaxError` at runtime.

## Semantics

1.  Relational operators behave "as usual" for integer and floating-point types.
2.  Relational operators perform [lexicographical comparison](https://en.wikipedia.org/wiki/Lexicographic_order) for string types.
3.  False is less than true.
4.  Unit is equal to unit.
5.  Boolean operators behave "as usual".
6.  Add, Subtract, Multiply and Divide operators work "as usual" on floating-point values.
6. The divide operator performs integer division when its parameters are integers (_e.g._, e.g., 5/10 = 0)
7. Add operator performs string concatenation when its operands are string values.
8. Operands are evaluated left-to-right.
9. There is _no_ short-circuit evaluation.
10. The body of a while loop is repeatedly executed until the condition becomes false.
11. The then branch of an if statement is executed when the condition is true; the else branch of an if statement is executed otherwise.

## Values and Types

1. Literals have the expected values and types.
2. The value and type of an assignment expression is the value and type of the right-hand side of the expression.
3. The value and type of a relational expression is the result of the relation and its type is boolean.
4. The value of a mathematical operation is the result of the mathematical operation and its type is integer or floating point, depending on the type of the parameters.
5. The value and type of an if expression is the value and type of the last expression in the sequence of expressions executed based on the value of the condition.
6. The value and type of a while expression is false and boolean.

## STIMPL Implementation

You have been given a significant amount of skeleton code to start your implementation. Begin this assignment by understanding what is included.

## State

As the program executes, it always has a state to hold the current value of the program's variables and their types. Use the `State` class defined in the provided code (`stimpl/runtime.py`). To update values in a state, use the `set_value` method. The `set_value` method takes three parameters: The variable name, the variable value and the variable type. **_`set_value` will not update the state in place -- it will return a copy of the existing state with the appropriate variable updated_**. To retrieve a value from the current state, use the `get_value` method. The `get_value` method returns a tuple whose first element is the variable value and whose second element is the variable type; if `get_value` is called for a variable that is not yet defined in the current state, `None` is returned.

## Evaluate

`evaluate` (stimpl/runtime.py) is the main driver of the STIMPL interpreter. As parameters, it takes a variable whose type is an expression and a variable whose type is a program state.

```
def evaluate(expression, state):
```

`evaluate` uses [pattern matching](https://uc.instructure.com/courses/1476336/pages/pattern-matching-in-python "Pattern Matching in Python") to determine the specific type of expression to be evaluated. Depending on the result of that determination, a specific set of code is executed. That code generates three things:

1.  The expression's value.
2.  The expression's type.
3.  An updated program state.

These three items are returned as a 3-tuple. For instance, if

```
Variable("i")
```

and `State()` were passed as `expression` and `state` (respectively) to evaluate,

```
case Variable(variable_name=variable_name):
      value = state.get_value(variable_name)
      if value == None:
        raise InterpSyntaxError(f"Cannot read from {variable_name} before assignment.")
      return (*value, state)
```

would execute. This implementation code generates a 3-tuple `(_value_, _type_, state)` where _`value`_ and `_type_` are the value and type of `i`, respectively. Notice that the "updated" program state after evaluating this expression is no different than the program state before evaluating this expression. In other words, accessing the value of a variable does not change the program's state! Remember operational semantics!

Take a very close look at the expressions that are already implemented in `evaluate` -- there is a pattern that should emerge that will help you implement the remaining functionality!

## Interpreter Errors

There are two pre-defined exceptions for you to use to signal a program error -- `InterpTypeError` and `InterpSyntaxError` (`stimpl/errors.py`).  You can `raise` these exceptions to signal errors according to the specifications of STIMPL.

## Types

There are classes already defined for the integer, floating-point, string and boolean types (`stimpl/types.py`). These classes already have built-in functionality for equality testing. In other words,
```
  FloatingPoint() == FloatingPoint()
  String() == String()
```

etc. You will want to use this built-in equality functionality when checking to make sure that operands to operators are of matching type and to determine, for example, whether an operand to a boolean operator is a boolean type.

## Literals

There are classes already implemented for all literals (`stimpl/expression.py`). You may use these as they are -- they need no modification to meet the requirements of STIMPL.

## Binary, Unary and Combining-Form Expressions

There are classes already implemented to hold the structure of the binary, unary and combining-form expressions (`stimpl/expression.py`). You may use these as they are -- they need no modification to meet the requirements of STIMPL.

## Execution

`run_stimpl` (`stimpl/runtime.py`) takes a STIMPL program as a parameter and evaluates it. `run_stimpl` takes an optional second parameter to control whether debugging output is enabled. Calling `run_stimpl` with `True` as the second parameter will cause debugging output to be produced during evaluation of the STIMPL program. If the argument is missing, the default is to suppress debugging output.

## Testing

`run_stimpl_sanity_tests` (`stimpl/test.py`) is a function that will help you determine whether your implementation is "complete". Based on the skeleton code provided, one (or many) tests may fail. Guide your work on this assignment by getting each of the tests in `run_stimpl_sanity_tests` to pass.

# Assignment Requirements

Your assignment is to build on the provided STIMPL code and complete the implementation of the interpreter.

## Getting Started

The first step is to download the skeleton code. It is available on GitHub at [https://github.com/hawkinsw/stimpl](https://github.com/hawkinsw/stimpl). If you are new to git/github, check out this [handbook](https://guides.github.com/introduction/git-handbook/) from GitHub or the project's [website](http://git-scm.com/).

The next step is to make sure that you have Python 3.10 installed and available. Python 3.10 is not quite officially released. But that's okay, it's far enough along that we can use it! You can find the latest information on how to access it at Python [website](https://www.python.org/downloads/release/python-3100rc2/).

The final step before you get started programming is to make sure that you can execute the code in `shakedown_stimpl.py`.If you have a sane Python 3.10 installation and everything configured correctly, you should see

```
Hello, World
```

printed on the console when you execute `shakedown_stimpl.py`

In order to judge your progress on the assignment, use the code in test\_stimpl.py. You will know that you are almost done with this assignment when you execute the code in that file and see

```
All tests ran successfully!
```

printed on the screen! In fact, if you see that message, you will know that your grade will be at least 80% (see below).

## Grading

Your grade will be calculated (out of 100 total possible points) according to the following rubric:

|Points|Category|Description|
|---|---|---|
|  80 | Implementation completeness  |  You will receive up to 80 points depending on how many of the supplied tests pass on your implementation. See stimply/test.py for the exact relationship between points and tests. |
|  10 | Robustness  | You will receive up to 10 points depending on whether your implementation passes additional robustness tests.  |
| 10 | Hygiene | You will receive up to 10 points depending on the hygiene of your code -- good comments, good style, good variable names, modularity, etc. |


