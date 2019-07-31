# sync_to_async
Async wrapper to sync functions in Python.
```python
import asyncio
import functools


def sync_to_async(fn):
    @functools.wraps(fn)
    async def wrapper(*args, **kwargs):
        loop = asyncio.get_event_loop()
        p_func = functools.partial(fn, *args, **kwargs)
        return await loop.run_in_executor(None, p_func)

    return wrapper
```
# Sanic example
```python
import asyncio
import time
import functools
from sanic.response import json
from sanic import Sanic
from sanic.config import Config

Config.KEEP_ALIVE_TIMEOUT = 60


def sync_to_async(fn):
    @functools.wraps(fn)
    async def wrapper(*args, **kwargs):
        loop = asyncio.get_event_loop()
        p_func = functools.partial(fn, *args, **kwargs)
        return await loop.run_in_executor(None, p_func)

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
