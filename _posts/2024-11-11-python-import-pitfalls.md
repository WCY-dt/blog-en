---
layout: post
title:  "Python Import Pitfalls"
date:   2024-11-11 17:00:00 +0800
categories: programming
tags: python
summary: "This article introduces how Python's import statement works, including absolute and relative path imports, package scope pitfalls, and __init__.py pitfalls. Understanding these concepts can help avoid issues when importing modules."
comments: true
mathjax: true
copyrights: 原创
---

When we use the `import` statement to import modules in Python, the Python interpreter follows certain rules to locate modules:

1. First, check if the module is already cached in `sys.modules`;
2. If not cached, check built-in modules;
3. If not found in built-in modules, search for the module according to paths in `sys.path`:
    1. First search in the directory where the current script is located;
    2. Then search in system default paths, such as Python's installation directory, Python's library directory, etc.

## Absolute and Relative Paths

Let's start with the simplest case: two files `moduleA.py` and `moduleB.py` in the root directory. `moduleA.py` contains the following code:

```python
def foo():
    print('moduleA foo()')
```

`moduleB.py` contains the following code:

```python
from moduleA import foo

def bar():
    foo()

if __name__ == '__main__':
    bar()
```

Running `python moduleB.py` directly will output `moduleA foo()` normally.

The above code can also be written as:

```python
import moduleA

def bar():
    moduleA.foo()

if __name__ == '__main__':
    bar()
```

This will also output `moduleA foo()` normally.

Now, let's move both `moduleA.py` and `moduleB.py` to the `packageA/subpackageA` directory, with the following structure:

```plaintext
packageA/
    subpackageA/
        moduleA.py
        moduleB.py
```

Running `python packageA/subpackageA/moduleB.py` from the root directory will still output `moduleA foo()` normally.

Now, let's create a new `main.py` file in the root directory with the following content:

```python
from packageA.subpackageA.moduleB import bar

if __name__ == '__main__':
    bar()
```

Running `python main.py` from the root directory will result in an error:

```plaintext
Traceback (most recent call last):
  File "main.py", line 1, in <module>
    from packageA.subpackageA.moduleB import bar
  File "/path/to/packageA/subpackageA/moduleB.py", line 1, in <module>
    from moduleA import foo
ModuleNotFoundError: No module named 'moduleA'
```

This is because when Python searches for modules, it only searches in the directory where `main.py` is located, not in parent or subdirectories.

There are two solutions:

1. Absolute path import:

    Change the import statement in `moduleB.py` to:

    ```python
    from packageA.subpackageA.moduleA import foo
    ```

    This will output `moduleA foo()` normally.

    However, if we run `python packageA/subpackageA/moduleB.py` from the root directory now, it will error:

    ```plaintext
    Traceback (most recent call last):
      File "packageA/subpackageA/moduleB.py", line 1, in <module>
        from packageA.subpackageA.moduleA import foo
    ModuleNotFoundError: No module named 'packageA'
    ```

    Because the current `sys.path` directory is `packageA/subpackageA`, which doesn't contain `packageA`.

2. Relative path import:

    Change the import statement in `moduleB.py` to:

    ```python
    from .moduleA import foo
    ```

    This will output `moduleA foo()` normally.

    Here, `.` represents the directory where `moduleB.py` is located, i.e., the `subpackageA` directory.

Like command line paths, relative paths can also use `..` to represent the parent directory. Let's create a new `packageA/subpackageB/moduleC.py` file with the following content:

```python
from ..subpackageA.moduleA import foo

def baz():
    foo()
```

The folder structure is now:

```plaintext
main.py
packageA/
    subpackageA/
        moduleA.py
        moduleB.py
    subpackageB/
        moduleC.py
```

We import `moduleC.py` in `main.py`:

```python
from packageA.subpackageB.moduleC import baz

if __name__ == '__main__':
    baz()
```

Running `python main.py` from the root directory will output `moduleA foo()` normally.

We need to understand that when Python executes `import` statements, it converts relative paths to absolute paths. For example, when we run from the root directory, the import statement in `moduleC.py` is converted to:

```python
from packageA.subpackageA.moduleA import foo
```

## Package Scope Pitfall

Now, let's create a new `submain.py` file in the `packageA` directory with the following content:

```python
from subpackageB.moduleC import baz

if __name__ == '__main__':
    baz()
```

The folder structure is now:

```plaintext
main.py
packageA/
    submain.py
    subpackageA/
        moduleA.py
        moduleB.py
    subpackageB/
        moduleC.py
```

Running `python packageA/submain.py` will result in an error:

```plaintext
Traceback (most recent call last):
  File "/path/to/packageA/submain.py", line 1, in <module>
    from subpackageB.moduleC import baz
  File "/path/to/packageA/subpackageB/moduleC.py", line 1, in <module>
    from ..subpackageA.moduleA import foo
ImportError: attempted relative import beyond top-level package
```

This is because relative paths can only be used within packages, not outside packages. Here, when using `from subpackageB.moduleC import baz`, `subpackageB` becomes the top-level package, which prevents `moduleC.py` from accessing `subpackageA` outside the top-level package.

Not only `from ... import ...` will error, but `import ... as ...` follows the same principle.

Changing the import statement in `moduleC.py` to an absolute path can solve this problem.

Previously, we used `from packageA.subpackageB.moduleC import baz` in `main.py`, where `packageA` is the top-level package, so `moduleC.py` can access the subpackage `subpackageA` under the top-level package.

This helps us understand another issue. The current `moduleB.py` is:

```python
    from .moduleA import foo

    def bar():
        foo()

    if __name__ == '__main__':
        bar()
```

If we run `python packageA/subpackageA/moduleB.py` directly from the root directory, it will error:

```plaintext
Traceback (most recent call last):
  File "packageA/subpackageA/moduleB.py", line 1, in <module>
    from .moduleA import foo
ImportError: attempted relative import with no known parent package
```

This is because `moduleB.py` is running as a standalone script file here and doesn't belong to any package. Therefore, when using relative path imports, it cannot find the top-level package, let alone subpackages or modules under the top-level package.

## `__init__.py` Pitfall

Finally, let's discuss the `__init__.py` file. Since Python 3.3, `__init__.py` files are no longer required. Whether there's an `__init__.py` file or not, a folder can be imported as a package. In other words, as long as you create a folder, it's a package; and any `.py` file can be imported as a module.

However, `__init__.py` files still have their purpose. For example, when we import a package for the *first time*, Python automatically executes the `__init__.py` file in that package.

For example, we have the following folder structure:

```plaintext
main.py
packageA/
    __init__.py
```

The `__init__.py` file contains the following code:

```python
print('packageA __init__.py')
```

Import `packageA` in `main.py`:

```python
import packageA
```

Running `python main.py` will output `packageA __init__.py`.

However, if we change the import statement in `main.py` to:

```python
import packageA
import packageA
import packageA
import packageA
```

Running `python main.py` will still only output `packageA __init__.py` once. This is determined by the first rule mentioned at the beginning of this article.
