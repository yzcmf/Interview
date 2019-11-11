.. meta::
    :description lang=en: Collect useful snippets of Python typing
    :keywords: Python3, Static Typing, Python Type hints, Type hints Cheat Sheet

======
Typing
======

PEP `484 <https://www.python.org/dev/peps/pep-0484/>`_, which provides a
specification about what a type system should look like in Python3, introduced
the concept of type hints. Moreover, to better understand the type hints design
philosophy, it is crucial to read PEP `483 <https://www.python.org/dev/peps/pep-0483/>`_
that would be helpful to aid a pythoneer to understand reasons why Python
introduce a type system. The main goal of this cheat sheet is to show some
common usage about type hints in Python3.


.. contents:: Table of Contents
    :backlinks: none

Without type check
-------------------

.. code-block:: python

    def fib(n):
        a, b = 0, 1
        for _ in range(n):
            yield a
            b, a = a + b, b

    print([n for n in fib(3.6)])


output:

.. code-block:: bash

    # errors will not be detected until runtime

    $ python fib.py
    Traceback (most recent call last):
      File "fib.py", line 8, in <module>
        print([n for n in fib(3.5)])
      File "fib.py", line 8, in <listcomp>
        print([n for n in fib(3.5)])
      File "fib.py", line 3, in fib
        for _ in range(n):
    TypeError: 'float' object cannot be interpreted as an integer


With type check
----------------

.. code-block:: python

    # give a type hint
    from typing import Generator

    def fib(n: int) -> Generator:
        a: int = 0
        b: int = 1
        for _ in range(n):
            yield a
            b, a = a + b, b

    print([n for n in fib(3.6)])

output:

.. code-block:: bash

    # errors will be detected before running

    $ mypy --strict fib.py
    fib.py:12: error: Argument 1 to "fib" has incompatible type "float"; expected "int"

Basic types
-----------

.. code-block:: python

    import io
    import re

    from collections import deque, namedtuple
    from typing import (
        Dict,
        List,
        Tuple,
        Set,
        Deque,
        NamedTuple,
        IO,
        Pattern,
        Match,
        Text,
        Optional,
        Sequence,
        Iterable,
        Mapping,
        MutableMapping,
        Any,
    )

    # without initializing
    x: int

    # any type
    y: Any
    y = 1
    y = "1"

    # built-in
    var_int: int = 1
    var_str: str = "Hello Typing"
    var_byte: bytes = b"Hello Typing"
    var_bool: bool = True
    var_float: float = 1.
    var_unicode: Text = u'\u2713'

    # could be none
    var_could_be_none: Optional[int] = None
    var_could_be_none = 1

    # collections
    var_set: Set[int] = {i for i in range(3)}
    var_dict: Dict[str, str] = {"foo": "Foo"}
    var_list: List[int] = [i for i in range(3)]
    var_Tuple: Tuple = (1, 2, 3)
    var_deque: Deque = deque([1, 2, 3])
    var_nametuple: NamedTuple = namedtuple('P', ['x', 'y'])

    # io
    var_io_str: IO[str] = io.StringIO("Hello String")
    var_io_byte: IO[bytes] = io.BytesIO(b"Hello Bytes")
    var_io_file_str: IO[str] = open(__file__)
    var_io_file_byte: IO[bytes] = open(__file__, 'rb')

    # re
    p: Pattern = re.compile("(https?)://([^/\r\n]+)(/[^\r\n]*)?")
    m: Optional[Match] = p.match("https://www.python.org/")

    # duck types: list-like
    var_seq_list: Sequence[int] = [1, 2, 3]
    var_seq_tuple: Sequence[int] = (1, 2, 3)
    var_iter_list: Iterable[int] = [1, 2, 3]
    var_iter_tuple: Iterable[int] = (1, 2, 3)

    # duck types: dict-like
    var_map_dict: Mapping[str, str] = {"foo": "Foo"}
    var_mutable_dict: MutableMapping[str, str] = {"bar": "Bar"}

Functions
----------

.. code-block:: python

    from typing import Generator, Callable

    # function
    def gcd(a: int, b: int) -> int:
        while b:
            a, b = b, a % b
        return a

    # callback
    def fun(cb: Callable[[int, int], int]) -> int:
        return cb(55, 66)

    # lambda
    f: Callable[[int], int] = lambda x: x * 2

