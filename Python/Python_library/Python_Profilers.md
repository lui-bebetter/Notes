# Python Profilers
## profile && cProfile
### Usage
`profile.run(cmd, filename=None, sort=-1)`
`profile.runctx(cmd, globals, locals, filename=None)`
```
class profile.Profile(timer=None, timeunit=0.0, subcalls=True, builtins=True)
	enable()	# start collecting profiling data
	diable()	# stop collecting profiling data
	create_stats()
	print_stats(sort=-1)
	dump_stats(filename)
	run(cmd)
	runctx(cmd, globals, locals)
	runcall(func, *args, **kwargs)
```

```
class pstats.Stats(*filenames or profile, stream=sys.stdout)
	strip_dirs()
	add(*filenames)
	dump_stats(filename)
	sort_stats(*keys)
	reverse_order()
	print_stats(*restrictions)
	print_callers(*restrictions)
	print_callees(*restrictions)
```
