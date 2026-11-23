## Object, Values and Types
Every Python object has three properties:
- **Identity** - Unique identifier `id()`; compared using `is`
- **Type** - Determines supported operations `type()`
- **Value** - The data represented by the object
### Mutability
- **Mutable** - can change after creation `list` / `dict` / `set`
- **Immutable** - cannot change after creation `int` / `str` / `tuple` / `float`
> An immutable container can still reference mutable objects

```python
data = ([1, 2], 3)
data[0].append(4)  # Valid
```

## Main Built-in Types
Python groups objects into types based on their behavior

| Category          | Types                                | Key property                  |
| ----------------- | ------------------------------------ | ----------------------------- |
| None              | `NoneType`                           | Represents absence of a value |
| Numbers           | `int` / `float` / `complex` / `bool` | Immutable                     |
| Sequences         | `str` / `tuple` / `bytes`            | Ordered                       |
| Mutable Sequences | `list` / `bytearray`                 | Ordered and mutable           |
| Sets              | `set` / `frozenset`                  | Unique elements, unordered    |
| Mappings          | `dict`                               | Key-value pairs               |
Important Distinctions:
- `bool` is a subtype of `int` (True == 1, False == 0)
- Sequences are ordered collections indexed by integers
- Sets contain unique, immutable elements
- Dictionaries preserve insertion order and require hashable keys
- `NotImplemented` signals that an operation is unsupported for given operands
- `Ellipsis` is the singleton represented by `...`
## Callable Types
### Functions are Objects
```python
def greet(name):
	return f"Hello, {name}"
print(type(greet))  # <class 'function'>

say_hello = greet   # assign a function to a variable
say_hello("Alla")   # Hello, Alla

def execute(func, value): # pass a function as an argument  
	return func(value)
execute(greet, "Alla")    # Hello, Alla
```
### Instance Methods are Functions Associated with a Class
```python
class Plant:
	def water(self):
		print("Watering plant")
plant = Plant()
plant.water()  # Python automatically passes the instance as the first argument
```
This is a **bound method**. It stores:
- `__self__` - the instance it belongs to
- `__func__` - the original function
### Generator Functions
Produce values on demand instead of storing all results in memory. It returns a generator object. When there are no more values, `StopIteration` is raised.
```python
def read_numbers():
    for number in range(1_000_000):
        yield number
numbers = read_numbers()
print(next(numbers))
for number in numbers:
	print(number)
```
## Modules
A basic organizational unit of Python code, usually a `.py` file
### Module Objects
- Modules are Python objects (`type(module) == module`)
- Every module has its own namespace
- A module's namespace is implemented as a dictionary (`module.__dict__`)
- The namespace contains variables, functions, classes and imported names
### Attribute Access
Module attribute access is dictionary lookup:
```python
module.x
# Equivalent to:
module.__dict__["x"]

module.x = 10
# Equivalent to:
module.__dict__["x"] = 10
```
### `__name__` == "\_\_main\_\_"
Used to distinguish direct execution from importing
## Custom Classes
Classes are objects with their own namespace, implemented as a dictionary.
### Attribute Lookup
- Instance attributes
- Class attributes
- Parent classes according to MRO
- `AttributeError` if not found
### Method Binding
- Instance methods automatically receive the instance as `self`
- Class methods automatically receive the class as `cls`
- Static methods receive no automatic first argument
### Basic Customization
- `__new__(cls, ...)` - called before `__init__()`
- `__init__(self, ...)` - commonly assigns instance attributes
- `__del__(self)` - called when an object is being finalized
### Descriptors
Customizes attribute access through the descriptor protocol
##### Data Descriptor
- Implements `__set__()` or `__delete()__()`
- Takes precedence over instance attributes
##### Non-data Descriptors
- Implements `__get__()` only
- Can be overridden by instance attributes
