.. meta::
    :description lang=en: Collect useful snippets of Python generator
    :keywords: Python, Python3, Python Generator, Python Generator Cheat Sheet

=========
Generator
=========

.. contents:: Table of Contents
    :backlinks: none


Glossary of Generator
---------------------

.. code-block:: python

    # generator function

    >>> def gen_func():
    ...     yield 5566
    ...
    >>> gen_func
    <function gen_func at 0x1019273a>

    # generator
    #
    # calling the generator function returns a generator

    >>> g = gen_func()
    >>> g
    <generator object gen_func at 0x101238fd>
    >>> next(g)
    5566
    >>> next(g)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    StopIteration

    # generator expression
    #
    # generator expression evaluating directly to a generator

    >>> g = (x for x in range(2))
    >>> g
    <generator object <genexpr> at 0x10a9c191>
    >>> next(g)
    0
    >>> next(g)
    1
    >>> next(g)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    StopIteration

Produce value via generator
---------------------------

.. code-block:: python

    >>> from __future__ import print_function
    >>> def prime(n):
    ...     p = 2
    ...     while n > 0:
    ...         for x in range(2, p):
    ...             if p % x == 0:
    ...                 break
    ...         else:
    ...             yield p
    ...             n -= 1
    ...         p += 1
    ...
    >>> p = prime(3)
    >>> next(p)
    2
    >>> next(p)
    3
    >>> next(p)
    5
    >>> next(p)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    StopIteration
    >>> for x in prime(5):
    ...     print(x, end=" ")
    ...
    2 3 5 7 11 >>>


Unpacking Generators
----------------------

.. code-block:: python

    # PEP 448

    # unpacking inside a list

    >>> g1 = (x for x in range(3))
    >>> g2 = (x**2 for x in range(2))
    >>> [1, *g1, 2, *g2]
    [1, 0, 1, 2, 2, 0, 1]
    >>> # equal to
    >>> g1 = (x for x in range(3))
    >>> g2 = (x**2 for x in range(2))
    >>> [1] + list(g1) + [2] + list(g2)
    [1, 0, 1, 2, 2, 0, 1]

    # unpacking inside a set

    >>> g = (x for x in [5, 5, 6, 6])
    >>> {*g}
    {5, 6}

    # unpacking to variables

    >>> g = (x for x in range(3))
    >>> a, b, c = g
    >>> print(a, b, c)
    0 1 2
    >>> g = (x for x in range(6))
    >>> a, b, *c, d = g
    >>> print(a, b, d)
    0 1 5
    >>> print(c)
    [2, 3, 4]

    # unpacking inside a function

    >>> print(*(x for x in range(3)))
    0 1 2


Implement Iterable object via generator
---------------------------------------

.. code-block:: python

    >>> from __future__ import print_function
    >>> class Count(object):
    ...     def __init__(self, n):
    ...         self._n = n
    ...     def __iter__(self):
    ...         n = self._n
    ...         while n > 0:
    ...             yield n
    ...             n -= 1
    ...     def __reversed__(self):
    ...         n = 1
    ...         while n <= self._n:
    ...             yield n
    ...             n += 1
    ...
    >>> for x in Count(5):
    ...     print(x, end=" ")
    ...
    5 4 3 2 1 >>>
    >>> for x in reversed(Count(5)):
    ...     print(x, end=" ")
    ...
    1 2 3 4 5 >>>

Send message to generator
-------------------------

.. code-block:: python

    >>> def spam():
    ...     msg = yield
    ...     print("Message:", msg)
    ...
    >>> try:
    ...     g = spam()
    ...     # start generator
    ...     next(g)
    ...     # send message to generator
    ...     g.send("Hello World!")
    ... except StopIteration:
    ...     pass
    ...
    Message: Hello World!

``yield from`` expression
---------------------------

