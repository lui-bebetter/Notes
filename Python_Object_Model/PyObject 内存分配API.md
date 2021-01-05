# PyObject 内存分配API

High-level object memory interfaces主要是现在objimpl.h中：

```c
[objimpl.h]
/* BEWARE:
   Each interface exports both functions and macros. Extension 	 	   should use the functions, to ensure binary compatibility across Python versions. Because the Python implementation is free to change internal details, and the macros may (or may not) expose details for speed, if you do use the macros you must recompile your extensions with each Python release.
   Never mix calls to PyObject_ memory functions with calls to the platform malloc/realloc/calloc/free, or with calls to PyMem_.
*/

#define _PyObject_SIZE(typeobj) ((typeobj)->tp_basicsize)

#define PyObject_INIT(op,typeobj) \
(Py_TYPE(op)=(typeobj), _Py_NewReference((PyObject*)(op)) , (op))

#define PyObject_INIT_VAR(op, typeobj, nitems) \
	( Py_SIZE(op)=(nitems), PyObject_INIT((op), (typeobj)) )
	
#define _PyObject_VAR_SIZE(tp nitems) \
	(size_t)(( (tp)->tp_basicsize +  \ 
	(nitems)*(tp)->tp_itemsize+(Py_VOID_SIZE-1)) \
     & ~(Py_VOID_SIZE-1))
s
        
PyObject *_PyObject_New(PyTypeObject *tp){
	PyObject *op;
	op=(PyObject *)PyObject_MALLOC(_PyObject_SIZE(tp));
	if(op==NULL) return PyErr_NoMemory();
	return PyObject_INIT(op,tp);
}

#define PyObject_New(type, typeobj) \
	((type *) _PyObject_New(typeobj))

PyVarObject *_PyObject_NewVar(PyTypeObject *tp, Py_ssize_t nitems){
	PyVarObject *op;
    const size_t size=_PyObject_VAR_SIZE(tp,nitems);
    op=(PyVarObject *) _PyObject_MALLOC(size);
    if (op==NULL) returnm (PyVarObject *) PyErr_NoMemory();
    return PyObject_INIT_VAR(op, tp, nitems);
}

#define PyObject_NewVar(type, typeobj, nitems) \
	((type *) _PyObject_NewVar((typeobj),(nitems) ) )

PyObject * PyObject_Init(PyObject * op, PyTypeObject * tp){
    if (op==NULL) return PyErr_NoMemory();
    PyTYPE(op)=tp;
    _Py_NewReference(op);
    return op;
}

PyVarObject * PyObject_InitVar(PyVarObject *op, PyTypeObject *tp, Py_ssize_t n){
	if (op==NULL) return (PyVarObject *) PyErr_NoMemory();
    PyTYPE(op)=tp;
    op->ob_size=n;
    _Py_NewReference((PyObject *)op);
    return op;
}

#define PyObject_NEW(type, typeobj) \
	( (type*)\
	PyObject_Init( \
	(PyObject*) _PyObject_MALLOC( _PyObject_SIZE(typeobj)),(typeobj) )\
    )

#define PyObject_NEW_VAR(type, typeobj,n) \
	((type *)\
	PyObject_InitVar(\
	(PyVarObject *)PyObject_MALLOC(_PyObject_VAR_SIZE((typeobj),(n))), typeobj,n)\
	)
        
```