Classes
--------

.. code-block:: python

    from typing import ClassVar, Dict, List

    class Foo:

        x: int = 1  # instance variable. default = 1
        y: ClassVar[str] = "class var"  # class variable

        def __init__(self) -> None:
            self.i: List[int] = [0]

        def foo(self, a: int, b: str) -> Dict[int, str]:
            return {a: b}

    foo = Foo()
    foo.x = 123

    print(foo.x)
    print(foo.i)
    print(Foo.y)
    print(foo.foo(1, "abc"))

Generator
----------

.. code-block:: python

    from typing import Generator

    # Generator[YieldType, SendType, ReturnType]
    def fib(n: int) -> Generator[int, None, None]:
        a: int = 0
        b: int = 1
        while n > 0:
            yield a
            b, a = a + b, b
            n -= 1

    g: Generator = fib(10)
    i: Iterator[int] = (x for x in range(3))

Asynchronous Generator
-----------------------

.. code-block:: python

    import asyncio

    from typing import AsyncGenerator, AsyncIterator

    async def fib(n: int) -> AsyncGenerator:
        a: int = 0
        b: int = 1
        while n > 0:
            await asyncio.sleep(0.1)
            yield a

            b, a = a + b, b
            n -= 1

    async def main() -> None:
        async for f in fib(10):
            print(f)

        ag: AsyncIterator = (f async for f in fib(10))

    loop = asyncio.get_event_loop()
    loop.run_until_complete(main())

Context Manager
---------------

.. code-block:: python

    from typing import ContextManager, Generator, IO
    from contextlib import contextmanager

    @contextmanager
    def open_file(name: str) -> Generator:
        f = open(name)
        yield f
        f.close()

    cm: ContextManager[IO] = open_file(__file__)
    with cm as f:
        print(f.read())

Asynchronous Context Manager
-----------------------------

.. code-block:: python

    import asyncio

    from typing import AsyncContextManager, AsyncGenerator, IO
    from contextlib import asynccontextmanager

    # need python 3.7 or above
    @asynccontextmanager
    async def open_file(name: str) -> AsyncGenerator:
        await asyncio.sleep(0.1)
        f = open(name)
        yield f
        await asyncio.sleep(0.1)
        f.close()

    async def main() -> None:
        acm: AsyncContextManager[IO] = open_file(__file__)
        async with acm as f:
            print(f.read())

    loop = asyncio.get_event_loop()
    loop.run_until_complete(main())

Avoid ``None`` access
----------------------

.. code-block:: python

    import re

    from typing import Pattern, Dict, Optional

    # like c++
    # std::regex url("(https?)://([^/\r\n]+)(/[^\r\n]*)?");
    # std::regex color("^#?([a-f0-9]{6}|[a-f0-9]{3})$");

    url: Pattern = re.compile("(https?)://([^/\r\n]+)(/[^\r\n]*)?")
    color: Pattern = re.compile("^#?([a-f0-9]{6}|[a-f0-9]{3})$")

    x: Dict[str, Pattern] = {"url": url, "color": color}
    y: Optional[Pattern] = x.get("baz", None)

    print(y.match("https://www.python.org/"))

output:

.. code-block:: bash

    $ mypy --strict foo.py
    foo.py:15: error: Item "None" of "Optional[Pattern[Any]]" has no attribute "match"

Positional-only arguments
--------------------------

.. code-block:: python

    # define arguments with names beginning with __

    def fib(__n: int) -> int:  # positional only arg
        a, b = 0, 1
        for _ in range(__n):
            b, a = a + b, b
        return a


    def gcd(*, a: int, b: int) -> int:  # keyword only arg
        while b:
            a, b = b, a % b
        return a


    print(fib(__n=10))  # error
    print(gcd(10, 5))   # error

output:

.. code-block:: bash

    mypy --strict foo.py
    foo.py:1: note: "fib" defined here
    foo.py:14: error: Unexpected keyword argument "__n" for "fib"
    foo.py:15: error: Too many positional arguments for "gcd"

Multiple return values
-----------------------

.. code-block:: python

    from typing import Tuple, Iterable, Union

    def foo(x: int, y: int) -> Tuple[int, int]:
        return x, y

    # or

    def bar(x: int, y: str) -> Iterable[Union[int, str]]:
        # XXX: not recommend declaring in this way
        return x, y

    a: int
    b: int
    a, b = foo(1, 2)      # ok
    c, d = bar(3, "bar")  # ok