.. code-block:: python

    # delegating gen do nothing(pipe)
    >>> def subgen():
    ...     try:
    ...         yield 9527
    ...     except ValueError:
    ...         print("get value error")
    ...
    >>> def delegating_gen():
    ...     yield from subgen()
    ...
    >>> g = delegating_gen()
    >>> try:
    ...     next(g)
    ...     g.throw(ValueError)
    ... except StopIteration:
    ...     print("gen stop")
    ...
    9527
    get value error
    gen stop

    # yield from + yield from
    >>> import inspect
    >>> def subgen():
    ...     yield from range(5)
    ...
    >>> def delegating_gen():
    ...     yield from subgen()
    ...
    >>> g = delegating_gen()
    >>> inspect.getgeneratorstate(g)
    'GEN_CREATED'
    >>> next(g)
    0
    >>> inspect.getgeneratorstate(g)
    'GEN_SUSPENDED'
    >>> g.close()
    >>> inspect.getgeneratorstate(g)
    'GEN_CLOSED'

yield (from) EXPR return RES
----------------------------

.. code-block:: python

    >>> def average():
    ...     total = .0
    ...     count = 0
    ...     avg = None
    ...     while True:
    ...         val = yield
    ...         if not val:
    ...             break
    ...         total += val
    ...         count += 1
    ...         avg = total / count
    ...     return avg
    ...
    >>> g = average()
    >>> next(g) # start gen
    >>> g.send(3)
    >>> g.send(5)
    >>> try:
    ...     g.send(None)
    ... except StopIteration as e:
    ...     ret = e.value
    ...
    >>> ret
    4.0

    # yield from EXP return RES
    >>> def subgen():
    ...     yield 9527
    ...
    >>> def delegating_gen():
    ...     yield from subgen()
    ...     return 5566
    ...
    >>> try:
    ...     g = delegating_gen()
    ...     next(g)
    ...     next(g)
    ... except StopIteration as _e:
    ...     print(_e.value)
    ...
    9527
    5566

Generate sequences
------------------

.. code-block:: python

    # get a list via generator

    >>> def chain():
    ...     for x in 'ab':
    ...         yield x
    ...     for x in range(3):
    ...         yield x
    ...
    >>> a = list(chain())
    >>> a
    ['a', 'b', 0, 1, 2]

    # equivalent to

    >>> def chain():
    ...     yield from 'ab'
    ...     yield from range(3)
    ...
    >>> a = list(chain())
    >>> a
    ['a', 'b', 0, 1, 2]

What ``RES = yield from EXP`` actually do?
--------------------------------------------

.. code-block:: python

    # ref: pep380
    >>> def subgen():
    ...     for x in range(3):
    ...         yield x
    ...
    >>> EXP = subgen()
    >>> def delegating_gen():
    ...     _i = iter(EXP)
    ...     try:
    ...         _y = next(_i)
    ...     except StopIteration as _e:
    ...         RES = _e.value
    ...     else:
    ...         while True:
    ...             _s = yield _y
    ...             try:
    ...                 _y = _i.send(_s)
    ...             except StopIteration as _e:
    ...                 RES = _e.value
    ...                 break
    ...
    >>> g = delegating_gen()
    >>> next(g)
    0
    >>> next(g)
    1
    >>> next(g)
    2

    # equivalent to
    >>> EXP = subgen()
    >>> def delegating_gen():
    ...     RES = yield from EXP
    ...
    >>> g = delegating_gen()
    >>> next(g)
    0
    >>> next(g)
    1


``for _ in gen()`` simulate ``yield from``
-------------------------------------------

.. code-block:: python

    >>> def subgen(n):
    ...     for x in range(n): yield x
    ...
    >>> def gen(n):
    ...     yield from subgen(n)
    ...
    >>> g = gen(3)
    >>> next(g)
    0
    >>> next(g)
    1

    # equal to

    >>> def gen(n):
    ...     for x in subgen(n): yield x
    ...
    >>> g = gen(3)
    >>> next(g)
    0
    >>> next(g)
    1


Check generator type
--------------------

