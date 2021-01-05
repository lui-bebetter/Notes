# PyLongObject 对象

+ ## 结构定义

  对应不同的系统平台，PyLongObject有两套不同的digit类型定义。PyLongObject 通过保存指向存储digits数组的指针ob->digit和ob_size来表示任意大小整数，为此PyLong_Type通过tp_basicsize，tp_itemsize来分配足够内存空间。

  ```c
  #define PyObject_VAR_HEAD \
  	struct _object *ob_next;\
  	struct _object *ob_prev;\
  	int ob_refcnt;\
  	PyTypeObject * ob_type;\
  	int ob_size;
  [longintrepr.h]
  #if PYLONG_BITS_IN_IDGIT==30
  typedef PY_UINT32_T digit;
  typedef PY_INT32_T sdigit; //signed digits
  typedef PY_UINT64_T twodigits;
  typedef PY_INT64_T stwodigits;
  #define PyLong_SHIFT 30
  
  #elif PYLONG_BITS_IN_DIGIT==15
  typedef unsigned short digit;
  typedef short sdigit;
  typedef usigned long twodigits;
  typedef long stwodigits;
  #define PyLong_SHIFT 15
  #endif
  
  #define PyLong_BASE  ((digit)1<<PyLong_SHIFT)
  #define PyLong_MASK  ((digit)(PyLong_BASE-1))
  
  struct _longobject{
      PyObject_VAR_HEAD
      digit ob_digit[1];
  }
  
  [longobject.h]
  typedef struct _longobject PyLongObject;
  
  [longobject.c]
  #define offsetof(type, attr) (size_t)&(((type *)0)->attr)
  
  PyTypeObject PyLong_Type={
  	PyVarObject_HEAD_INIT(&PyType_Type, 0)
  	"long",
  	offsetof(PyLongObject,ob_digit), //tp_basicsize
  	sizeof(digit), //tp_itemsize
  	(destructor)long_dealloc,
  	......
  }
  ```

+ ## 构造函数

  典型的构造函数有：

  ```c
  PyObject * PyLong_FromLong(long ival)
  ```