Union[Any, None] == Optional[Any]
----------------------------------

.. code-block:: python

    from typing import List, Union

    def first(l: List[Union[int, None]]) -> Union[int, None]:
        return None if len(l) == 0 else l[0]

    first([None])

    # equal to

    from typing import List, Optional

    def first(l: List[Optional[int]]) -> Optional[int]:
        return None if len(l) == 0 else l[0]

    first([None])

Be careful of ``Optional``
---------------------------

.. code-block:: python

    from typing import cast, Optional

    def fib(n):
        a, b = 0, 1
        for _ in range(n):
            b, a = a + b, b
        return a

    def cal(n: Optional[int]) -> None:
        print(fib(n))

    cal(None)

output:

.. code-block:: bash

    # mypy will not detect errors
    $ mypy foo.py

Explicitly declare

.. code-block:: python

    from typing import Optional

    def fib(n: int) -> int:  # declare n to be int
        a, b = 0, 1
        for _ in range(n):
            b, a = a + b, b
        return a

    def cal(n: Optional[int]) -> None:
        print(fib(n))

output:

.. code-block:: bash

    # mypy can detect errors even we do not check None
    $ mypy --strict foo.py
    foo.py:11: error: Argument 1 to "fib" has incompatible type "Optional[int]"; expected "int"

Be careful of casting
----------------------

.. code-block:: python

    from typing import cast, Optional

    def gcd(a: int, b: int) -> int:
        while b:
            a, b = b, a % b
        return a

    def cal(a: Optional[int], b: Optional[int]) -> None:
        # XXX: Avoid casting
        ca, cb = cast(int, a), cast(int, b)
        print(gcd(ca, cb))

    cal(None, None)

output:

.. code-block:: bash

    # mypy will not detect type errors
    $ mypy --strict foo.py


Forward references
-------------------

Based on PEP 484, if we want to reference a type before it has been declared, we
have to use **string literal** to imply that there is a type of that name later on
in the file.

.. code-block:: python

    from typing import Optional


    class Tree:
        def __init__(
            self, data: int,
            left: Optional["Tree"],  # Forward references.
            right: Optional["Tree"]
        ) -> None:
            self.data = data
            self.left = left
            self.right = right

.. note::

    There are some issues that mypy does not complain about Forward References.
    Get further information from `Issue#948`_.

.. _Issue\#948: https://github.com/python/mypy/issues/948

.. code-block:: python

    class A:
        def __init__(self, a: A) -> None:  # should fail
            self.a = a

output:

.. code-block:: bash

    $ mypy --strict type.py
    $ echo $?
    0
    $ python type.py   # get runtime fail
    Traceback (most recent call last):
      File "type.py", line 1, in <module>
        class A:
      File "type.py", line 2, in A
        def __init__(self, a: A) -> None:  # should fail
    NameError: name 'A' is not defined

Postponed Evaluation of Annotations
-----------------------------------

**New in Python 3.7**

- PEP 563_ - Postponed Evaluation of Annotations

.. _563: https://www.python.org/dev/peps/pep-0563/

Before Python 3.7

.. code-block:: python

    >>> class A:
    ...     def __init__(self, a: A) -> None:
    ...         self._a = a
    ...
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "<stdin>", line 2, in A
    NameError: name 'A' is not defined

After Python 3.7 (include 3.7)

.. code-block:: python

    >>> from __future__ import annotations
    >>> class A:
    ...     def __init__(self, a: A) -> None:
    ...         self._a = a
    ...

.. note::

    Annotation can only be used within the scope which names have already
    existed. Therefore, **forward reference** does not support the case which
    names are not available in the current scope. **Postponed evaluation
    of annotations** will become the default behavior in Python 4.0.

Type alias
----------

Like ``typedef`` or ``using`` in c/c++

.. code-block:: cpp

    #include <iostream>
    #include <string>
    #include <regex>
    #include <vector>

    typedef std::string Url;
    template<typename T> using Vector = std::vector<T>;

    int main(int argc, char *argv[])
    {
        Url url = "https://python.org";
        std::regex p("(https?)://([^/\r\n]+)(/[^\r\n]*)?");
        bool m = std::regex_match(url, p);
        Vector<int> v = {1, 2};

        std::cout << m << std::endl;
        for (auto it : v) std::cout << it << std::endl;
        return 0;
    }