.. code-block:: python

    >>> from types import GeneratorType
    >>> def gen_func():
    ...     yield 5566
    ...
    >>> g = gen_func()
    >>> isinstance(g, GeneratorType)
    True
    >>> isinstance(123, GeneratorType)
    False

Check Generator State
---------------------

.. code-block:: python

    >>> import inspect
    >>> def gen_func():
    ...     yield 9527
    ...
    >>> g = gen_func()
    >>> inspect.getgeneratorstate(g)
    'GEN_CREATED'
    >>> next(g)
    9527
    >>> inspect.getgeneratorstate(g)
    'GEN_SUSPENDED'
    >>> g.close()
    >>> inspect.getgeneratorstate(g)
    'GEN_CLOSED'


Simple compiler
-----------------

.. code-block:: python

    # David Beazley - Generators: The Final Frontier

    import re
    import types
    from collections import namedtuple

    tokens = [
        r'(?P<NUMBER>\d+)',
        r'(?P<PLUS>\+)',
        r'(?P<MINUS>-)',
        r'(?P<TIMES>\*)',
        r'(?P<DIVIDE>/)',
        r'(?P<WS>\s+)']

    Token = namedtuple('Token', ['type', 'value'])
    lex = re.compile('|'.join(tokens))

    def tokenize(text):
        scan = lex.scanner(text)
        gen = (Token(m.lastgroup, m.group())
                for m in iter(scan.match, None) if m.lastgroup != 'WS')
        return gen


    class Node:
        _fields = []
        def __init__(self, *args):
            for attr, value in zip(self._fields, args):
                setattr(self, attr, value)

    class Number(Node):
        _fields = ['value']

    class BinOp(Node):
        _fields = ['op', 'left', 'right']

    def parse(toks):
        lookahead, current = next(toks, None), None

        def accept(*toktypes):
            nonlocal lookahead, current
            if lookahead and lookahead.type in toktypes:
                current, lookahead = lookahead, next(toks, None)
                return True

        def expr():
            left = term()
            while accept('PLUS', 'MINUS'):
                left = BinOp(current.value, left)
                left.right = term()
            return left

        def term():
            left = factor()
            while accept('TIMES', 'DIVIDE'):
                left = BinOp(current.value, left)
                left.right = factor()
            return left

        def factor():
            if accept('NUMBER'):
                return Number(int(current.value))
            else:
                raise SyntaxError()
        return expr()


    class NodeVisitor:
        def visit(self, node):
            stack = [self.genvisit(node)]
            ret = None
            while stack:
                try:
                    node = stack[-1].send(ret)
                    stack.append(self.genvisit(node))
                    ret = None
                except StopIteration as e:
                    stack.pop()
                    ret = e.value
            return ret

        def genvisit(self, node):
            ret = getattr(self, 'visit_' + type(node).__name__)(node)
            if isinstance(ret, types.GeneratorType):
                ret = yield from ret
            return ret

    class Evaluator(NodeVisitor):
        def visit_Number(self, node):
            return node.value

        def visit_BinOp(self, node):
            leftval = yield node.left
            rightval = yield node.right
            if node.op == '+':
                return leftval + rightval
            elif node.op == '-':
                return leftval - rightval
            elif node.op == '*':
                return leftval * rightval
            elif node.op == '/':
                return leftval / rightval

    def evaluate(exp):
        toks = tokenize(exp)
        tree = parse(toks)
        return Evaluator().visit(tree)


    exp = '2 * 3 + 5 / 2'
    print(evaluate(exp))
    exp = '+'.join([str(x) for x in range(10000)])
    print(evaluate(exp))

output:

.. code-block:: bash

    python3 compiler.py
    8.5
    49995000


Context manager and generator
-----------------------------

.. code-block:: python

    >>> import contextlib
    >>> @contextlib.contextmanager
    ... def mylist():
    ...     try:
    ...         l = [1, 2, 3, 4, 5]
    ...         yield l
    ...     finally:
    ...         print("exit scope")
    ...
    >>> with mylist() as l:
    ...   print(l)
    ...
    [1, 2, 3, 4, 5]
    exit scope

