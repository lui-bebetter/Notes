# Python object serialization

                                                                          pickle marshal json

## ***PICKLE*** [1][]

- *binary serialization format*
- *Python-specific*
- *almost all Python types*
- *no safety guarantee*

### What can be pickled and unpickled?
* None, True, and False
* integers, long integers, floating point numbers, complex numbers
* normal and Unicode strings
* tuples, lists, sets, and dictionaries containing only picklable objects
* functions defined at the top level of a module  ---> `<module>.<func_name>`
* built-in functions defined at the top level of a module   
* classes that are defined at the top level of a module   ---> `<module>.<class_name>`
* instances of such classes whose `__dict__` or the result of calling `__getstate__()` is picklable.

### Illustration
```
def save(obj):
    return (obj.__class__, obj.__dict__)

def load(cls, attributes):
    obj = cls.__new__(cls)
    obj.__dict__.update(attributes)
    return obj
```
unpickling will import any class that it finds in the pickle data

### protocol
`object.__getstate__()`
`object.__setstate__(state)`
`object.__getinitargs__()`
`object.__getnewargs__()`

### Usage
`pickle.dump(obj, file[, protocol])`
`pickle.load(file)`
`pickle.dumps(obj[, protocol])`
`pickle.loads(string)`
```
class pickle.Pickler(file[, protocol])
	dump(obj)
	clear_memo()
	persistent_id(obj)
```
```
class pickle.Unpickler(file[, protocol])
	load(obj)
	noload()
	persistent_load(pid)
	find_class(module, name) # Restricting Globals
```

### Safety
By default, unpickling will import any class or function that it finds in the pickle data. For many applications, this behaviour is unacceptable as it permits the unpickler to import and invoke arbitrary code. Just consider what this hand-crafted pickle data stream does when loaded:
`pickle.loads(b"cos\nsystem\n(S'echo hello world'\ntR.")`

### Example
```
#!/usr/local/bin/python

class TextReader:
    """Print and number lines in a text file."""
    def __init__(self, file):
        self.file = file
        self.fh = open(file)
        self.lineno = 0

    def readline(self):
        self.lineno = self.lineno + 1
        line = self.fh.readline()
        if not line:
            return None
        if line.endswith("\n"):
            line = line[:-1]
        return "%d: %s" % (self.lineno, line)

    def __getstate__(self):
        odict = self.__dict__.copy() # copy the dict since we change it
        del odict['fh']              # remove filehandle entry
        return odict

    def __setstate__(self, dict):
        fh = open(dict['file'])      # reopen file
        count = dict['lineno']       # read from file...
        while count:                 # until line count is restored
            fh.readline()
            count = count - 1
        self.__dict__.update(dict)   # update attributes
        self.fh = fh                 # save the file object
```

## ***JSON*** [2][]

- *text serialization format*
- *only built-in types, no custom classes*
- *safety guarantee*

### conversion table

|      Python      |           JSON           |      Python      |
| :--------------: | :----------------------: | :--------------: |
|       True       |           true           |       True       |
|      False       |          false           |      False       |
|       None       |           null           |       None       |
| int, long, float |    number(int, real)     | int, long, float |
|   str, unicode   |          string          |     unicode      |
|   list, tuple    |          array           |       list       |
|       dict       |          object          |       dict       |
|  nan, inf, -inf  | NaN, Infinity, -Infinity |  nan, inf, -inf  |

### Usage
```
json.dump(obj, fp,
	# dict keys 默认(str,unicode,int,long,float,bool,None)，
	# 对于非默认key类型，False: raise TypeError, True: skip
	skipkeys=False,
	ensure_ascii=True,
	check_circular=True, # circular reference check for container types
	allow_nan=True,  # False: rasie ValueError for (nan, inf, -inf)
	cls=None,	# subclass of JSONEncoder
	indent=None,
	separators=None, # (item_separator, key_separator), default:(', ', ': ')
	encoding='utf-8',
	default=None, # default(obj) -> JSON serializable object
	sort_keys=False, # True: sort output json dict by key
	**kw
)

json.dumps(obj,
	skipkeys=False,
	ensure_ascii=True,
	check_circular=True,
	allow_nan=True,
	cls=None,
	indent=None,
	separators=None, 
	encoding='utf-8',
	default=None, 
	sort_keys=False, 
	**kw
)
```

```
json.load(fp,
	encoding='utf-8',
	cls=None,	# subclass of JSONDecoder
	object_hook=None,	# object_hook(dict) -> real object
	parse_float=None,	# default: float(str)
	parse_int=None,		# default: int(str)
	parse_constant=None,  # parse (NaN, Infinity, -Infinity)
	object_pairs_hook=None, # object_pairs_hook(ordered list of pairs) -> object
	**kw
)

json.load(s,
	encoding='utf-8',
	cls=None,
	object_hook=None,	
	parse_float=None,	
	parse_int=None,		
	parse_constant=None,  
	object_pairs_hook=None, 
	**kw
)
```

```
class json.JSONEncoder(skipkeys, ensure_ascii, check_circular, allow_nan, sort_keys, indent, separators, encoding, default)
	default(o)
	encode(o)
	iterencode(o)
	
class json.JSONDecoder(encoding, object_hook, parse_flaot, parse_int,
parse_constant, strict, object_pairs_hook)
	decode(s)
	raw_decode(s)
```

### Example
```
>>> import json
>>> def as_complex(dct):
...     if '__complex__' in dct:
...         return complex(dct['real'], dct['imag'])
...     return dct
...
>>> json.loads('{"__complex__": true, "real": 1, "imag": 2}',
...     object_hook=as_complex)
(1+2j)
>>> import decimal
>>> json.loads('1.1', parse_float=decimal.Decimal)
Decimal('1.1')
```

```
>>> import json
>>> class ComplexEncoder(json.JSONEncoder):
...     def default(self, obj):
...         if isinstance(obj, complex):
...             return [obj.real, obj.imag]
...         # Let the base class default method raise the TypeError
...         return json.JSONEncoder.default(self, obj)
...
>>> json.dumps(2 + 1j, cls=ComplexEncoder)
'[2.0, 1.0]'
>>> ComplexEncoder().encode(2 + 1j)
'[2.0, 1.0]'
>>> list(ComplexEncoder().iterencode(2 + 1j))
['[', '2.0', ', ', '1.0', ']']
```

## copy_reg
`copy_reg.pickle(type, function[, constructor])`  ->  a string or tuple
If a string is returned, it names a global variable whose contents are pickled as normal. The string returned by __reduce__() should be the object’s local name relative to its module; the pickle module searches the module namespace to determine the object’s module.

When a tuple is returned, it must be between two and five elements long. Optional elements can either be omitted, or None can be provided as their value. The contents of this tuple are pickled as normal and used to reconstruct the object at unpickling time

# References

[1]: https://docs.python.org/3.9/library/pickle.html?highlight=cpickle#module-pickle	"pickle"
[2]: https://www.json.org/json-en.html	"json"

