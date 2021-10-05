# Python Attribute Lookup

- Magic Method

  `obj->obj_type->tp_<magicmethod>`

- Non-Magic Method

    `obj_or_type.name or getattr(obj_or_type, name)` 实际做的是[1][]：

    ```
    try:
        return type(obj_or_type).__getattribute__(obj_or_type, name)
    except AttributeError:
        if not hasattr(type(obj_or_type), '__getattr__'):
            raise
    return type(obj_or_type).__getattr__(obj_or_type, name)
    ```
    属性查找顺序定义在`obj_or_type.__getattribute__`中

## Instance
`type(instance).__getattribute__`继承于`object.__getattribute__`

- data descriptor
- instance variable
- non-data descriptor
- class variable in method resolve order

A pure Python equivalent [1][]:

```
def object_getattribute(obj, name):
    "Emulate PyObject_GenericGetAttr() in Objects/object.c"
    null = object()
    objtype = type(obj)
    cls_var = getattr(objtype, name, null)
    descr_get = getattr(type(cls_var), '__get__', null)
    if descr_get is not null:
        if (hasattr(type(cls_var), '__set__')
            or hasattr(type(cls_var), '__delete__')):
            return descr_get(cls_var, obj, objtype)     # data descriptor
    if hasattr(obj, '__dict__') and name in vars(obj):
        return vars(obj)[name]                          # instance variable
    if descr_get is not null:
        return descr_get(cls_var, obj, objtype)         # non-data descriptor
    if cls_var is not null:
        return cls_var                                  # class variable
    raise AttributeError(name)
```
## Class

`type(cls).__getattribute__`继承于`type.__getattribute__`

- metaclass data descriptor

- class all kind of descriptor

- class variable

- metaclass non-data descriptor

- metaclass class variable in method resolve order

  A pure Python equivalent [2][]:

    ```
    def class_getattribute(cls, name):
        null = object()
        metaclass = type(cls)
        meta_var = getattr(metaclass, name, null)
        descr_get = getattr(type(meta_var), '__get__', null)
        if descr_get is not null:
            if (hasattr(type(meta_var), '__set__')
                or hasattr(type(meta_var), '__delete__')):
                return descr_get(meta_var, cls, metaclass)   # metaclass data descriptor
        
        for c in cls.__mro__:
        	if hasattr(c, '__dict__') and name in vars(c):
            	var = vars(c)[name]
            	if getattr(type(var), '__get__', null)：
                	return type(var).__get__(var, None, cls)	 # class all descriptor
            	else:
                	return var                          		 # class variable
                
        if descr_get is not null:
            return descr_get(meta_var, obj, objtype)          # metaclass non-data descriptor
        if meta_var is not null:
            return meta_var                                  # metaclass variable
        raise AttributeError(name)
    ```

## super

定义在`super.__getattribute__`：

`super(A, obj).m` =  searches `obj.__class__.__mro__` for the base class `B` immediately following `A` and then returns `B.__dict__['m'].__get__(obj, A)`. If not a descriptor, `m` is returned unchanged.

## References

[1]: https://docs.python.org/3.11/howto/descriptor.html	"Overview of descriptor invovation"

[2]: https://blog.ionelmc.ro/2015/02/09/understanding-python-metaclasses/#default-object-getattribute	"MetaClass"

