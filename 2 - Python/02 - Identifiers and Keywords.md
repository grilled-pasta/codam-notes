## Names
`NAME` tokens represent _identifiers,_ _keywords_ and _soft keywords_. They are composed of the following characters:
- uppercase and lowercase letters - case is significant
- the underscore
- digits - cannot appear as the first character
- non-ASCII characters - only _letter-like_ and _digit-like_
## Keywords
Reserved words that cannot be used as ordinary identifiers:
### Values and logical operators
`False` /  `True` - boolean values
`None` - absence of a value (check with `is None`)
`and` / `or` / `not` - logical and / or / negation
`is` - tests object identity (whether two references point to the same object):
```python
a = [1, 2]
b = [1, 2]

a == b # True: same contents
a is b # False: different object
```
`in` / `not in` - tests membership in a collection or substring membership in a string
### Conditional execution
`if` - executes a block when a condition is true
`elif` - checks another condition if previous conditions were false
`else` - executes when no preceding `if` or `elif` conditions were true (also used with loops and exception handling)
### Loops and iteration
`for` - iterates over elements of an iterable
`while` - repeats a block while a condition remains true
`break` - immediately terminates the innermost loop
`continue` - skips the rest of the current iteration and moves to the next one
`pass` - does nothing (used as a placeholder)
### Functions
`def` - defines a function
`return` - terminates a function and optionally sends a value back to its caller (without it a function returns `None`)
`lambda` - creates a small anonymous function consisting of a single expression
```python
double = lambda number : number * 2
print(double(5)) # 10
plants = ["rose", "tomato", "basil"]
plants.sort(key = lambda plant: len(plant))
```
`yield` - produces a value from a generator function (unlike `return`, it suspends the function and preserves its execution state); Generators produce values lazily rather than constructing an entire collection at once.
```python
def count():
	yield 1
	yield 2
	yield 3
for number in count():
	print(number) # 1 2 3
```
`global` - declares that a variable inside a function refers to a module-level variable
```python
counter = 0
def increment():
	global counter
	counter += 1
increment()
print(counter) # 1
```
`nonlocal` - declares that a variable belongs to an enclosing function scope, rather than the current function or global scope (useful for closures and nested functions)
```python
def outer():
	counter = 0
	def inner():
		nonlocal counter
		counter += 1
	inner()
	print(counter) # 1
outer()
```
### Exception handling
`try` - defines a block of code that might raise an exception
`except` - handles an exception raised inside a `try` block
`finally` - executes regardless of whether an exception occurred
`raise` - explicitly raises an exception
`assert` - checks whether a condition is true (raises `AssertionError`, for debugging and internal assumptions)
### Classes and OOP
`class` - defines a class
`del` - deletes a reference, an object attribute, or a collection element (deleting a variable name does not necessarily destroy the underlying object; other references may still ext)
### Imports and Namespaces
`import` - imports a module
`from` - imports specific names from a module
`as` - creates an alias (used with imports, exception handling, and the resources provided by a context manager)
### Context Management
`with` - executes a block inside a context manager (it handles setup and cleanup automatically)
```python
with open("plants.txt", "r") as file:
	content = file.read()
```
### Asynchronous Programming
`async` - declares an asynchronous function or asynchronous iterator
`await` - suspends an sync function until an awaitable operation completes (used inside `async`)
## Soft Keywords
> Added in version 3.10.
> Behave like keywords only in specific grammatical contexts

`match` / `case`  - structural pattern matching
`_` as wildcard - inside a `match`, it matches any value without binding it to a variable
`type` - type alias statement
```python
type PlantName = str
type PlantList = list[PlantName]
```
## Reserved Classes of Identifiers
### Names beginning with a single underscore
`_*` - these names are excluded from wildcard imports (the underscore does not make a variable truly private - you can still import it explicitly)
```python 
# garden.py
plant = "Tomato"
_water_level = 50

# main.py
from garden import *
print(plant)        # Works
print(_water_level) # Name Error

# main2.py
from garden import _water_level
print(_water_level)  # 50
```
### Underscore
Several different meanings:
- Wildcard in pattern matching
- Last expression in the interactive interpreter
- Unused variables
- Internationalization
```python
match "tomato":
	case "rose":
		print("Flower")
	case _:
		print("Unknown Plant")
		
>>> 10 + 5
15
>>> _
15
>>> _ * 2
30

for _ in range(3):
	print("Watering...")
plant, _ = ("tomato", 100)

import gettext
_ = gettext.gettext
message = _("Hello, world!")
```
### Dunder Names
> System-defined names

| Name       | Purpose                                              |
| ---------- | ---------------------------------------------------- |
| `__init__` | Initializes an instance                              |
| `__str__`  | Defines the human-readable string representation     |
| `__repr__` | Defines the developer-oriented representation        |
| `__len__`  | Defines the behavior for `len()`                     |
| `__add__`  | Defines the behavior for `+`                         |
| `__eq__`   | Defines equality comparison                          |
| `__name__` | Module or class name attributes                      |
| `__main__` | Name assigned to the top-level execution environment |
> When executing a Python file directly, its `__name__` is normally \_\_main\_\_
#### Class-private Names
`__*` - names beginning with at least two underscores and ending with at most one underscore are subject to _name mangling_ when used inside a class definition
```python
class Plant:
	def __init__(self):
		self.__water_level = 100
tomato = Plant()
print(tomato.__water_level)  # AttributeError
```
> Python internally transforms `__water_level` into `_Plant__water_level`