What ``@contextmanager`` actually doing?
------------------------------------------

.. code-block:: python

    # ref: PyCon 2014 - David Beazley
    # define a context manager class

    class GeneratorCM(object):

        def __init__(self, gen):
            self._gen = gen

        def __enter__(self):
            return next(self._gen)

        def __exit__(self, *exc_info):
            try:
                if exc_info[0] is None:
                    next(self._gen)
                else:
                    self._gen.throw(*exc_info)
                raise RuntimeError
            except StopIteration:
                return True
            except:
                raise

    # define a decorator
    def contextmanager(func):
        def run(*a, **k):
            return GeneratorCM(func(*a, **k))
        return run

    # example of context manager
    @contextmanager
    def mylist():
        try:
            l = [1, 2, 3, 4, 5]
            yield l
        finally:
            print("exit scope")

    with mylist() as l:
        print(l)

output:

.. code-block:: console

    $ python ctx.py
    [1, 2, 3, 4, 5]
    exit scope


profile code block
-------------------

.. code-block:: python

    >>> import time
    >>> @contextmanager
    ... def profile(msg):
    ...     try:
    ...         s = time.time()
    ...         yield
    ...     finally:
    ...         e = time.time()
    ...         print('{} cost time: {}'.format(msg, e - s))
    ...
    >>> with profile('block1'):
    ...     time.sleep(1)
    ...
    block1 cost time: 1.00105595589
    >>> with profile('block2'):
    ...     time.sleep(3)
    ...
    block2 cost time: 3.00104284286


``yield from`` and ``__iter__``
--------------------------------

.. code-block:: python

    >>> class FakeGen:
    ...     def __iter__(self):
    ...         n = 0
    ...         while True:
    ...             yield n
    ...             n += 1
    ...     def __reversed__(self):
    ...         n = 9527
    ...         while True:
    ...            yield n
    ...            n -= 1
    ...
    >>> def spam():
    ...     yield from FakeGen()
    ...
    >>> s = spam()
    >>> next(s)
    0
    >>> next(s)
    1
    >>> next(s)
    2
    >>> next(s)
    3
    >>> def reversed_spam():
    ...     yield from reversed(FakeGen())
    ...
    >>> g = reversed_spam()
    >>> next(g)
    9527
    >>> next(g)
    9526
    >>> next(g)
    9525

``yield from == await`` expression
------------------------------------

.. code-block:: python

    # "await" include in pyhton3.5
    import asyncio
    import socket

    # set socket and event loop
    loop = asyncio.get_event_loop()
    host = 'localhost'
    port = 5566
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM,0)
    sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR,1)
    sock.setblocking(False)
    sock.bind((host, port))
    sock.listen(10)

    @asyncio.coroutine
    def echo_server():
        while True:
            conn, addr = yield from loop.sock_accept(sock)
            loop.create_task(handler(conn))

    @asyncio.coroutine
    def handler(conn):
        while True:
            msg = yield from loop.sock_recv(conn, 1024)
            if not msg:
                break
            yield from loop.sock_sendall(conn, msg)
        conn.close()

    # equal to
    async def echo_server():
        while True:
            conn, addr = await loop.sock_accept(sock)
            loop.create_task(handler(conn))

    async def handler(conn):
        while True:
            msg = await loop.sock_recv(conn, 1024)
            if not msg:
                break
            await loop.sock_sendall(conn, msg)
        conn.close()

    loop.create_task(echo_server())
    loop.run_forever()

output: (bash 1)

.. code-block:: console

    $ nc localhost 5566
    Hello
    Hello


output: (bash 2)

.. code-block:: console

    $ nc localhost 5566
    World
    World


Closure in Python - using generator
-----------------------------------

