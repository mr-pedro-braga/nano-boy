# NanoScript Specification

# Introduction
Greetings, Reader.\
Today I bring you my new random project: Nano Script.

*Nano Script* is a *fantasy* programming language that exists within the universe of my written works.
In it, it's a language developed by Nanosoft for its console, the NanoBoy.

So, uh, it's not real. Yet here I am creating a specification for it.

```python
x = 3
y = 4
dist = fn a, b -> a + b

print dist 3, 4 # Will print out '5'.
```

Nano Script is designed to be simple, compact, easy to understand, and very convenient.
It's not specifically writen with speed in mind, so its features may not make the most sense,
instead, it's written based on what looks the coolest to me specifically, only loosely trying to be practical.

Let's see what it's about!!!

---

# Design Philosophy

Like I said -- looking cool from the perspective of me specifically, which means what I chose to include is quite arbitrary.
This language is based very loosely on Python, GDScript, CoffeeScript, Rust, C++, Lua and ASM.

1. **READABILITY**\
  I want to make a language that's easy to read at a glance (when you know what the symbols mean)
  and that feels logic and lightweight so you can look at it on a small screen.
  I also allow to use indents instead of braces and line terminators, because it looks so clean.

2. **HIGH-LEVEL**\
  It should be powerful and largely declarative... you tell it what you want to happen.
  For that, I want its syntax to feel customizable and language-like, so you can have things like
  `colour = albedo * normal dotted_with (light.position - position)`.

3. **EVERYTHING IS A NOUN**\
  Values, Behaviour, almost everything is a noun that can be set, retrieved, reassigned.
  Functions, the variables that contain your code's behaviour, can be interacted with as objects.

4. **PARADIGM-AGNOSTIC**\
  The features of the language don't force you to use them.
  You can program your entire codebase without using classes or signals if you want,
  they are here to help you, not to hold you hostage.

5. **EXTENSIBLE**\
  Libraries and your own codebase should look and behave like native code.
  You should be able to just plug in your libraries and immediately have extended functionality.
  For example, a library that allows you to create a window should behave something like this:
  ```python
  Window = load('lib/Window.nsl')
  my_window = Window.create title:'My Window', size:v[640, 360]
  my_window.show Window.CENTERED
  ```
  and it would *just work*.

---

Here's how the code for a little screen with a player should look like:
```coffeescript
with load('Game.ns')
  signal on_begin
  signal on_death
  
  player =
    position: v[0, 0]
    velocity: v[0, 0]
    sprite: load('img/player.png')
    💕: 3
    🛡️: 4
    items: ['Sword', 'Apple', 'Key']
    update: fn dt:float ->
      position += velocity * dt

  ready = fn -> on_begin()

  update = fn dt ->
    player.update dt
    player.velocity = Input.get_L_joystick_vector()
    if player.💕 <= 0
      on_death()

  draw = fn ->
    # Draw Player
    draw player.sprite, player.position
```

---

# Syntax

Let's take a closer look at how to write NanoScript code.

## Features

### Comments

```coffeescript
# Comments are done with a single hash (#) for line comments...

###
Or three hashes for multiline comments!!!
###
```

### Variables

You can declare variables by simply assigning to them as if they already exist.

```coffeescript
x = 3
y = 4
```

You may *force* a variable to be declared/redeclared using `var`.

```coffeescript
x = 3
y = fn ->
  var x = 4
  print x #Will print 4
print x #Will print 3
```

The right-hand sign of the assignment can be any expression that ultimately returns a value.
This is very useful since most statements in Nano Script are expressions.

The left-hand sign must be an identifier.

You can name your variables anything... as long as it doesn't begin with a digit, space, any of the control symbols; or is a keyword.

You CAN, however, override builtin functions such as `abs` or `sin`.
Be careful.

All variable names support unicode.\
> Please don't abuse this.

```lua
名前 = "さくら"
🍕 = "pizza"
```

