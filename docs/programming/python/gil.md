# GIL in Python

Resources:

- Blog article on [realpython.org](https://realpython.com/python-gil/) 
- Talk by Larry Hastings during PyCon 2015: [Python's Infamous GIL](https://www.youtube.com/watch?v=KVKufdTphKs) (referenced by the former)

Summary:

- There is no Garbage Collector in Python, so we need to count the references of any object, so that when this count reaches 0 we can free the memory allocated to store this object.
- Problem is: when several threads involved, they can all have different references to the same object --> difficult to keep track of the references
- Solution taken for Python: simply disallowing it! There is a lock on the **Python interpreter itself**. If there are several threads involved, and if they want to execute native Python code, they have to wait for their turn to execute the Python interpreter.
- Dates:
  - 1990: Python invented
  - 1992: GIL added. At this time, there was no multi-core, because if you wanted to do it, you really had to physically handle 2 processors on the motherboard (so have a motherboard specially adapted for that).
  - But now, the world is fully multi-core, since we managed to have several physical cores on the same processor
  - Guido is aware of that, but he said he is ok to remove the GIL, only if it doesn't decrease performance on single-threaded examples --> very difficult to reach!

There have been attempts to do multi-threading on Python in Python 1.4 without any API change.
They took all the global variables and turn them into `struct`s (structures).
 The problem was of course of the references counting (as explained before).
To solve this problem, they tried to create a resource which was keeping track of the counts of all references, but they had obviously to add a *lock* on this resource!
To sum up: in order to remove the lock on the *Python interpreter*, they had to add a lock on the *references counter*, so that this resource could count the references efficiently in spite of the different threads running the Python interpreter simultaneously.
But all the threads were constantly grabbing and releasing the lock so much, that the *lock itself* was causing a big performance issue --> **between 4x and 7x slower!**

In order to manage the memory, another possibility than the GIL (having a counter keeping track of the number of references there are of all the objects) would be to have a "Pure" Garbage Collector. It works the following way: when the memory is full (at least, almost full, because if we wait to have the absolute fullness of the memory, therefore it must certainly be that we cannot do any further computation to release it, given that any computation would require at least some memory..), then we stop all the program computation, and we do an exploration of all the objects currently used in the Program. All the objects which cannot be "reached" by the exploration cannot be used, and can be "collected" (a nice word to say destroyed and removed). 

CPython is not the only implementation of Python interpretation. There are four main ones, which does not all have a GIL, but have a GC:

|                |  Pure GC |  GIL |
|----------------|----------|------|
|cpython (C)     |     no   | yes  |
|jython (java)   |    yes   |  no  |
|ironpython (C#) |    yes   |  no  |
|pypy            |    yes   |  no* |

**It's perfectly possible to have a working Python interpreter without any sort of GIL**.
Would it be possible to have GC instead of reference counting? Yes. Would it be as fast as reference counting? We can't know until we do it.
Conventional wisdom about GC is that it is about the same speed as reference counting. But the real problem is that it would break all the C extensions API, and we can't afford that (Python 3 did that, and it seems that it is slowly adapted.. not sure though).