.. code-block:: python

    # nonlocal version
    >>> def closure():
    ...     x = 5566
    ...     def inner_func():
    ...         nonlocal x
    ...         x += 1
    ...         return x
    ...     return inner_func
    ...
    >>> c = closure()
    >>> c()
    5567
    >>> c()
    5568
    >>> c()
    5569

    # class version
    >>> class Closure:
    ...     def __init__(self):
    ...         self._x = 5566
    ...     def __call__(self):
    ...         self._x += 1
    ...         return self._x
    ...
    >>> c = Closure()
    >>> c()
    5567
    >>> c()
    5568
    >>> c()
    5569

    # generator version (best)
    >>> def closure_gen():
    ...     x = 5566
    ...     while True:
    ...         x += 1
    ...         yield x
    ...
    >>> g = closure_gen()
    >>> next(g)
    5567
    >>> next(g)
    5568
    >>> next(g)
    5569


Implement a simple scheduler
----------------------------

.. code-block:: python

    # idea: write an event loop(scheduler)
    >>> def fib(n):
    ...     if n <= 2:
    ...         return 1
    ...     return fib(n-1) + fib(n-2)
    ...
    >>> def g_fib(n):
    ...     for x in range(1, n + 1):
    ...         yield fib(x)
    ...
    >>> from collections import deque
    >>> t = [g_fib(3), g_fib(5)]
    >>> q = deque()
    >>> q.extend(t)
    >>> def run():
    ...     while q:
    ...         try:
    ...             t = q.popleft()
    ...             print(next(t))
    ...             q.append(t)
    ...         except StopIteration:
    ...             print("Task done")
    ...
    >>> run()
    1
    1
    1
    1
    2
    2
    Task done
    3
    5
    Task done

Simple round-robin with blocking
--------------------------------

.. code-block:: python

    # ref: PyCon 2015 - David Beazley
    # skill: using task and wait queue

    from collections import deque
    from select import select
    import socket

    tasks = deque()
    w_read = {}
    w_send = {}

    def run():
        while any([tasks, w_read, w_send]):
            while not tasks:
                # polling tasks
                can_r, can_s,_ = select(w_read, w_send, [])
                for _r in can_r:
                    tasks.append(w_read.pop(_r))
                for _w in can_s:
                    tasks.append(w_send.pop(_w))
            try:
                task = tasks.popleft()
                why,what = next(task)
                if why == 'recv':
                    w_read[what] = task
                elif why == 'send':
                    w_send[what] = task
                else:
                    raise RuntimeError
            except StopIteration:
                pass

    def server():
        host = ('localhost', 5566)
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        sock.bind(host)
        sock.listen(5)
        while True:
            # tell scheduler want block
            yield 'recv', sock
            conn,addr = sock.accept()
            tasks.append(client_handler(conn))

    def client_handler(conn):
        while True:
            # tell scheduler want block
            yield 'recv', conn
            msg = conn.recv(1024)
            if not msg:
                break
            # tell scheduler want block
            yield 'send', conn
            conn.send(msg)
        conn.close()

    tasks.append(server())
    run()

simple round-robin with blocking and non-blocking
-------------------------------------------------

.. code-block:: python

    # this method will cause blocking hunger
    from collections import deque
    from select import select
    import socket

    tasks = deque()
    w_read = {}
    w_send = {}

    def run():
        while any([tasks, w_read, w_send]):
            while not tasks:
                # polling tasks
                can_r,can_s,_ = select(w_read, w_send, [])
                for _r in can_r:
                    tasks.append(w_read.pop(_r))
                for _w in can_s:
                    tasks.append(w_send.pop(_w))
            try:
                task = tasks.popleft()
                why,what = next(task)
                if why == 'recv':
                    w_read[what] = task
                elif why == 'send':
                    w_send[what] = task
                elif why == 'continue':
                    print(what)
                    tasks.append(task)
                else:
                    raise RuntimeError
            except StopIteration:
                pass

    def fib(n):
        if n <= 2:
            return 1
        return fib(n-1) + fib(n-2)

    def g_fib(n):
        for x in range(1, n + 1):
            yield 'continue', fib(x)

    tasks.append(g_fib(15))

    def server():
        host = ('localhost', 5566)
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        sock.bind(host)
        sock.listen(5)
        while True:
            yield 'recv', sock
            conn,addr = sock.accept()
            tasks.append(client_handler(conn))

    def client_handler(conn):
        while True:
            yield 'recv', conn
            msg = conn.recv(1024)
            if not msg:
                break
            yield 'send', conn
            conn.send(msg)
        conn.close()

    tasks.append(server())
    run()


