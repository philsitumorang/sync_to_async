# sync_to_async
Async wrapper to sync functions in Python.
```python
import time
import asyncio    
import functools
from concurrent.futures import ThreadPoolExecutor


def sync_to_async(fn):
    @functools.wraps(fn)
    def wrapper(*args, **kwargs):
        pool = ThreadPoolExecutor(max_workers=1)
        loop = asyncio.get_event_loop()
        return loop.run_in_executor(pool, fn, *args, **kwargs)

    return wrapper
```
# Sanic example
```python
import asyncio
import time
import functools
from sanic.response import json
from sanic import Sanic
from concurrent.futures import ThreadPoolExecutor
from sanic.config import Config

Config.KEEP_ALIVE_TIMEOUT = 60


def sync_to_async(fn):
    @functools.wraps(fn)
    def wrapper(*args, **kwargs):
        pool = ThreadPoolExecutor(max_workers=1)
        loop = asyncio.get_event_loop()
        return loop.run_in_executor(pool, fn, *args, **kwargs)

    return wrapper


@sync_to_async
def async_sleep(s):
    time.sleep(s)


app = Sanic()


@app.route("/")
async def test(request):
    await async_sleep(5)
    return json({"hello": "world"})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000, debug=True)
```
