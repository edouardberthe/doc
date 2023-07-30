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

## Chapter 3: Resource Management

<i>A resource is something that, once you're done using it, you need to return to the system.</i>

### Item 13: Use objects to manage resources

2 critical aspects of using objects to manage resources:

- **Resources are acquired and immediately turned over to resource-managing objects**
  
    --> "Resource Acquisition Is Initialization" (RAII)

- **Resource-managing objects use their destructors to ensure that resources are released**

Smart pointers are doing that for us, e.g; `std::auto_ptr`.

Ex:

!!! failure "Instead of"

    ``` cpp
    void f()
    {
        Investment *pInv = createInvestment();
        
        ...
        
        delete pInv;
    }
    ```

!!! success "prefer"

    ``` cpp
    void f()
    {
        std::auto_ptr<Investment> pInv(createInvestment());
    }
    ```

But because an `std::auto_ptr` automatically `delete`s what it points to when it is destroyed, it's important that there never be more than one `auto_ptr` pointing to an object.
For this reason, copying an `std::auto_ptr` (via copy constructor or copy assignment operator) sets the internal pointer to null and transfers the sole ownership to the copied object.

This odd copying behavior means that they aren't the best way to manage all dynamically allocated resources.

!!! success "Summary"

    - To prevent resource leak, use RAII objects that acquire resources in their constructors and release them in their destructors
    - Two commonly useful RAII classes are `shared_ptr` and `std::auto_ptr`. The first one is usually the best choice, because its behavior when copied is intuitive, whereas copying an `auto_ptr` sets it to null.

### Item 14: THink carefully about coyping behavior in resource-managing classes

When creating a resource-allocating object, `shared_ptr` or `std::auto_ptr` make no sense to be used. 

Example for a `Lock` object:

``` cpp
class Lock {
public:
    explicit Lock( Mutex *pm ) : mutexPtr( pm )
    {
        lock( mutexPtr );
    }
    ~Lock()
    {
        unlock( mutexPtr );
    }

private:
    Mutex *mutexPtr;
};

void main()
{
    Mutex m;
    ...
    {                 // create block to define critical section
        Lock ml(&m);  // lock the mutex
        ...           // perform critical section operations
    }                 // automatically unlock mutex
}
```
But what should happen if a `Lock` object is copied?

``` cpp
Lock ml1( &m );
Lock m2( ml1 );
```

General question: what to do when a RAII object is copied? Often, several strategies:

- **prohibit copying**

- **reference count the underlying resource**: in this case, it makes sense to make use of a `shared_ptr` inside the class, instead of a raw pointer. But, by default, when the reference count goes to 0 the `shared_ptr` will `delete` the resource, which is not what we want here. Fortunately we can pass a "deleter" to a `shared_ptr`, a function which will be called when reference count goes to 0.

    ``` cpp
    class Lock {
    public:
        explicit Lock( Mutex *pm ) : mutexPtr( pm, unlock )
        {
            lock( mutexPtr.get() );
        }
    private:
        shared_ptr< Mutex > mutexPtr;
    }
    ```

- **Copy the underlying resource**: also called "deep copy". Example: `std::string`.

- **Transfer ownership of the underlying resource**. This is the meaning of `std::auto_ptr`.

!!! success "Summary"

    - Copying an RAII object entails copying the resource it manages, so the copying behavior of the resource determines the copying behavior of the RAII object.

### Item 15: Provide access to raw resources in resource-managing classes

You have the choice between **explicit conversion** or **implicit conversion**.

First one is usually implemented via a `.get()` operator.
Second one can be implemented by overriding the `operator HandleName() const`.
The downside of implicit conversion is that it increases the chance of errors, e.g. a client can create a RAII object where he wanted a raw ressource object.

### Item 16: Use the same form in corresponding uses of `new` and `delete`

### Item 17: Store `new`ed objects in smart pointers in standalone statements

Suppose the following function:
``` cpp
int priority();
void processWidget(std::shared_ptr< Widget > pw, int priority);
```

If there is no implicit conversion from `Widget*` to `std::shared_ptr<Widget>` (which is fine), it is tempting to call it like that:

``` cpp
processWidget( std::shared_ptr< Widget >(new Widget), priority());
```

This can lead to memory leak: indeed we do not in which order the 3 following calls:
1. `new Widget`
2. `std::shared_ptr` constructor
3.  `priority()`
will be done (we only now that 2. will be after 1. of course).

Therefore, if the order is 1. -> 3. -> 2. and 3. raises an exception, the `Widget*` pointer will never be passed to the smart pointer, and therefore never be `delete`d.

!!! success "Use the following"

   ``` cpp
   std::shared_ptr<Widget> pW( new Widget );
   processWidget( pw, priority() );
   ```

## Chapter 4: Designs and Declarations

How to design and declare good C++ interfaces?

### Item 18: Make interfaces easy to use correctly and hard to use incorrectly

Assuming you're dealing with reasonable people, the clients are trying to do a good job. They *want* to use the interfaces correctly. That being the case, if they use one incorrectly, your interface is at least partially to blame. Ideally if an attempted use of an interface won't do what the client expects, the code won't compile; and if the code does compile it will do what the client wants.

``` cpp
class Date {
public:
    Date( int month, int day, int year);
    ...
};
```

May seen reasonable (at least in the USA..) but can pass `int` in wrong order, or use a month greater than 12.
``` cpp
class Date
{
public:
    Date( const Month& m const Day& d, const Year& y);
};
```

Other way: restrict what can be done with a type --> adding `const` (see item 3).
More generally -> **have your types behave consistently with the built-in types**, unless there's a good reason not to. Clients already know how types like `int` behave, so you should strive to have your types behave the same way.

--> the main characteristic leading to interfaces which are easy to use is **consistency**.

Interfaces of STL are largely consistent, this helps make them easy to use (e.g. the method `size()` available on all containers).

Other example:

``` cpp
Investment* createInvestment();
```

can leads to the user to **not** handle correctly the memory. He can uses a smart pointer, but nothing prevents him not to. A better idea would be:

``` cpp
std::shared_ptr<Investment> createInvestment();
```

!!! success Summary
    - Good interfaces are easy to use correctly and hard to use incorrectly.
    - Ways to facilitate correct use include consistency in interfaces and behavioral compatibility with built-in types.
    - Ways to prevent errors include creating new types, restricting operations on types, constraining object values and eliminating client resources management responsibilities.
    - `std::shared_ptr` supports custom `delete`rs. This prevents the cross-DLL problem (see item 14).

### Item 19: Treat class deisgn as type design.

### Item 20: Prefer pass-by-reference-to-`const` to pass-by-value.

References are implemented as pointers. Therefore, for built-in types as `int` it makes sense to pass by value. Therefore, it could make sense to pass "small" custom types by value instead of by references.
But even with small ones, there can be problems. Some compilers don't handle them the same way as built-in types. For instance, even if your custom type is based on `int`, some compiler won't allow to put them in registers. References being implemented as pointers, therefore as `int`, they will be put in registers.

Furthermore, it avoids slicing problem: if you pass a derived class to a function which expects a base class, a copy will be created by a copy only of the "base" part of the class, leaving us with a real base class instance, which is almost never what we really want. Using reference makes it act polymorphically.

!!! success Summary
    - Good interfaces are easy to use correctly and hard to use incorrectly.

### Item 21: Don't try to return a reference when you must return an object.

### Item 22: Declare data members `private`

--> data member shouldn't be `public`
--> all the arguments against `public` data members apply equally to `protected` ones.