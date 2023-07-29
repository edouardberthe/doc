# Effective C++

## Chapter 1: Accustoming Yourself to C++

### Item 1: View C++ as a federation of languages

### Item 2: Prefer `const`s, `enum`s and `inline`s to `#define`s

Or "prefer the compiler to the preprocessor".

Instead of:

``` cpp
#define ASPECT_RATIO 1.653
```

prefer:
``` cpp
const double AspectRatio = 1.653; // uppercase names usually for macros
```
- in the case of floating point constant, can yield smaller code, because the preprocessor will result in multiple copy

### Item 3: Use `const` whenever possible

STL iterators are modeled on pointers so an `iterator` acts much like a `T*` pointer.
Declaring an `iterator const` is like declaring a pointer `const` (i.e. declaring a `T * const`): the iterator isn't allowed to point to something different, but the pointed thing can be modified.
To have an iterator to point to something that can't be modified, i.e. the STL analogue of a `const T*` pointer, you want a `const_iterator`.

``` cpp
std::vector<int> vec;

const std::vector<int>::iterator iter = vec.begin(); // iter acts like a `T* const`

*iter = 10;    // OK, changes what `iter` points to
++iter;        // error! `iter` is `const`

std::vector<int>::const_iterator citer = vec.begin(); // citer acts like a `const T*`

*citer = 10;   // error! `citer` is `const`
++citer;       // fine, changes `citer`
```

##### `const` in return value

Generally inappropriate, but can sometimes reduce the incidence of client errors without giving up safety or efficiency.

``` cpp
class Rational { ... };

const Rational operator*(const Rational &lhs, const Rationale &rhs);

Rational a, b, c;

(a * b) = c;  // oops, meant to do a comparison... but doesn't compile!
```

#### Bitwise `const`ness versus logical `const`ness

bitwise `const` means that not bit of the objects change. But the object can have pointer members whose the pointed objects can change! Therefore the behavior of this object will change. A most useful property is "logical `const`ness".

C++ `const` can only check for bitwise `const`ness. Thanks to the `mutable` qualifier, members can be changed but still the object won't change its logical `const`ness.


### Iterm 4: make sure objects are initialized before they're used

- use member initialization

!!! success "Summary"

    - Manually initialize objects of built-in type, because C++ only sometimes initializes them itself
    - In a constructor, prefer member initialization list to assignment inside the body. List data members in the same order they're declared in the class
    - Avoid initialization order problems across translation units by replacing non-local `static` objects with local `static` objects

## Chapter 2: Constructors, Destructors and Assignment Operators

### Item 6: Know what functions C++ silently writes and calls.