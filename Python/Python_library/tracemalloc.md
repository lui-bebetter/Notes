# tracemalloc

## Functions

- `tracemalloc.start(nframe: int=1)`

- `tracemalloc.take_snapshot()`

  return: a new `Snapshot` instance

# Classes

- `class tracemalloc.Snapshot`

  - Fields:

    - `traces` :  sequence of  `Trace` instance, 无序
- `traceback_limit`:  nframe
    
 - Functions:
  
   - `statistics(key_type: str, cumulatice: bool=False)`: traces按key_type分组后再排序
  
      key_type: 'filename', 'lineno', 'traceback'
  
        cumulative: 一个trace所有frame分配的内存累加
  
       `return`: sequence of `Statistic` 
  
    
  
  