Type aliases are defined by simple variable assignments

.. code-block:: python

    import re

    from typing import Pattern, List

    # Like typedef, using in c/c++

    # PEP 484 recommend capitalizing alias names
    Url = str

    url: Url = "https://www.python.org/"

    p: Pattern = re.compile("(https?)://([^/\r\n]+)(/[^\r\n]*)?")
    m = p.match(url)

    Vector = List[int]
    v: Vector = [1., 2.]

Define a ``NewType``
---------------------

Unlike alias, ``NewType`` returns a separate type but is identical to the original type at runtime.

.. code-block:: python

    from sqlalchemy import Column, String, Integer
    from sqlalchemy.ext.declarative import declarative_base
    from typing import NewType, Any

    # check mypy #2477
    Base: Any = declarative_base()

    # create a new type
    Id = NewType('Id', int) # not equal alias, it's a 'new type'

    class User(Base):
        __tablename__ = 'User'
        id = Column(Integer, primary_key=True)
        age = Column(Integer, nullable=False)
        name = Column(String, nullable=False)

        def __init__(self, id: Id, age: int, name: str) -> None:
            self.id = id
            self.age = age
            self.name = name

    # create users
    user1 = User(Id(1), 62, "Guido van Rossum") # ok
    user2 = User(2, 48, "David M. Beazley")     # error

output:

.. code-block:: bash

    $ python foo.py
    $ mypy --ignore-missing-imports foo.py
    foo.py:24: error: Argument 1 to "User" has incompatible type "int"; expected "Id"

Further reading:

- `Issue\#1284`_

.. _`Issue\#1284`: https://github.com/python/mypy/issues/1284


Using ``TypeVar`` as template
------------------------------

Like c++ ``template <typename T>``

.. code-block:: cpp

    #include <iostream>

    template <typename T>
    T add(T x, T y) {
        return x + y;
    }

    int main(int argc, char *argv[])
    {
        std::cout << add(1, 2) << std::endl;
        std::cout << add(1., 2.) << std::endl;
        return 0;
    }

Python using ``TypeVar``

.. code-block:: python

    from typing import TypeVar

    T = TypeVar("T")

    def add(x: T, y: T) -> T:
        return x + y

    add(1, 2)
    add(1., 2.)

Using ``TypeVar`` and ``Generic`` as class template
----------------------------------------------------

Like c++ ``template <typename T> class``

.. code-block:: cpp

    #include <iostream>

    template<typename T>
    class Foo {
    public:
        Foo(T foo) {
            foo_ = foo;
        }
        T Get() {
            return foo_;
        }
    private:
        T foo_;
    };

    int main(int argc, char *argv[])
    {
        Foo<int> f(123);
        std::cout << f.Get() << std::endl;
        return 0;
    }

Define a generic class in Python

.. code-block:: python

    from typing import Generic, TypeVar

    T = TypeVar("T")

    class Foo(Generic[T]):
        def __init__(self, foo: T) -> None:
            self.foo = foo

        def get(self) -> T:
            return self.foo

    f: Foo[str] = Foo("Foo")
    v: int = f.get()

output:

.. code-block:: bash

    $ mypy --strict foo.py
    foo.py:13: error: Incompatible types in assignment (expression has type "str", variable has type "int")

Scoping rules for ``TypeVar``
------------------------------

- ``TypeVar`` used in different generic function will be inferred to be different types.

.. code-block:: python

    from typing import TypeVar

    T = TypeVar("T")

    def foo(x: T) -> T:
        return x

    def bar(y: T) -> T:
        return y

    a: int = foo(1)    # ok: T is inferred to be int
    b: int = bar("2")  # error: T is inferred to be str

output:

.. code-block:: bash

    $ mypy --strict foo.py
    foo.py:12: error: Incompatible types in assignment (expression has type "str", variable has type "int")

- ``TypeVar`` used in a generic class will be inferred to be same types.

.. code-block:: python

    from typing import TypeVar, Generic

    T = TypeVar("T")

    class Foo(Generic[T]):

        def foo(self, x: T) -> T:
            return x

        def bar(self, y: T) -> T:
            return y

    f: Foo[int] = Foo()
    a: int = f.foo(1)    # ok: T is inferred to be int
    b: str = f.bar("2")  # error: T is expected to be int

