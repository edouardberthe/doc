# Concurrent programming

Python allows concurrent programming thanks to the built-in library `asyncio`.

## Since Python 3.7: `asyncio.run`

`asyncio.run` is the preferred (simplest) way to launch an asynchronous program (it should be launched from the highest-end point, like the `main.py` probably).
The best way to understand how `asyncio.run` interacts with its counterparts (`EventLoop.run_until_complete`, `run_forever`, etc.) is to see what is does.
From [this gist](https://gist.github.com/kaelzhang/e2ebdd03b61a28d879bfb74427fc4f46) it can be seen that `asyncio.run` does the following:

- create a new event loop
- set the newly-created event loop as the current event loop
- run the coroutine util completed
- set the current event loop as None

In Python code, it gives:

```python
new_loop = asyncio.new_event_loop()
asyncio.set_event_loop(new_loop)
new_loop.run_until_complete(coro)
asyncio.set_event_loop(None)
loop.close()
```
