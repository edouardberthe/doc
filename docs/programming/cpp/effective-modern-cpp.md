## Chapter 4: Smart Pointers

Raw pointers are genuinely *bad*

- points to single object or array? 
- should you destroy what it points to, i.e. does the pointer *owns* the object?
- if yes, how? `delete`, `delete[]`? 
- if yes, hard to do it only *exactly once* along every path in the code (memory leak or undefined behavior if destroyed twice)
- no way to tell if the pointer dangles

Smarts pointers are the way to solve it. 

> you should therefore prefer smart pointers to raw pointers

4 smart pointers in C++11:
- `std::auto_ptr`: tried to model unique ownership, but without move semantic. Full of bugs -> *never use *
- `std::unique_ptr`
- `std::shared_ptr`
- `std::weak_ptr`