output:

.. code-block:: bash

    $ mypy --strict foo.py
    foo.py:15: error: Incompatible types in assignment (expression has type "int", variable has type "str")
    foo.py:15: error: Argument 1 to "bar" of "Foo" has incompatible type "str"; expected "int"

- ``TypeVar`` used in a method but did not match any parameters which declare in ``Generic`` can be inferred to be different types.

.. code-block:: python

    from typing import TypeVar, Generic

    T = TypeVar("T")
    S = TypeVar("S")

    class Foo(Generic[T]):    # S does not match params

        def foo(self, x: T, y: S) -> S:
            return y

        def bar(self, z: S) -> S:
            return z

    f: Foo[int] = Foo()
    a: str = f.foo(1, "foo")  # S is inferred to be str
    b: int = f.bar(12345678)  # S is inferred to be int

output:

.. code-block:: bash

    $  mypy --strict foo.py

- ``TypeVar`` should not appear in body of method/function if it is unbound type.

.. code-block:: python

    from typing import TypeVar, Generic

    T = TypeVar("T")
    S = TypeVar("S")

    def foo(x: T) -> None:
        a: T = x    # ok
        b: S = 123  # error: invalid type

output:

.. code-block:: bash

    $ mypy --strict foo.py
    foo.py:8: error: Invalid type "foo.S"

Restricting to a fixed set of possible types
----------------------------------------------

``T = TypeVar('T', ClassA, ...)`` means we create a **type variable with a value restriction**.

.. code-block:: python

    from typing import TypeVar

    # restrict T = int or T = float
    T = TypeVar("T", int, float)

    def add(x: T, y: T) -> T:
        return x + y

    add(1, 2)
    add(1., 2.)
    add("1", 2)
    add("hello", "world")

output:

.. code-block:: bash

    # mypy can detect wrong type
    $ mypy --strict foo.py
    foo.py:10: error: Value of type variable "T" of "add" cannot be "object"
    foo.py:11: error: Value of type variable "T" of "add" cannot be "str"

``TypeVar`` with an upper bound
--------------------------------

``T = TypeVar('T', bound=BaseClass)`` means we create a **type variable with an upper bound**.
The concept is similar to **polymorphism** in c++.

.. code-block:: cpp

    #include <iostream>

    class Shape {
    public:
        Shape(double width, double height) {
            width_ = width;
            height_ = height;
        };
        virtual double Area() = 0;
    protected:
        double width_;
        double height_;
    };

    class Rectangle: public Shape {
    public:
        Rectangle(double width, double height)
        :Shape(width, height)
        {};

        double Area() {
            return width_ * height_;
        };
    };

    class Triangle: public Shape {
    public:
        Triangle(double width, double height)
        :Shape(width, height)
        {};

        double Area() {
            return width_ * height_ / 2;
        };
    };

    double Area(Shape &s) {
        return s.Area();
    }

    int main(int argc, char *argv[])
    {
        Rectangle r(1., 2.);
        Triangle t(3., 4.);

        std::cout << Area(r) << std::endl;
        std::cout << Area(t) << std::endl;
        return 0;
    }

Like c++, create a base class and ``TypeVar`` which bounds to the base class.
Then, static type checker will take every subclass as type of base class.

.. code-block:: python

    from typing import TypeVar


    class Shape:
        def __init__(self, width: float, height: float) -> None:
            self.width = width
            self.height = height

        def area(self) -> float:
            return 0


    class Rectangle(Shape):
        def area(self) -> float:
            width: float = self.width
            height: float = self.height
            return width * height


    class Triangle(Shape):
        def area(self) -> float:
            width: float = self.width
            height: float = self.height
            return width * height / 2


    S = TypeVar("S", bound=Shape)


    def area(s: S) -> float:
        return s.area()


    r: Rectangle = Rectangle(1, 2)
    t: Triangle = Triangle(3, 4)
    i: int = 5566

    print(area(r))
    print(area(t))
    print(area(i))

output:

.. code-block:: bash

    $ mypy --strict foo.py
    foo.py:40: error: Value of type variable "S" of "area" cannot be "int"

@overload
----------

