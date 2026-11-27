Python code in one module gains access to the code in another module by the process of importing it:
- `import` statement
	- searches for the named module (call to `__import__()`)
	- binds the results of that search to a name in the local scope
- `importlib.import_module(...)`
- `__import__(...)` 

>When the module is found, Python creates a _module object_. Otherwise, a `ModuleNotFoundError` is raised.
## `importlib`
A rich API for interacting with the import system. `importlib.import_module()` provides a recommended, simpler API than built-in `__import__()` for invoking the import machinery.
## Packages
Python has only one type of module object, and all modules are of this type (regardless of whether the module is implemented in Python or C). To help organize modules and provide a naming hierarchy, Python has a concept of packages.

> Think of packages as the directories and modules as files. 

All packages are modules, but not all modules are packages. Packages are a kind of module that contains a `__path__` attribute. 

All modules have a name. Subpackage names are separated from their parent package name by a dot:
- package `email`
- subpackage `email.mime`
- module `email.mime.text`
### Regular Packages
Traditional packages as they existed in Python 3.2 and earlier. It is typically implemented as a directory containing an `__init__.py` file. When it is imported, this `__init__.py` file is implicitly executed, and the object it defines are bound to names in the package's namespace. 

> A subdirectory inside a regular package that does not contain an `__init__.py` file is treated as an implicit _namespace package_ rooted in that parent.
### Namespace Packages
[PEP-0420](https://peps.python.org/pep-0420/)
A namespace package is a composite of various _portions_, where each portion contributes to a subpackage to the parent package. Portions may reside in different locations on the file system, zip files, on the network, or anything else that Python searching during import. They may not correspond directly to objects in the file system.

> Namespace packages use a custom iterable type which automatically performs a new search for package portions on the next import attempt within that package if the path of their parent package (or `sys.path` for a top-level package) changes.

While looking for a module or package named “foo”, for each directory in the parent path:
- If `<directory>/foo/__init__.py` is found, a regular package is imported and returned.
- If not, but `<directory>/foo.{py,pyc,so,pyd}` is found, a module is imported and returned. The exact list of extension varies by platform and whether the -O flag is specified. The list here is representative.
- If not, but `<directory>/foo` is found and is a directory, it is recorded and the scan continues with the next directory in the parent path.
- Otherwise the scan continues with the next directory in the parent path.

```
└── parent
	├── __init__.py      # regular package
    ├── one
    │   ├── __init__.py  # regular package
    │   └── one.py
    ├── parent.py
    └── two
        └── two.py       # namespace package
```

```python
>>> import parent
>>> print(parent)
<module 'parent' from '/home/alkonsta/test/parent/__init__.py'>
>>> import parent.two
>>> print(parent.two)
<module 'parent.two' (namespace) from ['/home/alkonsta/test/parent/two']>
```
## Searching
To begin searching, Python needs the _fully qualified_ name of the module. It may be the dotted path to a submodule, e.g. `foo.bar.baz`. In this case, Python first tries to import `foo`, then `foo.bar`, and finally `foo.bar.baz`. If any of the intermediate import fails, a `ModuleNotFoundError` is raised.
### The Module Cache: `sys.modules`
Python maintains a dictionary called `sys.modules`. It stores references to modules that have already been imported. When it encounters an `import` statement, Python first checks `sys.modules`.

```python
>>> import sys
>>> import math
>>> print("math" in sys.modules)
True
>>> print(sys.modules["math"])
<module 'math' from '/home/alkonsta/.pyenv/versions/3.14.1/lib/python3.14/lib-dynload/math.cpython-314-x86_64-linux-gnu.so'>
```

| Cache Status      | What Happens                             |
| ----------------- | ---------------------------------------- |
| Module exists     | Python reuses the existing module object |
| Module is missing | Python continues the import search       |
| Value is `None`   | Python raises `ModuleNotFoundError`      |
> For a nested module such as `foo.bar.baz`, the cache contains entries for all three levels.
#### Modifying the Cache
`sys.modules` is writable. Deleting an entry invalidates its cache entry and the next import triggers a new search. Deleting an entry does not necessarily destroy the original module object. Other parts of the program may still hold references to it. Assigning a module to `None` makes the next import raise `ModuleNotFoundError`.
```python
del sys.modules["foo"]
sys.modules["foo"] = None
```
#### Re-importing vs. Reloading
| Operation                              | Behavior                                              |
| -------------------------------------- | ----------------------------------------------------- |
| Delete from `sys.modules`, then import | Python may create a new module object                 |
| `importlib.reload(module)`             | Reuses the existing module object and reruns its code |
> References to the original module can become different from the newly imported module after cache invalidation
### Finders and Loaders
If a module is not found in `sys.modules`, Python invokes its import protocol.
- __Finder__ - Determines whether it can locate a requested module. It does not load it itself. If it finds it, it returns a __module spec__ containing information needed for the import.
- __Loader__ - Responsible for loading the module using the information provided by the module spec.
- __Importers__ - An object implementing both finder and loader interfaces
##### Default Search Mechanisms
Python provides default finders for:
- Built-in modules
- Frozen modules
- Modules found through an import path
	- Filesystem paths
	- ZIP files
	- Other resources supported by custom import mechanisms
> The import machinery is extensible: additional finders can be registered to expand how Python locates modules
### Import Hooks
Allows developers to customize Python's import system.
##### Meta Hooks
Run at the beginning of import processing, after the `sys.modules` cache lookup but before other import mechanisms. They can override normal searches. Meta hooks are registered through `sys.meta_path`.
##### Import Path Hooks
Run during the processing of `sys.path` or a package's `__path__`. They are registered through `sys.path_hooks`. These hooks customize how individual entries in the import path are handled.
### The Meta Path - `sys.meta_path`
When a module is not found in `sys.modules`, Python checks `sys.meta_path`. This is a list of meta path finder objects. Python queries them __in order__. Each meta path must implement `find_spec(fullname, path, target=None)`

| Parameter | Meaning                                         |
| --------- | ----------------------------------------------- |
| fullname  | Fully qualified name of the requested module    |
| path      | Locations to search; None for top-level modules |
| target    | Existing module object, supplied during reload  |
> Use `find_spec()` rather than the obsolete `find_module()` interface
### Import Search Flow

```text
import foo.bar.baz
        |
        v
  Check sys.modules
        |
   +----+----+
   |         |
 Found     Missing
   |         |
 Reuse       v
 module   Check sys.meta_path
             |
       Query finders
             |
       +-----+------+
       |            |
    Found spec    No spec
       |            |
       v            v
    Load module   ModuleNotFoundError
```
## Loading
Module importing has two major stages:
1. __Searching__ - Locates the requested module and obtains a module specification (`ModuleSpec`)
2. __Loading__ - Creates or obtains a module object, initializes its attributes, and executes its code
The loading process is primarily coordinated by Python's import machinery, while the loader is responsible for executing the module.
### Module Loading Process
Once a module spec is found, Python uses it and its associated loader to load it.

```python
module = None
if spec.loader is not None and hasattr(spec.loader, 'create_module'):
    # It is assumed 'exec_module' will also be defined on the loader.
    module = spec.loader.create_module(spec)
if module is None:
    module = ModuleType(spec.name)
# The import-related module attributes get set here:
_init_module_attrs(spec, module)

if spec.loader is None:
    # unsupported
    raise ImportError
if spec.origin is None and spec.submodule_search_locations is not None:
    # namespace package
    sys.modules[spec.name] = module
elif not hasattr(spec.loader, 'exec_module'):
    module = spec.loader.load_module(spec.name)
else:
    sys.modules[spec.name] = module
    try:
        spec.loader.exec_module(module)
    except BaseException:
        try:
            del sys.modules[spec.name]
        except KeyError:
            pass
        raise
return sys.modules[spec.name]
```

1. __Module registration happens before execution__
Python inserts the module into `sys.modules` before executing its code. Because a module might directly or indirectly import itself. Registering it first prevents _unbounded recursive imports_ and _the same module being loaded multiple times_.
2. __Module execution populates its namespace__
The loader executes the module's code. For a normal Python module, this means executing the code in its global namespace `module.__dict__`.
3. __Failed imports are cleaned up__
If execution fails, Python removes the failing module from `sys.modules`, previously cached modules remain cached, and other modules successfully imported as a side effects remain cached.
> This cleanup behavior differs from `importlib.reload()`, where a failing module remain in `sys.modules`.
### Loaders
Responsible for executing a module. Its main interface is `exec_module(module)`. A loader can optionally implement `create_module(spec)`.
### Submodules
A module contained within a package.

```text
# Example structure:
spam/
├── __init__.py
└── foo.py

# spam/foo.py
class Foo:
    pass

# spam/__init__.py
from .foo import Foo

# Both of these names are available
spam.foo
spam.Foo
```
### Module Specifications - `ModuleSpec`
Encapsulates import-related information about a module. It is created during module searching and used during module loading. It is accessible through `module.__spec__`. Its purpose is to transfer information between import system components.

Before Python 3.4, loaders were responsible for much of the boilerplate involved in loading modules. `ModuleSpec` allows the import machinery to handle those responsibilities consistently. 
> The special module `__main__` is an exception: its `__spec__` can be `None` in some situations.
### The `__path__` Attribute
A module with a `__path__` attribute is a __package__. This contains a sequence of strings identifying locations where Python should search for the package's submodules.

| Attribute          | Purpose                                                   |
| ------------------ | --------------------------------------------------------- |
| `sys.path`         | Locations searched for top-level modules and packages     |
| `package.__path__` | Locations searched for submodules of a particular package |
`package.__path__` is typically more restricted. Python also consults `sys.path_hooks` when traversing a package's `__path__`.
### Module Representations - `repr()`
The system uses module information to construct this representation. It prioritizes the module's specification, using information such as `name` / `loader` / `origin` / `has_location`. If the specification is unavailable, Python uses available module attributes like `module.__name__` / `module.__file__` / `module.__loader__`.
### Cached Bytecode Invalidation
Python can cache compiled bytecode in `.pyc` files. Before using cached bytecode, Python checks whether it is still valid relative to the source  `.py` file. The purpose is to avoid executing outdated compiled code after the source changes.
### Complete Import Lifecycle

```text
import package.module
        |
        v
Check sys.modules
        |
   +----+----+
   |         |
 Found     Missing
   |         |
 Reuse       v
 module   Search sys.meta_path
             |
             v
       Finder returns spec
             |
             v
       Create module object
             |
             v
       Initialize attributes
             |
             v
       Insert into sys.modules
             |
             v
       Loader executes module
             |
       +-----+-----+
       |           |
    Success      Failure
       |           |
       v           v
   Import       Remove failing
   complete     module from cache
                   |
                   v
               Propagate error
```
## The Path Based Finder
One of Python's default meta path finders. It searches the import path for modules and packages. It delegates the search to __path entry finders__, which understand specific type of locations.
### What Can Be Searched?
Filesystem directories, ZIP archives, other locations represented by strings, such as URLs or database queries. The default path entry finders support `.py` / `.pyc` / `.so` files.
### Meta Path Finders vs Path Entry Finders
| Meta Path Finder                              | Path Entry Finder                     |
| --------------------------------------------- | ------------------------------------- |
| Operates at the beginning of import searching | Operates on an individual path entry  |
| Registered in `sys.meta_path`                 | Obtained through `sys.path_hooks`     |
| Can handle imports using any strategy         | Handles a particular type of location |
| Part of the main import protocol              | Used by the path-based finder         |
> `PathFinder` is itself a meta path finder
### Three Main Variables
#### `sys.path`
List of strings representing locations where Python searches for top-level modules and packages. It is initialized using the `PYTHONPATH` environment variable, installation-specific defaults, and other implementation-specific configuration.
#### `sys.path_hooks`
Contains callable objects called __path entry hooks__. These hooks determine whether a particular path entry can be handled by a path entry finder. Each hook receives one argument - the path entry. It can either return a path entry finder that supports the location or raise `ImportError`. An `ImportError` from a hook is treated as an indication that the hooks cannot handle the path entry. Python ignores it and tries the next hook.
#### `sys.path_importer_cache`
Caches the relationship between path entries and their corresponding path entry finders. Searching for an appropriate finder can be expensive. 
Filesystem searches may require `stat()` calls. Instead of repeating the same search, Python caches the result. The cache can contain a path entry finder object or `None`, indicating that no finder was found for that entry. 
> This cache stores finder objects, not imported module objects
### How the Path-Based Search Works
When Python needs to search for a module using the path-based finder, it calls `PathFinder.find_spec(fullname, path=None)`. The `path` argument determines which locations are searched.

| Situation               | Search location             |
| ----------------------- | --------------------------- |
| Top-level Import        | `sys.path`                  |
| Import inside a package | Parent package's `__path__` |
### Search algorithm
For every entry in the search path:
1. Check `sys.path_importer_cache`.
2. If a finder is cached, reuse it.
3. If not, iterate through `sys.path_hooks`.
4. Call each hook with the path entry.
5. Use the first hook that returns a finder.
6. Cache the finder in `sys.path_importer_cache`.
7. Ask the finder to locate the requested module.
8. If a module spec is returned, the import process can continue with loading.

If no hook can handle the path entry:
- Python stores `None` in `sys.path_importer_cache`.
- The path-based finder returns `None` for that search.
### Special Case: Current Working Directory
Represented by an empty string ("") in `sys.path`. It is handled differently from other entries:
- If the current working directory cannot be determined or does not exist, no cache entry is stored.
- Python resolves the current working directory afresh for each module lookup.
- The actual resolved directory path, rather then the empty string, in used in `sys.path_importer_cache` and by `PathFinder.find_spec()`.
### Path Entry Finder Protocol
A path entry finder must implement `find_spec(fullname, target=None)`. Its responsibility is to locate a module or package within its associated path entry and return a module specification.
#### Namespace Packages
A path entry finder can also contribute a portion to a namespace package. To indicate that the spec represents a namespace package portion, it sets `spec.submodule_search_locations` to a list containing that portion. This allows multiple locations to contribute to the same namespace package.
### Complete Search Flow

```text
             import module
                   |
                   v
             PathFinder
                   |
                   v
             Read search path
              (sys.path)
                   |
                   v
          Iterate over path entries
                   |
                   v
       Check sys.path_importer_cache
              /          \
          Cached        Not cached
          finder           |
              |            v
              |       Iterate through
              |       sys.path_hooks
              |            |
              |       +----+-----+
              |       |          |
              |    Returns    ImportError
              |     finder        |
              |       |           v
              |       |       Try next hook
              |       |
              |       v
              |   Cache finder
              |       |
              +-------+
                   |
                   v
          Call find_spec()
                   |
             +-----+-----+
             |           |
          Found       Not found
             |           |
             v           v
        Return spec   Try next path
                      entry
```



## Replacing the Standard Import System
### Replace the Entire Import System
1. __Delete the default entries in `sys.meta_path`__
2. __Replace them with a custom meta path hook__
> `sys.meta_path` contains the finders consulted during module imports. Removing the standard finders prevents the normal import machinery from handling imports.
### Replace the `__import__()` function
If you only want to change the behavior of `import` statements, without affecting other APIs that access the import system, replacing the built-in `__import__()` function may be sufficient. This is a narrower intervention than replacing `sys.meta_path`.
### Block Specific Modules
A custom meta path finder can selectively prevent certain modules from being imported. Its `find_spec()` method has two important possible outcomes:
- __Return `None`__ - this finder cannot handle the module. Continue searching other meta path finders.
- __Raise `ModuleNotFoundError`__ - Stop the import search immediately and fail the import.
A custom finder placed early in `sys.meta_path` can block selected modules by raising `ModuleNotFoundError` directly from `find_spec()`.
## Package Relative Imports
Relative imports specify a module's location relative to the current package. They use leading dots. 
### Dot notation
- `.` - current package
- `..` - parent package
- `...` - grandparent package
- Each additional dot moves one level further up the package hierarchy
### Example
```text
# Package Structure

package/
    __init__.py
    subpackage1/
        __init__.py
        moduleX.py
        moduleY.py
    subpackage2/
        __init__.py
        moduleZ.py
    moduleA.py
    
# Valid Relative Imports (subpackage1/)

from .moduleY import spam
from .moduleY import spam as ham
from . import moduleY
from ..subpackage1 import moduleY
from ..subpackage2.moduleZ import eggs
from ..moduleA import foo
```
### Absolute vs. Relative Import Syntax
Absolute imports support both forms
- `import package.subpackage1.moduleY`
- `from package.subpackage1.moduleY import spam`
Relative imports only support the `from ... import ...` form
- `from .moduleY import spam`
- `from ..subpackage2.moduleZ import eggs`
This is because an absolute import such as `import package.subpackage1.moduleY` makes the full module path available as an expression. A relative path such as `.moduleY` cannot be used as a standalone expression. Therefore, relative imports require the `from ... import ...` syntax.
## `__main__`

Like `sys` and `builtins`, `__main__` is initialized directly when the interpreter starts. However, unlike those modules, it is not strictly considered a built-in module. Its initialization depends on how the Python interpreter is invoked, including its command-line flags and options.
### `__main__.__spec__`

The value of `__main__.__spec__` depends on how Python initializes the `__main__` module. `__spec__` contains the module specification associated with an importable module.
#### When `_spec__` is populated
When Python is started using the `-m` option: `python -m package.module`, `__main__.__spec__` is set to the module specification of the corresponding module or package. 
It is also populated when Python executes a directory, a ZIP file, or another `sys.path` entry.
#### When `__spec__` is `None`
In this cases, the code used to populate `__main__` does not correspond directly to an importable module. Direct file execution always sets `__main__.__spec__` to `None`, even if the file could technically be imported as a module.

| Execution mode        | Example                      |
| --------------------- | ---------------------------- |
| Interactive prompt    | `python`                     |
| `-c` option           | `python -c "print('hello')"` |
| Standard input        | `python < script.py`         |
| Direct file execution | `python script.py`           |
> If valid module metadata is needed, use the `-m` option
#### `__main__` and the original module are distinct

Even when `__main__.__spec__` refers to an importable module, `__main__` and that module are still considered distinct modules. 
```python
# example.py
print("Module code executed")

if __name__ == "__main__":
     print("Executed as the main program")
```

```bash
python example.py
>> Module code executed
>> Executed as the main program
```

```python
import example
# Output: Module code executed
```