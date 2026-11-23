## Structure of a Program
### Code Block
Unit of Python code that executes as a single unit.

| Type                | Example                              |
| ------------------- | ------------------------------------ |
| Module              | A `.py` file                         |
| Function Body       | Code inside `def` (doesn't execute)  |
| Class Definition    | Code inside `class` (executes)       |
| Interactive Command | A command entered in the Python REPL |
| Script              | `python script.py`                   |
| Command String      | `python -c "print('Hello')"`         |
| Module Execution    | `python -m module_name`              |
| Dynamic Execution   | `eval()` and `exec()`                |
### Execution Frames
The context in which a code block executes. It contains information about the current execution, including:
- Local Variables
- References to Global and Built-in Namespaces
- Current Execution Position
- Information Needed to Resume Execution After a Function Call
- Debugging Information

```python
>>> import inspect
>>> def first():
...     second()
...     
>>> def second():
...     frame = inspect.currentframe()
...     print("Function:", frame.f_code.co_name)
...     print("Local Variables:", frame.f_locals)
...     print("Caller:", frame.f_back.f_code.co_name)
...     
>>> first()
Function: second
Local Variables: {'frame': <frame at 0x7f67f5db2400, file '<python-input-2>', line 4, code second>}
Caller: first
>>> 
```

## Naming and Binding
How Python associates names with objects and determines which object a name refers to when it is used.
- __Binding__ - establishing a relationship between names and an object
- __Scope__ - defining where a name is visible
- __Name Resolution__ - determining which binding a name refers to
### Name Binding
A name is a reference to an object. A binding operation establishes that relationship.

| Operation             | Example                                         |
| --------------------- | ----------------------------------------------- |
| Assignment            | `x = 10`                                        |
| Function Definition   | `def greet(): ...`                              |
| Class Definition      | `class Person: ...`                             |
| Function Parameters   | `def greet(name): ...`                          |
| Import                | `import math` (except names beginning with `_`) |
| Assignment Expression | `(n := 5)`                                      |
| For Loop Target       | `for item in items:`                            |
| With Statement        | `with open("file") as f:`                       |
| Exception Target      | `except Exception as error:`                    |
| Pattern Matching      | `case {"name": name}:`                          |
| Type Statements       | `type Number = int`                             |
| Delete Statements     | `del x` (unbind)                                |
### Name Resolution
A name's classification depends on where it is bound.
- __Local__ - Bound in the current function block
- __Global__ - Bound at a module level
- __Free__ - Used in a block but not bound there, and resolved through an enclosing scope
```python
x = 10  # Global

def calculate():
	y = 20  # Local
	
	def multiply():
		return x * y  # y is Free inside multiply()

	return multiply()
```
### Scope
Determines where a name is visible. Python resolves names by searching the nearest applicable enclosing scope.
```python
x = "global"

def outer():
    x = "outer"

    def inner():
        x = "inner"
        print(x)

    inner()

outer()  # "inner"
```

> `NameError` - is raised if the given name can't be found in the applicable namespace.
#### UnboundLocalError
The name is local to a function, but it is accessed before being assigned a value.
```python
x = 10

def calculate():
    print(x)
    x = 20

calculate()  # UnboundLocalError
```

> Python scans the entire function block for binding operations. Since `x = 20` exists inside a function, Python treats `x` as local throughout that function. The earlier `print(x)` therefore attempts to access an uninitialized local variable rather than the global `x`.
#### `global` Keyword
Tells Python that a name refers to the module-level namespace.
```python
counter = 0

def increment():
    global counter
    def inner():
	    print(counter)  # treats counter as global when reading
	    counter += 1  # UnboundLocalError when rebinding
    counter += 1
    inner()

increment()  # 1
```

> Without `global`, assigning to `counter` inside the function would make it local.
#### `nonlocal` Keyword
Refers to a previously bound variable in the nearest enclosing function scope. Does not refer to the module namespace.
```python
def outer():
    counter = 1

    def inner():
        nonlocal counter
        counter += 1

    inner()
    return counter

print(outer())  # 2
```

> `SyntaxError` is raised at compile time if the given name does not exist in any enclosing function scope. 
#### Class Scope
The class namespace becomes the class's attribute dictionary. Methods don't inherit the class body's scope. 
Unbound local variables are looked up in the global namespace.

```python
class Calculator:
    precision = 2

    def calculate(self, number):
        return round(number, self.precision)
```

> `precision` is not automatically accessible as a free variable inside the method
#### Annotation Scopes
Evaluate type-related expressions. They behave like function scopes, but can access their enclosing class namespace. Used for:
- Function Annotations
- Variable Annotations
- Generic Type Parameters
- Type Aliases
- Type Parameter Bounds and Constraints
```python
class User:
    name = str
    type Name = name
```

> Unbound local variables are looked up in the global namespace.
#### Lazy Evaluations
An expression is evaluated only when its value is needed, rather than immediately when it is defined. Used for Type Aliases, Type Parameters, and Annotations.
```python
type Alias = 1 / 0

print("Alias created")
print(Alias.__value__)

>>> Alias created
>>> ZeroDivisionError: division by zero
```

> Lazy Evaluation allows references to types that might not yet have been evaluated or defined.
#### Builtins Namespace
Python has a builtins namespace containing commonly available objects. You don't need to import `print` / `len` / `type` / `sum`. Python searches the global namespace first, then the builtins namespace.

> Avoid modifying `__builtins__` directly. Use the `builtins` module when you need to interact with builtins.
#### Dynamic Name Resolution
Python resolves free variables at runtime, not necessarily when the function is defined.

```python
x = 10

def show():
	print(x)

x = 42
show()  # 42
```

> The function resolves `x` when it executes. It finds the current binding in the global namespace.
## Exceptions
An exception is a mechanism for interrupting the normal flow of execution to handle errors or exceptional conditions. It is _raised_ at the point where the error is detected. It may be _handled_ by any code block that directly or indirectly invoked the code block where the error occurred.

```python
10 / 0  # ZeroDivisionError - automatic exception

raise ValueError("Invalid Value")  # explicit exception
```
### Handling Exceptions
- `try` - Defines the code that might raise an exception
- `except` - Defines a handler for a matching exception
- Execution of the `try` block stops when an exception is raised
- If a matching handler exists, it executes and normal execution continues after the `try` statement
- If no exception occurs, the `except` block is skipped

```python
try:
    number = int(input("Number: "))
    result = 100 / number
except ValueError as e:
    print("Invalid number: ", e)
except ZeroDivisionError:
    print("Cannot divide by zero")
```

> Exceptions are identified by class instances. The handler can receive the instance using `as`. 
### Unhandled Exceptions
- Python terminates program execution or returns to the interactive main loop
- A stack traceback is printed, unless it was `SystemExit` exception
> A traceback shows the chain of function calls leading to the exception
### `finally` Clause
- `finally` defines cleanup code
- Executes whether an exception occurred or not in the preceding `try` block
- Commonly used for closing files, releasing resources, and cleanup operations
## Runtime Components
Python executes within a host machine and its runtime environment.

```text
Host machine
└── Process
    └── Thread(s)
```

- __Host Machine__ - the physical or virtual computer on which the program runs
- __Process__ - an instance of a running program
	- Represents the program's execution context
	- Owns resources assigned by the OS
	- Resources include memory, signals, file handles, sockets, and environment variables
	- Processes are isolated and independent
- __Thread__ - an execution path inside a process
	- Executes machine code
	- Uses the resources assigned to its process
	- Threads in the same process share process resources
	- A Python program starts with exactly one thread in the conceptual model
	- Additional threads can be created if supported by the host and implementation
### Python Runtime Model
Python adds three runtime-specific layers:

```text
Host machine
└── Process
    └── Python global runtime
        └── Python interpreter(s)
            └── Python thread state(s)
                └── Host thread(s)
```

- __Global Runtime__
	- Conceptually, a collection of interpreters
	- Manages resources that might be shared between interpreters
	- Resource management is implementation-specific
- __Interpreter__
	- Encapsulates non-process-global, non-thread-specific runtime state
	- State persists between uses
	- Contains interpreter-specific data, such as `sys.modules`
	- Multiple threads using the same interpreter share its state safely through the runtime
	- __Multiple interpreters__ - isolated and independent, own `sys.modules`
- __Thread State__
	- Contains thread-specific runtime information, including:
		- Currently raised exception
		- Python call stack
		- Other thread-specific resources
	- Properties:
		- Each thread state belongs to exactly one interpreter
		- Each thread state belongs to exactly one host thread
		- It can only be used by that host thread with that interpreter
		- Multiple thread states may be associated with one host thread, but only one can be active at a time
		- Thread states do not share thread-specific data, though they may share an interpreter and its objects
### Creating Additional Execution Contexts

| Component    | Module                                  | Purpose                              |
| ------------ | --------------------------------------- | ------------------------------------ |
| Threads      | `threading`                             | Create and manage threads            |
| Processes    | `os` / `subprocess` / `multiprocessing` | Create and manage processes          |
| Interpreters | `concurrent.interpreters`               | Create and use multiple interpreters |
| Coroutines   | `asyncio`                               | Run asynchronous tasks               |
> `asyncio` does not create a new OS thread for every coroutine - typically single thread per interpreter