Sometimes, we use ``Union`` to infer that the return of a function has multiple
different types. However, type checker cannot distinguish which type do we want.
Therefore, following snippet shows that type checker cannot determine which type
is correct.

.. code-block:: python

    from typing import List, Union


    class Array(object):
        def __init__(self, arr: List[int]) -> None:
            self.arr = arr

        def __getitem__(self, i: Union[int, str]) -> Union[int, str]:
            if isinstance(i, int):
                return self.arr[i]
            if isinstance(i, str):
                return str(self.arr[int(i)])


    arr = Array([1, 2, 3, 4, 5])
    x:int = arr[1]
    y:str = arr["2"]

output:

.. code-block:: bash

    $ mypy --strict foo.py
    foo.py:16: error: Incompatible types in assignment (expression has type "Union[int, str]", variable has type "int")
    foo.py:17: error: Incompatible types in assignment (expression has type "Union[int, str]", variable has type "str")

Although we can use ``cast`` to solve the problem, it cannot avoid typo and ``cast`` is not safe.

.. code-block:: python

    from typing import  List, Union, cast


    class Array(object):
        def __init__(self, arr: List[int]) -> None:
            self.arr = arr

        def __getitem__(self, i: Union[int, str]) -> Union[int, str]:
            if isinstance(i, int):
                return self.arr[i]
            if isinstance(i, str):
                return str(self.arr[int(i)])


    arr = Array([1, 2, 3, 4, 5])
    x: int = cast(int, arr[1])
    y: str = cast(str, arr[2])  # typo. we want to assign arr["2"]

output:

.. code-block:: bash

    $ mypy --strict foo.py
    $ echo $?
    0

Using ``@overload`` can solve the problem. We can declare the return type explicitly.

.. code-block:: python

    from typing import Generic, List, Union, overload


    class Array(object):
        def __init__(self, arr: List[int]) -> None:
            self.arr = arr

        @overload
        def __getitem__(self, i: str) -> str:
            ...

        @overload
        def __getitem__(self, i: int) -> int:
            ...

        def __getitem__(self, i: Union[int, str]) -> Union[int, str]:
            if isinstance(i, int):
                return self.arr[i]
            if isinstance(i, str):
                return str(self.arr[int(i)])


    arr = Array([1, 2, 3, 4, 5])
    x: int = arr[1]
    y: str = arr["2"]

output:

.. code-block:: bash

    $ mypy --strict foo.py
    $ echo $?
    0

.. warning::

    Based on PEP 484, the ``@overload`` decorator just **for type checker only**, it does not implement
    the real overloading like c++/java. Thus, we have to implement one exactly non-``@overload``
    function. At the runtime, calling the ``@overload`` function will raise ``NotImplementedError``.

.. code-block:: python

    from typing import List, Union, overload


    class Array(object):
        def __init__(self, arr: List[int]) -> None:
            self.arr = arr

        @overload
        def __getitem__(self, i: Union[int, str]) -> Union[int, str]:
            if isinstance(i, int):
                return self.arr[i]
            if isinstance(i, str):
                return str(self.arr[int(i)])


    arr = Array([1, 2, 3, 4, 5])
    try:
        x: int = arr[1]
    except NotImplementedError as e:
        print("NotImplementedError")

output:

.. code-block:: bash

    $ python foo.py
    NotImplementedError

Stub Files
----------

Stub files just like header files which we usually use to define our interfaces in c/c++.
In python, we can define our interfaces in the same module directory or ``export MYPYPATH=${stubs}``

First, we need to create a stub file (interface file) for module.

.. code-block:: bash

    $ mkdir fib
    $ touch fib/__init__.py fib/__init__.pyi

Then, define the interface of the function in ``__init__.pyi`` and implement the module.

.. code-block:: python

    # fib/__init__.pyi
    def fib(n: int) -> int: ...

    # fib/__init__.py

    def fib(n):
        a, b = 0, 1
        for _ in range(n):
            b, a = a + b, b
        return a

Then, write a test.py for testing ``fib`` module.

.. code-block:: python

    # touch test.py
    import sys

    from pathlib import Path

    p = Path(__file__).parent / "fib"
    sys.path.append(str(p))

    from fib import fib

    print(fib(10.0))

output:

.. code-block:: bash

    $ mypy --strict test.py
    test.py:10: error: Argument 1 to "fib" has incompatible type "float"; expected "int"
