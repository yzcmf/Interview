======
Future
======


`Future statements <https://docs.python.org/3/reference/simple_stmts.html#future>`_
tell the interpreter to compile some semantics as the semantics which will be
available in the future Python version. In other words, Python uses ``from __future__ import feature``
to backport features from other higher Python versions to the current interpreter.
In Python 3, many features such as ``print_function`` are already enabled, but
we still leave these future statements for backward compatibility.

Future statements are **NOT** import statements. Future statements change how
Python interprets the code. They **MUST** be at the top of the file. Otherwise,
Python interpreter will raise ``SyntaxError``.

If you're interested in future statements and want to acquire more explanation,
further information can be found on `PEP 236 - Back to the __future__  <https://www.python.org/dev/peps/pep-0236>`_

.. contents:: Table of Contents
    :backlinks: none

List All New Features
---------------------

`__future__ <https://docs.python.org/3/library/__future__.html>`_ is a Python
module. We can use it to check what kind of future features can import to
current Python interpreter. The fun is ``import __future__`` is **NOT** a future
statement, it is a import statement.

.. code-block:: python

    >>> from pprint import pprint
    >>> import __future__
    >>> pprint(__future__.all_feature_names)
    ['nested_scopes',
     'generators',
     'division',
     'absolute_import',
     'with_statement',
     'print_function',
     'unicode_literals',
     'barry_as_FLUFL',
     'generator_stop',
     'annotations']

Future statements not only change the behavior of the Python interpreter but
also import ``__future__._Feature`` into the current program.

.. code-block:: python

    >>> from __future__ import print_function
    >>> print_function
    _Feature((2, 6, 0, 'alpha', 2), (3, 0, 0, 'alpha', 0), 65536)

Print Function
--------------

Replacing **print statement** to  **print function** is one of the most
notorious decision in Python history. However, this change brings some
flexibilities to extend the ability of ``print``. Further information can
be found on PEP `3105 <https://www.python.org/dev/peps/pep-3105>`_.

.. code-block:: python

    >>> print "Hello World"  # print is a statement
    Hello World
    >>> from __future__ import print_function
    >>> print "Hello World"
      File "<stdin>", line 1
        print "Hello World"
                          ^
    SyntaxError: invalid syntax
    >>> print("Hello World") # print become a function
    Hello World

Unicode
-------

As **print function**, making text become Unicode is another infamous decision.
Nevertheless, many modern programming languages’ text is Unicode. This change
compels us to decode texts early in order to prevent runtime error after we
run programs for a while. Further information can be found on PEP
`3112 <https://www.python.org/dev/peps/pep-3112>`_.

.. code-block:: python

    >>> type("Guido") # string type is str in python2
    <type 'str'>
    >>> from __future__ import unicode_literals
    >>> type("Guido") # string type become unicode
    <type 'unicode'>

Division
--------

Sometimes, it is counterintuitive when the division result is int or long.
In this case, Python 3 enables the **true division** by default. However, in
Python 2, we have to backport ``division`` to the current interpreter. Further
information can be found on  PEP `238 <https://www.python.org/dev/peps/pep-0238>`_.

.. code-block:: python

    >>> 1 / 2
    0
    >>> from __future__ import division
    >>> 1 / 2   # return a float (classic division)
    0.5
    >>> 1 // 2  # return a int (floor division)
    0

Annotations
-----------

Before Python 3.7, we cannot assign annotations in a class or a function if
it is not available in the current scope. A common situation is the definition
of a container class.

.. code-block:: python

    class Tree(object):

        def insert(self, tree: Tree): ...

Example

.. code-block:: bash

    $ python3 foo.py
    Traceback (most recent call last):
      File "foo.py", line 1, in <module>
        class Tree(object):
      File "foo.py", line 3, in Tree
        def insert(self, tree: Tree): ...
    NameError: name 'Tree' is not defined

In this case, the definition of the class is not available yet. Python interpreter
cannot parse the annotation during their definition time. To solve this issue,
Python uses string literals to replace the class.

.. code-block:: python

    class Tree(object):

        def insert(self, tree: 'Tree'): ...

After version 3.7, Python introduces the future statement, ``annotations``, to
perform postponed evaluation. It will become the default feature in Python 4.
For further information please refer to PEP `563 <https://www.python.org/dev/peps/pep-0563>`_.


.. code-block:: python

    from __future__ import annotations

    class Tree(object):

        def insert(self, tree: Tree): ...

BDFL Retirement
---------------

**New in Python 3.1**

PEP `401 <https://www.python.org/dev/peps/pep-0401/>`_ is just an Easter egg.
This feature brings the current interpreter back to the past. It enables the
diamond operator ``<>`` in Python 3.

.. code-block:: python

    >>> 1 != 2
    True
    >>> from __future__ import barry_as_FLUFL
    >>> 1 != 2
      File "<stdin>", line 1
        1 != 2
           ^
    SyntaxError: with Barry as BDFL, use '<>' instead of '!='
    >>> 1 <> 2
    True

Braces
------

``braces`` is an Easter egg. The source code can be found on
`future.c <https://github.com/python/cpython/blob/master/Python/future.c>`_.

.. code-block:: python

    >>> from __future__ import braces
      File "<stdin>", line 1
    SyntaxError: not a chance
