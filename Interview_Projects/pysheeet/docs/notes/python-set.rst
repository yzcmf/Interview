===
Set
===

Set comprehension
-----------------

.. code-block:: python

    >>> a = [1, 2, 5, 6, 6, 6, 7]
    >>> s = {x for x in a}
    >>> s
    set([1, 2, 5, 6, 7])
    >>> s = {x for x in a if x > 3}
    >>> s
    set([5, 6, 7])
    >>> s = {x if x > 3 else -1 for x in a}
    >>> s
    set([6, 5, -1, 7])

Uniquify a List
---------------

.. code-block:: python

    >>> a = [1, 2, 2, 2, 3, 4, 5, 5]
    >>> a
    [1, 2, 2, 2, 3, 4, 5, 5]
    >>> ua = list(set(a))
    >>> ua
    [1, 2, 3, 4, 5]

Union Two Sets
--------------

.. code-block:: python

    >>> a = set([1, 2, 2, 2, 3])
    >>> b = set([5, 5, 6, 6, 7])
    >>> a | b
    set([1, 2, 3, 5, 6, 7])
    >>> # or
    >>> a = [1, 2, 2, 2, 3]
    >>> b = [5, 5, 6, 6, 7]
    >>> set(a + b)
    set([1, 2, 3, 5, 6, 7])

Append Items to a Set
---------------------

.. code-block:: python

    >>> a = set([1, 2, 3, 3, 3])
    >>> a.add(5)
    >>> a
    set([1, 2, 3, 5])
    >>> # or
    >>> a = set([1, 2, 3, 3, 3])
    >>> a |= set([1, 2, 3, 4, 5, 6])
    >>> a
    set([1, 2, 3, 4, 5, 6])

Intersection Two Sets
---------------------

.. code-block:: python

    >>> a = set([1, 2, 2, 2, 3])
    >>> b = set([1, 5, 5, 6, 6, 7])
    >>> a & b
    set([1])

Common Items from Sets
----------------------

.. code-block:: python

    >>> a = [1, 1, 2, 3]
    >>> b = [1, 3, 5, 5, 6, 6]
    >>> com = list(set(a) & set(b))
    >>> com
    [1, 3]

Contain
-------

b contains a

.. code-block:: python

    >>> a = set([1, 2])
    >>> b = set([1, 2, 5, 6])
    >>> a <= b
    True

a contains b

.. code-block:: python

    >>> a = set([1, 2, 5, 6])
    >>> b = set([1, 5, 6])
    >>> a >= b
    True

Set Diff
--------

.. code-block:: python

    >>> a = set([1, 2, 3])
    >>> b = set([1, 5, 6, 7, 7])
    >>> a - b
    set([2, 3])

Symmetric diff
--------------

.. code-block:: python

    >>> a = set([1,2,3])
    >>> b = set([1, 5, 6, 7, 7])
    >>> a ^ b
    set([2, 3, 5, 6, 7])