You may make a variable constant (can't be changed) by preceding its declaration with `const`.

```coffeescript
const x = 3
x = 3 #(!)ERROR: Reassignment of const Variable.
```

You can strictly type a variable by using a colon `:`.\
This isn't needed for const variables, they do this on their own.

```coffeescript
x : int = 3
y := 4 #You can allow NS to infer the type.
```

You can create all sorts of number literals (both **floats** and **integers**).

```c++
binary = 0b00101001
octal = 0o4156
hexadecimal = 0x00AAFF
floating = 1.253E+3
```

Numbers have some special assignment operators.

```coffeescript
x += 2
y *= 4
z /= 3
y ^= 5
```

(Assignment operators, like any other operators CAN be used inside expressions and will return the assigned value.)

**Booleans** exist, too.
You can use `on`, `yes` for `true`, and `off`, `no` for `false`.

```coffeescript
is_active = yes
is_open = no
lamp = on
car = off
boolval := true
```

There are some fancy assignments for each logical operator.

```coffeescript
is_active &= some_boolean #Shorthand for is_active = is_active && some_boolean
is_open |= other_boolean
```

**Strings** can be created using `"`s or `'`s.

```coffeescript
string1 = "This is a string."
string2 = 'This is also a string'
string3 = "Strings can be multiline. These usually
  join lines with a single space, ignoring the
  identation completely.
"
string4 = "Earlier I created a string that said '#{string1}' and now I just referenced it using interpolation!!!"

stringm = """This is a multiline string, which
    supports identation.
"""
```

Strings support some special escaped characters like `\n`, `\t`.
You can escape the backslash with another backslash `\\`.

**Lists** can be created using square brackets and support the use of ellipsis as ranges.
They are mutable, ordered and indexable, and accept duplicates.
They are like other languages' arrays.

```coffeescript
list1 = [0, 1, 2, 3, 4, 5]
list2 = [
  1, 2, 3
  4, 5, 6 #The line break is treated like a comma here.
  7, 8, 9
]
range1 = list1[1..5] #Equivalent to [1, 2, 3, 4, 5]
range2 = list1[1...5] #Equivalent to [1, 2, 3, 4]
range3 = list1[3..] #A copy from index 3 and so on.
range4 = list1[..3] #A copy from the start of the array until index 3
copy = list1[..] #A duplicate of list1
```

Lists can also be indexed with ranges, returning a sublist.

```coffeescript
list1 = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
midlist = list1[3..6]
print midlist #Will print '[3, 4, 5, 6]'
```

You can also use ranges to set elements of a list.

```coffeescript
list1[3..6] = [9, 8, 7, 6]
```

You can index a list with a negative index to index backwards from the last member.

```coffeescript
list1[-1] # Returns '10'.
```

> You can assign by destructuring.
> You can do it with any depth of lists.

```coffeescript
name = ''
age = 0
[name, age] = ['Bob', 34]

[first, middle..., last] = "a,silly,butterfly,jumped,in,me".split(",") #You can use splats to retrieve an unspecified amount of items.
[first, ..., last] = some_array #Or you can use pure '...'s to completely ignore the middle.
```

**Vectors** can be created using `v[]`.
They are ordered, indexable, but imutable (they can't be changed after created).

```coffeescript
vector = v[3, 4]
othervec = v[4, 6, -3]
```

Vectors can be indexed by position but also using 'x, y, z, w' or 'r, g, b, a'.\
Swizzling is allowed.

```coffeescript
vector = v[3, 4]
x = vector.x
inverted = vector.yx
```

**Objects** can be created using curly braces and comma-separated values or identation and keys followed by colons `:`.

```coffeescript
object = name: Bob, age: 37
object2 = 
  name: Michelle
  age: 12
```

You can index an object using `.` for string keys, or using `[]` for any other kind key (numeric or vector keys, for example).

> You can also assign by destructuring using objects (with any depth).

---

***Functions*** are, yes, first-class values and can be created with `fn`, an optional comma-separated list of parameters and `->`.
> You can also use `λ` instead of `fn` if you're feeling frisky.

```coffeescript
add = fn a, b -> a + b
sub = λ a, b -> a - b
```

Note that there's no `return` statement. That's because functions return the value of the last statement in its body.

> You can (and should) still use `return` anyways if you want to return early.

You may call a function that has no parameters by using the () operator.
```coffeescript
otherfunc = fn x=3, y=5 -> #Note the default values for the parameters!
  do_something(x)
  do_something_else(y)
```

Calling a function with parameters, no parenthesis are needed.\
You can, however, use parenthesis externally -- in the way they're used for anything else -- for disambiguation.

```coffeescript
y = add 3, 4
y = add 3, add 4, 5 #BAD!!! What does this even do?
y = (add 3, (add 4, 5)) #Clear syntax.
```

You can pass parameters by position (like we just did) or by name for better readability.

```coffeescript
cook = fn recipe, amount -> [...]

cook amount:3, recipe:2
```

> Note that the sytax for passing parameters is similar to the object construction syntax.
> For disambiguation you should wrap objects with brackets, which can be unintuitive if you're used to brackets for function calls.
> 
> ```coffeescript
> get_model fn car -> car.model
> 
> print get_model model:Volkswagen, color:'blue', wheels: 4
> # This would call a function with 3 named parameters, and
> # would result in an error.
> 
> print get_model (model: Volkswagen, color:'blue', wheels:4)
> # This would call a function with 1 parameter, which is an object.
> # This will compile work!
> 
> print create_car_gradient (model: Ford, color:'yellow'), 'blue', 3
> # This is a function that takes three parameters:
> # An object, a string, and an integer.
> ```
>


In NS, you can create operator-like functions by using `*` as a placeholder

```coffeescript
add = fn x, *, y -> x + y

print 3 add 4
```

You can create variable parameter functions (will collect all parameters into a list) with the an ellipsis.

```coffeescript
∑ = fn a, others... ->
  result = a
  for x in others: result += x
  result
```

Inside a function, the word `self` will refer to the object/class where this function is being called from, similar to JavaScript `this`.

```coffeescript
fn x -> 
  self.y = 0
```

Functions that, when created, refer to variables inside a local scope copy their values as internal constants, like lambda functions.

```coffeescript
fn -> 
  x = 3
  f = fn -> print x
  x = 4
  f() #Will print 3
```

This can be used to create functions on the fly like this:

```coffeescript
make_adder = fn x ->
  fn y ->
    x + y

add5 = make_adder 5
add10 = make_adder 10

add5 10 #Returns 15
add10 20 #Returns 30
```

Paralellizable functions that run on the *GPU* can be created by preceding the `fn` keyword with `kernel` and some optional *out* parameters.

```coffeescript
# A simple shader that fills the output image with a UV,
# allowing you to pass in the value of the blue channel.
shader = kernel outcolor:vec3 fn blue -> 
  uv : vec2 = KR_GROUP_INDEX / KR_GROUP_SIZE
  outcolor = v[uv.r, uv.g, blue]
```

You can then call this kernel function like normal functions, but specifying the dimensions of the work blocks and work threads in angle brackets.

```coffeescript
result = await shader<128, 128> 1.0
# Results returns a buffer containing a struct with the out parameters.
# You can then pass use these results for whatever you want!!!

image = result.outcolor
```

More about how it works in [Parallelization](#Parallelization).

---

### Non-Variable Members

#### Signals

Signals are useful for connecting your code together.\
You can create signals and emit them from anywhere,
you can then connect signals to functions using `<signal>.connect(<function>)`
or `await <signal>` inside the function's body.

```js
on_task_completed = signal()
```

You can emit signals the same way you call functions.

```js
on_task_completed()
```

#### Enumerations

Enumerations are syntactic sugar for integer values with specific meanings, so you don't have to remember which integer values correspond to what.

```coffeescript
enum NATURAL, INTEGER, RATIONAL, REAL, COMPLEX

# In this case, NATURAL equals 0, INTEGER equals 1 and so on... but you CAN set the values manually, like an object. When doing this, they can have non-integer values.

enum WindowMode # You can name your enumerations, good for organization.
  WINDOWED, MINIMIZED, MAXIMISED, FULLSCREEN
```

An alias for enums is `flags`, which enumerates your items acoording to increasing powers of two.

```coffeescript
flags Divisibility
  IS_EVEN
  IS_THREEVEN
  IS_TETREVEN

# IS_EVEN = 1, IS_THREEVEN = 2, IS_TETREVEN = 4
```
This is useful for usage with bitwise operators.

#### Classes

Classes are useful for aggregating data and behaviour, if you're into OOP.
They look exactly like objects, but they can inherit other classes (multiple even).

```coffeescript
class Car extends Vehicle
  wheel_count: 3
  drive: fn ->
    "vroom_vroom"
```

You can define a static member function by prefixing the function with a '$'.

```coffeescript
Car::$create_from_model = fn model -> get_car_from_model model

Car.create_from_model 'Volkswagen 20'
```

Classes behave as new types you can typeset/typecheck variables with.

```coffeescript
c is Car
x : Car = Car.create_from_model 'Ford 30'
```

You can overload operators in classes by using names like `operator+`

```coffeescript
VectorPrototype.operator+ = fn right_hand -> v[self.x + right_hand.x, self.y + right_hand.y]
```

---

### Builtin Operators

#### Arithmetic Operators for Numbers!!!
```coffeescript
x + 3 #Addition
6 - 4 #Subtraction
9 * 9 #Product
y / 7 #Division
y // 7 #Integer division
3 ^ 3 #Power

-9 #Negation
++x #Pre-Increment
--y #Pre-Decrement
x++ #Post-Increment
y-- #Post-Decrement

6 % 4 #Remainder of the division
7 mod 2 #A value that's always inside the interval [0; 2[
```

These operations can also be done on vectors if the vector's components are numbers, and the vectors are the same size.
In such case, they're applied component-wise.
```coffeescript
v[3, 4] + v[5, 6] #Returns v[7, 10]
```

It also works between a vector and a single number,
the number will be treated as a vector of equal dimension to the vector.

#### Comparisons
```coffeescript
y == 2 #Value Equality
y != 3 #Value Inequality
y is int #Type Checking
y isnt string #Negative Type Checking

4 > 3
5 < 6
4 >= 4
4 <= 10
2 < x < y # You can use chained comparisons to test intervals. Same as `2 < x and x < y`
```

#### Existence
```coffeescript
y? #Checks whether y was declared and doesn't equal null
```
#### Collections
```coffeescript
# You can use `in` to check for the presence of a value in a collection.
3 in [1, 2, 3] #Checks whether 3 exists in the list [1, 2, 3]
[1, 2] in [1, 2, 3] #Will check if the previous list is a sublist of the second list.

# You can use math operators for shorthands
3 ∈ [1, 2, 3] #You may use ∈ instead of 'in' for item presence
[1, 2] ⊆ [1, 2, 3] #You may use ⊆ instead of 'in' for subset presence

# This notation is useful to test whether a list is
# an item (not a sublist) of another list.
[1, 2] ∈ [[1, 0], [1, 2], [3, 4]]

# It does not work with objects, use the existencial operator instead.
my_cousin = 
  name: Alice
  age: 20
  father: 'Mark'
  
if my_cousin.father?
  print my_cousin.father
  
# You can treat strings as lists of characters.
"c" in "Cascadia Code" #You can check character presence.
"Banana" in "I love Bananas" #You can check for substring presence.
```

#### Logical
```coffeescript
a && b
a || b
a and b
a or b
!a
not a
```

#### Bitwise
```coffeescript
a & b #Bitwise AND
a | b #Bitwise OR
a band b
a bor b
~a #Bitwise NOT
bnot a

a << 2
a >> 3 #Bit Shifting
```

#### String Concatenation

You can use + to concatenate strings with strings.
You can also use `..` to concatenate strings with other objects. NS will try to convert them intro strings.

```coffeescript
"Hello " + "World"
"My niece is " .. 3 .. " years old."
```

#### Members

You can use . to access the value of a member of an object or an instance.
You can use :: to reference of a member of an object or an instance.

```coffeescript
bob = 
  age: 3
  action: fn -> 'I did an action!!!'

x = bob.age
bob::action = fn -> 'I changed that action to another action!!!'
```

---

### Keywords / Flow Control

You have your basic flow control keywords (with some aliases, for flavour).

**Conditionals**

You have 'if', 'elif', 'else'.
'unless' is an alias for 'if not'.

Conditionals are expressions and can be passed as the right-hand values of an assignment!!!

```coffeescript
if has_food and food_level < 3
  eat()
elif liquid_level < 3
  drink()
else
  food_level -= delta

y = unless x < 3: 4 # Using a colon to separate condition from value when there's no identation.
```

If the body of code you want to express conditionally is a single expression, you can place it *before* the `if`/`unless` keyword.

```coffeescript
y = 4 unless x < 3
```

**While/Until** loops until a condition is met.

```coffeescript
while x > 4
  x++
  y -= 4 until y < 3 # Until is a shorthand for 'while not', for readability.
```

These can be written as an expression, in which case they'll return a list of each iteration of the loop.

```coffeescript
y = 0
x = while y < 3
  y++
  'Y was decremented'

# x will be "['Y was decremented', 'Y was decremented', 'Y was decremented']"
```

`loop` may be used instead of `while true`, for readability.

```coffeescript
loop
  infinite_loop
```

**For** loops basically work for iterating over collections.

You can iterate over items of a list, a vector or a set.
They, too, are expressions, that can be used inline.

```coffeescript
squares_of_primes = []
for i in [0...4]
  squares_of_primes.append x ^ 2 for x in [1, 2, 3, 5, 7, 11, 13]

double_vector = n * 2 for n in v[3, 4, -10]
```

You can iterate over index-item pairs of lists or of a vector.

```coffeescript
for index, item in list
  print 'Item #{item} at position {index}.'
```

You can iterate over strings (they get treated as a list of characters).

```coffeescript
for index, char in 'I love my mother.'
    print 'Char at position #{index} is "#{char}".\n'
```

You can use of to iterate through key/value pairs of an object.
```coffeescript
hunger_points =
  brocolli: 20
  chocolate: 30
  rice: 10
for veggie, points of hunger_points
  print 'Turns out #{veggie} heals #{points} hunger points.'
```

> You can use `∀` as an alias for `for`.
> ```coffeescript
> squares_of_primes = num ^ 2 ∀ num ∈ [0..10]
> ```

If you don't care about the current iterated item's name you might as well omit it.

```coffeescript
for [0...8]
  print 'Repeating Task!'
```

You can use 'when' while iterating to selectively choose whether to count the current iterated object.

```coffeescript
print x for x in [0...100] when is_prime x
###
Another case where you should probably use parenthesis.
###
print (x for x in [0...100] when (is_prime x))
```

Similarly to while/until, for loops will return a *collection* of the iterated values if used in an assignment expression.

The type of the collection will be the same as the type of the iterated collection.

```coffeescript
primes = (num for num ∈ [0...100] when (is_prime n)) # Will return a list.

double_vector = n * 2 for n in v[3, 4, -10] # Will return a vector.

non_vowels = 'p'...char for char in 'My balloon popped.' when char in 'bcdfghjklmnpqrstvwxyz BCDFGHJKLMNPQRSTVWXYZ.,'
# Will return a string, specifically 'My blln pppd.'
```

`continue` will skip the current iteration of a loop.\
`break` will exit the current loop.

---
**Match** statements are like `switch`es in other languages.\
They can take multiple values in each clause.\
They can also omit the control parameter enirely and behave like a more readable elif chain.

```coffeescript
car_model = match year
  when 1920: "Ford 20"
  when 1930, 1830: "Volkswagen 30"
  else "Unknown"

val = match
  when x > 3: 75
  when x < 3: 20
  when y > 6: 98
  else 4
```

**Try-Catches** can also be passed to an assignment and omit the finally and catch clauses.

```coffeescript
name = try generate_name()
age = try
    generate_name()
  catch error
    print "(!) An error occurred while generating the name: ${error}"
  finally
    cleanup_generator()
```

**With** can be used to easily call functions/access variables inside an object.

```coffeescript
sam =
  name: 'Samantha'
  age: 2
  height: 4

with sam
  print height
  print 'I, #{name} am #{age} years old.'
```

**Await** can be used to wait for the execution of functions OR for the calling of signals.
It stops the current thread until the awaited object resolves.

```coffeescript
await on_clicked
await someFunctionCall()
```

---

### Built-ins

There's a plethora of constant values and functions you can use.

Here are some examples:

You got your buddies PI, TAU, E, and the functions sin, cos, tan, asin, acos, atan, atan2, sinh, cosh, tanh.

For numbers you have abs, floor, ceil, round, mod, exp, log, pow, and all the usuals you'd expect.

For strings you have split, concat, etc.

For lists, a remarkable one is `apply` that allows you to apply a function to each element of a list.

For vectors, you have functions like `len` that returns the vector's length,
and `distance` that returns the distance between the two vector's tips.

There are some miscelaneous functions too, like 'print', 'printerr' and 'printd' for outputing to the console, and many more.

---

### Statements

Statements are separeted using line breaks, but multiple statements can be typed in one line if separated by a semicolon `;`.

```coffeescript
x += 3
y = 3; z = 4
```

You can also join two lines into a single statement, by ending the line with a backslash `\`.

```coffeescript
x = 3 + 4 + 5 \
  + 6 + 7 + 8 \
  + 9 + 10 + 11
```

---

### Parallelization
<a id="pookie"></a>

Remeber this example in the `function` section?
```coffeescript
# A simple shader that fills the output image with a UV,
# allowing you to pass in the value of the blue channel.
shader = kernel outcolor:vec3 fn blue -> 
  uv : vec2 = KR_GROUP_INDEX / KR_GROUP_SIZE
  outcolor = v[uv.r, uv.g, blue]
```

This corresponds to the following CUDA C++

```c++
auto shader = __global__ [=]( int blue, int* outcolor )
{
  vec2 uv = vec2(blockIdx.x, blockIdx.y);
  *outcolor = vec3(uv.x, uv.y, blue);
}
```

With this, you can do things blazing fast:

```coffeescript
# This function calculates the roots of the even numbers up to 200
sqrt = kernel n:int fn -> n = sqrt(KR_INDEX * 2)
run = await sqrt<100, 1>()
roots = run.n
```

The way the declaration works is:

The parameters passed after the kernel keyword are used to create buffers on the GPU (It is a good practice to type your parameters so the buffer will be created with the right size).

On the previous example, a single out buffer of `sizeof(int) * 100 -> 400` bytes will be created on DEVICE.

After everything is completed, the buffers created by the `out` parameters will by copied back to HOST where they'll be accessible together as a NanoScript object containing each buffer as a member.\
Back on the CPU, the buffers will be typed lists of whatever types you specified.

`out` parameters that weren't verbosely typed will be treated as integers.

Calling the parallel function is simple enough:

```coffeescript
function_name<num_blocks, num_threads>(parameters)

# num_blocks and num_threads may be vectors of [1..3] dimensions.
```

This is a very quick way to run a single function in parallel, if that's all you need, but you might want to do more complicated operations on the GPU.\
For that purpose, you can create a special closure that'll temporarily allocate memory on the GPU.

```coffeescript
x := [0..99]
z := buffer 100 * sizeof int
sqrt = kernel n:int fn -> n = sqrt KR_INDEX * 2

DEVICE in x, out y
  z = sqrt<len(x), 1> x
```
`in` variables are copied from HOST into DEVICE when the closure starts,\
`out` variables are copied from DEVICE into HOST when the closure ends,\
`inout` variables are copied both times.

Each kernel call is asynchronous, so if the next operation requires results from the previous ones, use `await` to force resynchronization end.

```coffeescript
DEVICE in x, out y
  await z = sqrt<len(x), 1> x
  await z = sqrt<len(x)/2, 1> x
# Taking the square root twice on half of 
```

This block syntax allows multiple GPU functions to be executed over the same data without having to come back to the CPU. This is useful if you need to change the work blocks/threads dimensions or chain multiple different kernel functions.


[...]

---
# Implementation

So, how would you go about implementing it?\
Let's start with an example, actually.

### FizzBuzz Game

This is a common interview question for junior coders, the FizzBuzz game.
An algorithm that prints integers... except if they are divisible by 3, 5 or both, in which cases they print 'Fizz!', 'Buzz!' or 'FizzBuzz!'; respectively.

Here is it in all its glory:

```coffeescript
fizzbuzz = fn ->
  for i in [0..20]
    print_number = yes
    if i mod 3 == 0
        print_number = no; print 'Fizz'
    if i mod 5 == 0
        print_number = no; print 'Buzz'
    print i if print_number else '!'
    print '\n'
```

The Lexer should transform this beautiful source code into this:

```
[identifier: fizzbuzz]
[op: assignmnent]
[function_declaration_begin]
[function_parameters_end]
[newline] [indent]
[keyword: for]
[identifier: i]
[keyword: in]
[open_square_brackets]
[integer_literal: 0]
[op: inclusive_range]
[integer_literal: 20]
[close_square_brackets]
[newline] [indent] [indent]
[identifier: print_number]
[op: assignment]
[boolean_literal: true]
[newline] [indent] [indent]
[kw: if]
[identifier: i]
[identifier: mod]
[integer_literal: 3]
[op: equality]
[integer_literal: 0]
[newline] [indent] [indent] [indent]
[identifier: print_number]
[op: assignment]
[boolean_literal: false]
[statement_break]
[identifier: print]
[string_literal: 'Fizz']
[newline] [indent] [indent]
[kw: if]
[identifier: i]
[identifier: mod]
[integer_literal: 5]
[op: equality]
[integer_literal: 0]
[newline] [indent] [indent] [indent]
[identifier: print_number]
[op: assignment]
[boolean_literal: false]
[statement_break]
[identifier: print]
[string_literal: 'Buzz']
[newline] [indent]
[identifier: print]
[identifier: i]
[kw: if]
[identifier: print_number]
[kw: else]
[string_literal: '!']
[newline] [indent]
[identifier: print]
[string_literal: '\n']
```

The Parser should take this mess of tokens and transform into an AST:

```coffeescript
[op: assignment]
├── 0: [identifier: fizzbuzz]
└── 1:
    └── [function_declaration]
        ├── parameters:
        └── body:
            └── [for_loop]
                ├── current_item_identifier: 'i'
                ├── collection:
                │   └── [op: inclusive_range]
                │       ├── 0: [integer_literal: 0]
                │       └── 1: [integer_literal: 20]
                └── body:
                    ├── [op: assignment]
                    │   ├── 0: [identifier: print_number]
                    │   └── 1: [boolean_literal: yes]
                    ├── [if]
                    │   ├── condition:
                    │   │   └── [op: equality]
                    │   │       ├── 0:
                    │   │       │   └── [func: mod]
                    │   │       │       ├── 0: [identifier: i]
                    │   │       │       └── 1: [integer_literal: 3]
                    │   │       └── 1: [integer_literal: 0]
                    │   └── body:
                    │       ├── [op: assignment]
                    │       │   ├── 0: [identifier: print_number]
                    │       │   └── 1: [boolean_literal: no]
                    │       └── [func: print]
                    │           └── 0: [string_literal: 'Fizz']
                    └── [if]
                        ├── condition:
                        │   └── [op: equality]
                        │       ├── 0:
                        │       │   └── [func: mod]
                        │       │       ├── 0: [identifier: i]
                        │       │       └── 1: [integer_literal: 5]
                        │       └── 1: [integer_literal: 0]
                        ├── body:
                        │   ├── [op: assignment]
                        │   │   ├── 0: [identifier: print_number]
                        │   │   └── 1: [boolean_literal: no]
                        │   └── [func: print]
                        │       └── 0: [string_literal: 'Buzz']
                        ├── [func: print]
                        │   └── 0:
                        │       └── [if]
                        │           ├── condition: [identifier: print_number]
                        │           ├── body: [identifier: i]
                        │           └── else: [string_literal: '!']
                        └── [func: print]
                            └── 0: [string_literal: '\n']
```

Which then can be used to interpret the code, or to transpile it onto a different language that can do the same functions.

Let's look at the code in *NanoScript* and *C++* side-by-side.

#### NanoScript
```coffeescript
fizzbuzz = fn ->
  for i in [0..20]
    print_number = yes
    if i mod 3 == 0
        print_number = no; print 'Fizz'
    if i mod 5 == 0
        print_number = no; print 'Buzz'
    print i if print_number else '!'
    print '\n'
```
#### C++ 17 + Libraries
```c++
#include <nano_script_core.h>

std::any fizzbuzz;
std::any print_number;

int main (int argc, char** argv)
{
    fizzbuzz = std::function<void()>
    (
        [](){
            for (int i = 0; i <= 20; i++)
            {
                print_number = true;
                if ( nscore::mod(i, 3) == 0 )
                {
                    print_number = false;
                    nscore::print(std::string("Fizz"));
                }
                if ( nscore::mod(i, 5) == 0 )
                {
                    print_number = false;
                    nscore::print(std::string("Buzz"));
                }
                std::any temp;
                if ( std::any_cast<bool>(print_number) )
                {
                    temp = i;
                }
                else
                {
                    temp = std::string("!");
                }
                nscore::print( temp );
                nscore::print(std::string("\n"));
            }
        }
    );
    
    std::any_cast<std::function<void()>>(fizzbuzz)();
}
```

For all the capabilities that NanoBoy has, a good implementation would probably be to convert it into CUDA C++20 with some libraries.

The transpilation should get rid of all the `std::any` and `std::any_cast` whenever it can if your variable was declared to be strictly-typed.

So,

```coffeescript
x = 3
```

becomes

```c++
#include <any>
std::any x = 3;
```

While

```coffeescript
x := 3
```

Becomes

```c++
int x = 3;
```

---

# Conclusion

A Nano Script compiler will likely never be implemented, but I believe it looks nice.\
It would probably succeed well at teaching newbies how to program in a way that's computer agnostic.
Could be good for learning programming games.

NS is also very similar to pseudocode, which means that having a NS interpreter on your pocket could be a
very quick way of testing out ideas.

---

Anyways, I'm out.

Regards,
Pedro Braga.