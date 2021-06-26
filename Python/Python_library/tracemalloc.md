# tracemalloc

## Functions

- `tracemalloc.start(nframe: int=1)`

- `tracemalloc.take_snapshot()`

  return: a new `Snapshot` instance
  
- `tracemalloc.stop()`

- `tracemalloc.clear_traces()`

- `tracemalloc.is_tracing()`

- `tracemalloc.get_traced_memory()`:

  - `return`: the current size and peak size of memory blocks traced by.

- `tracemalloc.get_object_traceback(obj)`: 

  get the traceback where the Python object obj was allocated.

  - `return`: `tracemalloc.Traceback`

# Classes

- class `tracemalloc.Snapshot`

  - Fields:

    - `traces` :  sequence of  `Trace` instance, 无序
    - `traceback_limit`:  nframe
  
  - Functions:
  
    - `statistics(key_type: str, cumulatice: bool=False)`:
    
      ​    traces按key_type分组后再排序
    
      - key_type: 'filename', 'lineno', 'traceback'
    
      - cumulative: 一个trace所有frame分配的内存累加
      
      - ` return`: sequence of `Statistic` 
  
    - `compare_to(other_snapshot, key_type, cumulative)`
      
      - `return`: sorted list of `StatisticDiff`
      
    - `dump(filename)`: write to file.
    
    - `filter_traces(filters)`: return a new `Snapshot` instance with a filtered `traces` sequences.
  
- class `tracemalloc.Trace`
  
  - `size` :  int ,  一个memory block 的大小
  - `traceback`: 分配该memory block 的堆栈信息
  - `domain`: 该memory block的address space type
  
- class `tracemalloc.Statistic`
  
  - `count`:  group后，该`Statistic`有#个memory block.
  
  - `size`: total size of memory blocks in bytes.
  
  - `traceback`: `Traceback` instance.
  
- class `tracemalloc.StatisticDiff`
  
  - `count`: # memory block in new snapshot.
  - `size`: total size of memory blocks in new snapshot.
  - `count_diff`: #memory block in new - # memory block in old .
  - `size_diff`: total size in new - total size in old
  
  - `traceback`: `Traceback` instance.
  
- class `tracemalloc.Traceback`

  sequence of `Frame` instances from oldest to recent

  - `total_nframe`: #frames original.
  
  - `format(limit=None, most_recent_first=False)`

# Estimate Memory of Python Object Use `sys.getsizeof`

  

```
from __future__ import print_function
from sys import getsizeof, stderr
from itertools import chain
from collections import deque
try:
    from reprlib import repr
except ImportError:
    pass

def total_size(o, handlers={}, verbose=False):
    """ Returns the approximate memory footprint an object and all of its contents.

    Automatically finds the contents of the following builtin containers and
    their subclasses:  tuple, list, deque, dict, set and frozenset.
    To search other containers, add handlers to iterate over their contents:

        handlers = {SomeContainerClass: iter,
                    OtherContainerClass: OtherContainerClass.get_elements}

    """
    dict_handler = lambda d: chain.from_iterable(d.items())
    all_handlers = {tuple: iter,
                    list: iter,
                    deque: iter,
                    dict: dict_handler,
                    set: iter,
                    frozenset: iter,
                   }
    all_handlers.update(handlers)     # user handlers take precedence
    seen = set()                      # track which object id's have already been seen
    default_size = getsizeof(0)       # estimate sizeof object without __sizeof__

    def sizeof(o):
        if id(o) in seen:       # do not double count the same object
            return 0
        seen.add(id(o))
        s = getsizeof(o, default_size)

        if verbose:
            print(s, type(o), repr(o), file=stderr)

        for typ, handler in all_handlers.items():
            if isinstance(o, typ):
                s += sum(map(sizeof, handler(o)))
                break
        return s

    return sizeof(o)


##### Example call #####

if __name__ == '__main__':
    d = dict(a=1, b=2, c=3, d=[4,5,6,7], e='a string of chars')
    print(total_size(d, verbose=True))
```

