# PyIntObject 对象

## 结构定义

```c
#define PyObject_HEAD \
	PyObject *ob_next; \
	PyObect *ob_prev; \
	int ob_refcnt; \
	PyTypeObject *ob_type;

[intobject.h]
typedef struct {
	PyObject_HEAD
    long ob_ival;
} PyIntObject;

[intobject.c]
PyTypeObject PyInt_Type={
	PyVarObject_HEAD_INIT(&PyType_Type,0)
	"int",
	sizeof(PyIntObject), //tp_basicsize
	0, //tp_itemsize(one item size in varObject like array, list)
	(destructor)int_dealloc, //tp_dealloc
	(printfunc)int_print, //tp_print
	0, //tp_getattr
	0, //tp_setattr
	(cmpfunc) int_compare, //tp_compare
	(reprfunc) int_repr, //tp_repr
	&int_as_number, //tp_as_number
	0, //tp_as_sequence
	0, //tp_as_mapping
	(hashfunc) int_hash, //tp_hash
	0, //tp_call
	(reprfunc) int_to_decimal_string, //tp_str
	PyObject_GenericGetAttr, //tp_getattro
	0, //tp_setattro
	0, //tp_as_buffer
	Py_TPFLAGS_DEFAULT | Py_TPFLAGS_CHECKTYPES | 			  Py_TPFLAGS_BASETYPES, //tp_flags
	int_doc, //tp_doc
	0, //tp_traverse
	0, //tp_clear
	0, //tp_richcompare
	0, //tp_weaklistoffset
	0, //tp_iter
	int_methods, //tp_methods
	0, //tp_members
	int_getset, //tp_getset
	0, //tp_base
	0, //tp_dict
	0, //tp_descr_get
	0, //tp_descr_set
	0, //tp_dictoffset
	0, //tp_init
	0, //tp_alloc
	int_new,//tp_new
}

```



## 构造函数

一般通过这三种函数：

```c
PyObject * PyInt_FromLong(long ival);
PyObject * PyInt_FromString(char * s, char **pend, int base);
#ifdef Py_USING_UNICODE
PyObject * PyInt_FromUnicode(Py_Unicode *s, int length, int base);
#endif
```

### 小整数对象

小整数对象有两个宏：NSMALLPOSINTS, NSMALLNEGINTS来定义，区间为-NSMALLNEGINTS～NSMALLPOSINTS-1

```c
[intobject.c]
#ifndef NSMALLPOSINTS
#define NSMALLPOSINTS 100
#endif

#ifndef NSMALLNEGINTS
#define NSMALLNEGINTS 5
#endif 

#if NSMALLPOSINTS+NSMALLNEGINTS>0
static PyIntObject * small_ints[NSMALLPOSINTS+NSMALLNEGINTS];
#endif


int _PyInt_Init(void){
#if NSMALLNEGINTS+NSMALLPOSINTS >0 
    PyIntObject *v;
    long ival;
    for(ival=-NSMALLNEGINTS; ival<NSMALLPOSINTS; ival++){
    	if(free_list==NULL &&(free_list=fill_free_list())==NULL)
        	return 0;
        v=free_list;
        free_list=(PyIntObject *)(v->ob_type);
        (void)PyObject_INIT(v,&PyInt_Type);
        v->ob_ival=ival;
        small_ints[ival+NSMALLNEGINTS]=v;
    }  
#endif
    return 1;
}
```

### 大整数对象

预分配内存块：

```c
[intobject.c]
#define BLOCK_SIZE 1000
#define BHEAD_SIZE 8 //enough for 64-bit pointer
#define N_INTOBJECTS  ((BLOCK_SIZE-BHEAD_SIZE)/sizeof(PyIntObject))

//never return to system before shutdown (PyInt_FIni).
typedef struct _intblock {
    struct _intblock * next;
    PyIntObject objects[N_INTOBJECTS]；
};
```

预分配内存块通过单向链表block_list连接起来, block内部空闲内存通过free_list连接：

```c
[intobject.c]
static PyIntBlock * block_list=NULL;
static PyIntObject * free_list=NULL;
```

构造整数对象：

```c
[intobject.c]
#define PyObject_INIT(op,typeobj) \
	((op)->ob_type=(typeobj), (op)->ob_refcnt=1, (op))
	
static PyIntObject * fill_free_list(void){
	PyIntBlock * b;
    PyIntObject * s,e;
    b=(PyIntBlock *)PyMem_MALLOC(sizeof(PyIntBlock));
    if(b==NULL)
        return (PyIntObject *)PyErr_NoMemory();
    b->next=block_list;
    block_list=b;
    s=&b->objects[0];
   	e=s+N_INTOBJECTS;
    while(--e>p){
        e->ob_type=(PyTypeObject *)(e-1);
    }
    e->ob_type=NULL;
    return s+N_INTOBJECTS-1;
}


PyObject * PyInt_FromLong(long ival){
    register PyObject * v;
#if NSMALLPOSINTS+NSMALLNEGINTS >0
    if(ival>=-NSMALLNEGINTS && ival <NSMALLPOSINTS){
        v=small_ints[ival+NSMALLNEGINTS];
        Py_INCREF(v);
        return (PyObject*)v;
    }
#endif
    if(free_list==NULL){
        if((free_list=fill_free_list())==NULL){
            return NULL;
        }
    }
    v=free_list;
    free_list=(PyIntObject *) v->ob_type;
    PyObject_INIT(v,&PyIntType);
    v->ob_ival=ival;
    return (PyObject*)v;
}
```

