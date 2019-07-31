# sync_to_async
Async wrapper to sync functions in Python.
```python
    
def sync_to_async(fn):
    @functools.wraps(fn)
    def wrapper(*args, **kwargs):
        pool = ThreadPoolExecutor(max_workers=1)
        loop = asyncio.get_event_loop()
        return loop.run_in_executor(pool, fn, *args, **kwargs)

    return wrapper
```
