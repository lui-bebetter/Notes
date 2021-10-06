# ContextManager

## context manager protocol
* `__enter__(self)` 

* `__exit__(self, type, value, traceback)`

  `: return` `bool` 异常是否继续往上传

## The `with` statement

```
with EXPRESSION as TARGET:
    SUITE
```

is semantically equivalent to:

```
manager = (EXPRESSION)
enter = type(manager).__enter__
exit = type(manager).__exit__
value = enter(manager)
hit_except = False

try:
    TARGET = value
    SUITE
except:
    hit_except = True
    if not exit(manager, *sys.exc_info()):
        raise
finally:
    if not hit_except:
        exit(manager, None, None, None)
```
***本质是：保证只要`__enter__`成功执行，`__exit__`中的cleanup就一定会执行，但suite中触发的异常还是需要手动处理***

With more than one item, the context managers are processed as if multiple [`with`](https://docs.python.org/3.10/reference/compound_stmts.html#with) statements were nested:

```
with A() as a, B() as b:
    SUITE
```

is semantically equivalent to:

```
with A() as a:
    with B() as b:
        SUITE
```
## python lib
```
# contextlib.py
class GeneratorContextManager(object):
    """Helper for @contextmanager decorator."""

    def __init__(self, gen):
        self.gen = gen

    def __enter__(self):
        try:
            return self.gen.next()
        except StopIteration:
            raise RuntimeError("generator didn't yield")

    def __exit__(self, type, value, traceback):
        if type is None:
            try:
                self.gen.next()
            except StopIteration:
                return
            else:
                raise RuntimeError("generator didn't stop")
        else:
            if value is None:
                # Need to force instantiation so we can reliably
                # tell if we get the same exception back
                value = type()
            try:
                self.gen.throw(type, value, traceback)
                raise RuntimeError("generator didn't stop after throw()")
            except StopIteration, exc:
                # Suppress the exception *unless* it's the same exception that
                # was passed to throw().  This prevents a StopIteration
                # raised inside the "with" statement from being suppressed
                return exc is not value
            except:
                # only re-raise if it's *not* the exception that was
                # passed to throw(), because __exit__() must not raise
                # an exception unless __exit__() itself failed.  But throw()
                # has to raise the exception to signal propagation, so this
                # fixes the impedance mismatch between the throw() protocol
                # and the __exit__() protocol.
                #
                if sys.exc_info()[1] is not value:
                    raise


def contextmanager(func):
    """@contextmanager decorator.

    Typical usage:

        @contextmanager
        def some_generator(<arguments>):
            <setup>
            try:
                yield <value>
            finally:
                <cleanup>

    This makes this:

        with some_generator(<arguments>) as <variable>:
            <body>

    equivalent to this:

        <setup>
        try:
            <variable> = <value>
            <body>
        finally:
            <cleanup>

    """
    @wraps(func)
    def helper(*args, **kwds):
        return GeneratorContextManager(func(*args, **kwds))
    return helper
```
## example

```
@contextmanager
def locked(lock):
    lock.acquire()
    try:
        yield
    finally:
        lock.release()
```
Used as follows:
```
with locked(myLock):
    # Code here executes with myLock held.  The lock is
    # guaranteed to be released when the block is left (even
    # if via return or by an uncaught exception).
```

## 注解
 @contextmanager
 def some_generator(<arguments>):
     <setup>
     try:
         yield <value>
     finally:
         <cleanup>
这里`try... finally...` 处理的是 with 中block可能抛出的错误，`__enter__` ,` __exit__`中的`try...finally...`处理的是<setup> 和<cleanup>中可能抛出的错误



## context manager 最终效果，类似切片编程