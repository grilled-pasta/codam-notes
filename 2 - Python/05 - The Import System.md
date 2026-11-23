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
