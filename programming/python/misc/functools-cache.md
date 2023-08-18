# Functools Cache

*August 2023* [Python](programming.html#python) [Misc](programming.html#python-misc)

```python
from functools import lru_cache
import timeit

def fib(n): return n if n < 2 else fib(n - 1) + fib(n - 2)

# fibonacci with cache
@lru_cache(maxsize=128)
def fib_cache(n): return n if n < 2 else fib_cache(n - 1) + fib_cache(n - 2)

print(format(timeit.timeit('fib(30)', globals=globals(), number=1), '.17f'))
# 0.12468479387462139
print(format(timeit.timeit('fib_cache(30)', globals=globals(), number=1), '.17f'))
# 0.00001071766018867

print(fib_cache.cache_info())
# CacheInfo(hits=28, misses=31, maxsize=128, currsize=31)
```