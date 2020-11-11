# Important PEP to remember

## Generators and synchronicity

- [PEP 342](https://www.python.org/dev/peps/pep-0342/) (10 May 2005): Coroutines via Enhanced Generators.
    In particular, this solves:
    - [PEP 288](https://www.python.org/dev/peps/pep-0288/) (2002): Generators Attributes and Exceptions (withdrawn)
    - [PEP 325](https://www.python.org/dev/peps/pep-0325/) (25/08/2003): Resource-Release Support for Generators (rejected)
- [PEP 380](https://www.python.org/dev/peps/pep-0380/) (13/02/2009): Syntax for Delegating to a Subgenerator.
    
    Basically, implements the `yield from` operator, allowing to transfer the generation to another generator. If only `yield` as statement are used, this is equivalent to iterating over the subgenerator via a classical `for` loop, but if the `yield` is used as an expression (for instance in `x = yield`), meaning that the generator is actually a coroutine, then the `yield from` expression also transfer:
    - the data sent by the `send()` command
    - the errors sent by the `throw()` command
    - the exception sent by the `close()` command
- [PEP 492](https://www.python.org/dev/peps/pep-0492/): Coroutines with async and await syntax
    - 09 Apr. 2015: Proposed
    - 05 May 2015: Accepted by Guido
    - 11 May 2015: Merged
