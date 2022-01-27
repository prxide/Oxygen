# **A simple interactive interpreter written in python**

## Installation

To run the program, simply download the source code and run main.py with Python 3.9+. <br> You won't need any additional
libraries.

## Syntax

**Note: The `>` at the beginning of the line will indicate user input, the following line will be the interpreter
output.**

### **Declaring Variables**

---

This interpreter has 2 different datatypes: numbers(which will always be floats) and bools. <br>
Variables are assigned with the identifier on the left, an equals sign, and the value on the right. Identifiers can only
consist of uppercase letters, lowercase letters and underscores. Example:

```python
> x = 3
3
```

Nested and chained assignments are also possible:

```python
> x = y = 3
3
> x = 5 + (y=4)
9
> y
4
```

Booleans are assigned the same way, except there must be some kind of boolean statement on the right. This can be either
be "True","False" or some comparison. Numbers can be converted to bools too; 0 will be converted to False while any
other number will be converted to True. Other datatypes like functions can not be converted to bools. Some examples:

```python
> x = True
True
> x = 2 > 3
False
> x = !0
True
```

### **Operations**

---

The interpreter will be able to evaluate the following operations:

- Add, Subtract, Multiply, Divide with the common operators
- Operation assignment with +=, -=, *=, /= and %=
- Modulus with %
- Exponentiate with ^
- Comparison operators:  ==, !=, <, >, <=, >=
- Logical operator NOT: Can be used either by "!" or "not"
- Logical operator AND: Can be used either by "&" or "and"
- Logical operator OR: Can be used either by "|" or "or"

Operator precedence will follow the common order:

```python
> x = 2+3*4
14
> x = (2+3)*4
20
```

Chained boolean comparisons are possible. If any type of comparison is put in brackets, it will always be converted to a
boolean value first, which will prevent chaining of comparisons:

```python
# 3<5<10 is a chained comparison. It will be evaluated to True, as (3<5) and (5<10) are True.
> 3 < 5 < 10
True
# 5==5==5 is a chained comparison. It will be evaluated to True.
> 5 == 5 == 5
True
# The brackets will cause (5 == 5) to be converted to a boolean, leaving "True == 5", which is false.
> (5 == 5) == 5
False
```

When implicitly converting a boolean value to a number, `False` will be equal to 0 and `True` will be equal to 1.

```python
> x = True
> x == 1
True
```

### **Functions**

---

Functions are declared using the keyword `fn`, the identifier, the arguments listed with spaces inbetween, the operator
=> and the function body (including the expression the function should return).
> Declaring the function "add" that takes two arguments and returns the sum:

```python
> fn add x y => x + y
```

Functions are called using the identifier and the respective arguments in brackets; separated by commas.
> Calling the "add" function:

```python
> sum = add(7, 3)
10
```

Alternatively, if the function takes exactly one argument, it is also callable with the identifier, a period, and the
argument.

```python
> fn add_two x => x + 2
> add_two.2
4
```

If there is a name conflict between a function variable and a global variable, the function variable will take
precedence
(emulating local and global scope).

```python
> x = 5
> add_two(4)
6
```

### **Built-in functions and constants**

---

Currently, there are the following usable built-in functions:

- `sqrt` (square root)
- `sin`, `cos`, `tan`
- `asin`, `acos`, `atan`
- `abs`
- `factorial`
- `bool` - *Note: This will attempt to convert anything into a bool. <br> The same thing will be done automatically in
  the context of a boolean expression*

They are called just like normal functions: `keyword.factor` or `keyword(expression)`

*Example:*

```python
> sqrt.81
9
> sqrt(80+1)
9
```

There also are a few built-in mathematical constants: pi, e, h, and golden (for the golden ratio).
> Example: Utilizing pi to declare a function which takes in a radius and returns the circumference of the circle

```python
> fn get_circumference radius => 2 * pi * radius
> get_circumference(2)
12.566370614359172
```

### **Output**

---

Output for a valid expression will be the result of the expression.<br>
Output for a valid function declaration will be nothing. <br>
Output for input consisting entirely of whitespace will be nothing. <br>
Output for any input preceded by `/` will be nothing and the input will be ignored. <br>
A warning will be displayed if the user tries to override a built-in function.

### **Errors**

---

Exceptions will be thrown for invalid Expressions. <br>
*Some examples*:

```
> 4 + (4 + 1
SyntaxError: Expected a closing parenthesis.
> y + 1
NameError: Name "y" is not defined.
> fn add a b => a + b
> add(5)
SyntaxError: Expected 2 arguments, got 1.
> x = 2
> x()
TypeError: float object is not callable
> fn add a b = a + b
SyntaxError: Expected "=>"
```

## **Code examples**

> Evaluating an expression and comparing it to 1

```python
> 1 == (0.5 + 2 / 2^2)
True
```

> Defining a function which can calculate c in a triangle according to the theorem of pythagoras

```python
> fn get_c a b => sqrt(a^2+b^2)
> get_c(3,4)
5
```

> Defining a function that reverses a boolean value

```python
> fn reverse boolean_value => !boolean_value
reverse(1==1)
False
```

> Defining a function which takes the values a, b and x and returns True if x is between a and b (and vice versa)

```python
fn is_in_range a b x => a<x<b
is_in_range(0, 5, 3)
True
is_in_range(-2, 2, -4)
False
```

## **Functionality**

Here's a quick summary on how it works: <br>
First, the **lexer** will convert the input string into a list of `tokens`. I used `namedtuples` for that. <br>
Then, the **parser** will recursively go over these tokens and put them into defined nodes (also `namedtuples`),
following the common order. <br>
Lastly, the generated tree of operations will be recursively evaluated in the **interpreter**. <br>

#### Example input:

```python
x = 5 + 1
```

#### **Lexer** output:

```python
[token(type='IDENTIFIER', value='x'), token(type='EQUALS', value=None), token(type='NUMBER', value=5.0), token(type='PLUS_SIGN', value=None), token(type='NUMBER', value=1.0)]
```

#### **Parser** output:

```python
AssignNode(identifier='x', value=AddNode(a=5.0, b=1.0))
```

#### **Interpreter** output:

```
6
```
