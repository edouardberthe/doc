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
- `std::auto_ptr`: tried to model unique ownership, but without move semantic. Full of bugs -> *NEVER use, use `std::unique_ptr` instead*
- `std::unique_ptr`
- `std::shared_ptr`
- `std::weak_ptr`

### 18. Use `std::unique_ptr` for exclusive-ownership resource management

Small and fast.

> *If a raw pointer is small enough and fast enough for you, a `std::unique_ptr` almost certainly is, too. *

> [!TIP] Summary
> By default, resource destruction takes place via `delete` but custom deleters can be specified. Stateful deleters increase the size of `std::unique_ptr` objects.
>
> `std::unique_ptr -> std::shared_ptr` conversion is easy.

### 19. Use `std::shared_ptr` for shared-ownership resource management.

Garbage collection vs destructors : *"Resource lifetime management should be done by machines"*.

But the timing of resource reclamation can be nondeterministic, whereas destructors calls are.

`std::shared_ptr` is the C++11 way of having the best of both worlds: automatically but predictable timing.

All `std::shared_ptr` pointing to the same object have a reference count keeping track of how many they are. When the count reaches 0, the memory is deallocated.

- **`std::shared_ptr` are 2x the size of a raw pointer**, because they also contain a raw pointer to the resource's reference count.

- **Memory for the reference count must be dynamically allocated**. Pointed-to object knows nothing about the reference count, so have no space to store it. When using `std::make_shared`, the cost of allocation is avoided.

- **Increments and decrements of the reference counts must be atomic**, because there can be simultaneous readers/writers in different threads.