Asynchronous Generators
------------------------

.. code-block:: python

    # PEP 525
    #
    # Need python-3.6 or above

    >>> import asyncio
    >>> async def slow_gen(n, t):
    ...     for x in range(n):
    ...         await asyncio.sleep(t)
    ...         yield x
    ...
    >>> async def task(n):
    ...     async for x in slow_gen(n, 0.1):
    ...         print(x)
    ...
    >>> loop = asyncio.get_event_loop()
    >>> loop.run_until_complete(task(3))
    0
    1
    2

Asynchronous generators can have ``try..finally`` blocks
---------------------------------------------------------

.. code-block:: python

    # Need python-3.6 or above

    >>> import asyncio
    >>> async def agen(t):
    ...     try:
    ...         await asyncio.sleep(t)
    ...         yield 1 / 0
    ...     finally:
    ...         print("finally part")
    ...
    >>> async def main(t=1):
    ...     try:
    ...         g = agen(t)
    ...         await g.__anext__()
    ...     except Exception as e:
    ...         print(repr(e))
    ...
    >>> loop = asyncio.get_event_loop()
    >>> loop.run_until_complete(main(1))
    finally part
    ZeroDivisionError('division by zero',)


send value and throw exception into async generator
----------------------------------------------------

.. code-block:: python

    # Need python-3.6 or above

    >>> import asyncio
    >>> async def agen(n, t=0.1):
    ...     try:
    ...         for x in range(n):
    ...             await asyncio.sleep(t)
    ...             val = yield x
    ...             print(f'get val: {val}')
    ...     except RuntimeError as e:
    ...         await asyncio.sleep(t)
    ...         yield repr(e)
    ...
    >>> async def main(n):
    ...     g = agen(n)
    ...     ret = await g.asend(None) + await g.asend('foo')
    ...     print(ret)
    ...     ret = await g.athrow(RuntimeError('Get RuntimeError'))
    ...     print(ret)
    ...
    >>> loop = asyncio.get_event_loop()
    >>> loop.run_until_complete(main(5))
    get val: foo
    1
    RuntimeError('Get RuntimeError',)


Simple async round-robin
---------------------------

.. code-block:: python

    # Need python-3.6 or above

    >>> import asyncio
    >>> from collections import deque
    >>> async def agen(n, t=0.1):
    ...     for x in range(n):
    ...         await asyncio.sleep(t)
    ...         yield x
    ...
    >>> async def main():
    ...     q = deque([agen(3), agen(5)])
    ...     while q:
    ...         try:
    ...             g = q.popleft()
    ...             ret = await g.__anext__()
    ...             print(ret)
    ...             q.append(g)
    ...         except StopAsyncIteration:
    ...             pass
    ...
    >>> loop.run_until_complete(main())
    0
    0
    1
    1
    2
    2
    3
    4


Async generator get better performance than async iterator
------------------------------------------------------------

.. code-block:: python

    # Need python-3.6 or above

    >>> import time
    >>> import asyncio
    >>> class AsyncIter:
    ...     def __init__(self, n):
    ...         self._n = n
    ...     def __aiter__(self):
    ...         return self
    ...     async def __anext__(self):
    ...         ret = self._n
    ...         if self._n == 0:
    ...             raise StopAsyncIteration
    ...         self._n -= 1
    ...         return ret
    ...
    >>> async def agen(n):
    ...     for i in range(n):
    ...         yield i
    ...
    >>> async def task_agen(n):
    ...     s = time.time()
    ...     async for _ in agen(n): pass
    ...     cost = time.time() - s
    ...     print(f"agen cost time: {cost}")
    ...
    >>> async def task_aiter(n):
    ...     s = time.time()
    ...     async for _ in AsyncIter(n): pass
    ...     cost = time.time() - s
    ...     print(f"aiter cost time: {cost}")
    ...
    >>> n = 10 ** 7
    >>> loop = asyncio.get_event_loop()
    >>> loop.run_until_complete(task_agen(n))
    agen cost time: 1.2698817253112793
    >>> loop.run_until_complete(task_aiter(n))
    aiter cost time: 4.168368101119995


