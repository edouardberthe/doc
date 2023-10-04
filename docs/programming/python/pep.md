# PEP

## Important PEPs to remember

## Generators and synchronicity

- [PEP 342 -- Coroutines via Enhanced Generators](https://www.python.org/dev/peps/pep-0342/) (10 May 2005, Python 2.5)  
    In particular, this solves:
    - [PEP 288](https://www.python.org/dev/peps/pep-0288/) (2002): Generators Attributes and Exceptions (withdrawn)
    - [PEP 325](https://www.python.org/dev/peps/pep-0325/) (25/08/2003): Resource-Release Support for Generators (rejected)

- [PEP 380 -- Syntax for Delegating to a Subgenerator](https://www.python.org/dev/peps/pep-0380/) (13 Feb 2009, Python 3.3)  
    Basically, implements the `yield from` operator, allowing to transfer the generation to another generator. If only `yield` as statement are used, this is equivalent to iterating over the subgenerator via a classical `for` loop, but if the `yield` is used as an expression (for instance in `x = yield`), meaning that the generator is actually a coroutine, then the `yield from` expression also transfer:
    - the data sent by the `send()` command
    - the errors sent by the `throw()` command
    - the exception sent by the `close()` command

- [PEP 492 -- Coroutines with async and await syntax](https://www.python.org/dev/peps/pep-0492/) (Python 3.5)
    - 09 Apr. 2015: Proposed
    - 05 May 2015: Accepted by Guido
    - 11 May 2015: Merged

## Type hints:

- [PEP 484 -- Type Hints](https://www.python.org/dev/peps/pep-0484/) (Python 3.5), by Guido. The most important one.
- [PEP 483 -- The Theory of Type Hints](https://www.python.org/dev/peps/pep-0483/) (Informational)  
  Lays down the theory of the previous one
    

- [PEP 526 -- Syntax for Variable Annotations](https://www.python.org/dev/peps/pep-0526/) (Python 3.6, 09/08/2016):   
  Allow to annotate variables directly instead of inside comments.  
  Before:  
  ```
  a = "coucou" # type: str
  ```  
  After:  
  ```
  a: str = "coucou"
  ```
- [PEP 585 -- Type Hinting Generics In Standard Collections](https://www.python.org/dev/peps/pep-0585/) (03/03/2019)  
  Abstract:  
  *"Static typing as defined by PEPs 484, 526, 544, 560, and 563 was built incrementally on top of the existing Python runtime and constrained by existing syntax and runtime behavior. This led to the existence of a duplicated collection hierarchy in the typing module due to generics (for example typing.List and the built-in list).  
  This PEP proposes to enable support for the generics syntax in all standard collections currently available in the typing module."*