Asynchronous Comprehensions
---------------------------

.. code-block:: python

    # PEP 530
    #
    # Need python-3.6 or above

    >>> import asyncio
    >>> async def agen(n, t):
    ...     for x in range(n):
    ...         await asyncio.sleep(t)
    ...         yield x
    >>> async def main():
    ...     ret = [x  async for x in agen(5, 0.1)]
    ...     print(*ret)
    ...     ret = [x async for x in agen(5, 0.1) if x < 3]
    ...     print(*ret)
    ...     ret = [x if x < 3 else -1 async for x in agen(5, 0.1)]
    ...     print(*ret)
    ...     ret = {f'{x}': x async for x in agen(5, 0.1)}
    ...     print(ret)

    >>> loop.run_until_complete(main())
    0 1 2 3 4
    0 1 2
    0 1 2 -1 -1
    {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4}

    # await in Comprehensions

    >>> async def foo(t):
    ...     await asyncio.sleep(t)
    ...     return "foo"
    ...
    >>> async def bar(t):
    ...     await asyncio.sleep(t)
    ...     return "bar"
    ...
    >>> async def baz(t):
    ...     await asyncio.sleep(t)
    ...     return "baz"
    ...
    >>> async def gen(*f, t=0.1):
    ...     for x in f:
    ...         await asyncio.sleep(t)
    ...         yield x
    ...
    >>> async def await_simple_task():
    ...     ret = [await f(0.1) for f in [foo, bar]]
    ...     print(ret)
    ...     ret = {await f(0.1) for f in [foo, bar]}
    ...     print(ret)
    ...     ret = {f.__name__: await f(0.1) for f in [foo, bar]}
    ...     print(ret)
    ...
    >>> async def await_other_task():
    ...     ret = [await f(0.1) for f in [foo, bar] if await baz(1)]
    ...     print(ret)
    ...     ret = {await f(0.1) for f in [foo, bar] if await baz(1)}
    ...     print(ret)
    ...     ret = {f.__name__: await f(0.1) for f in [foo, bar] if await baz(1)}
    ...     print(ret)
    ...
    >>> async def await_aiter_task():
    ...     ret = [await f(0.1) async for f in gen(foo, bar)]
    ...     print(ret)
    ...     ret = {await f(0.1) async for f in gen(foo, bar)}
    ...     print(ret)
    ...     ret = {f.__name__: await f(0.1) async for f in gen(foo, bar)}
    ...     print(ret)
    ...     ret = [await f(0.1) async for f in gen(foo, bar) if await baz(1)]
    ...     print(ret)
    ...     ret = {await f(0.1) async for f in gen(foo, bar) if await baz(1)}
    ...     print(ret)
    ...     ret = {f.__name__: await f(0.1) async for f in gen(foo, bar) if await baz(1)}
    ...
    >>> import asyncio
    >>> asyncio.get_event_loop()
    >>> loop.run_until_complete(await_simple_task())
    ['foo', 'bar']
    {'bar', 'foo'}
    {'foo': 'foo', 'bar': 'bar'}
    >>> loop.run_until_complete(await_other_task())
    ['foo', 'bar']
    {'bar', 'foo'}
    {'foo': 'foo', 'bar': 'bar'}
    >>> loop.run_until_complete(await_gen_task())
    ['foo', 'bar']
    {'bar', 'foo'}
    {'foo': 'foo', 'bar': 'bar'}
    ['foo', 'bar']
    {'bar', 'foo'}
    {'foo': 'foo', 'bar': 'bar'}
