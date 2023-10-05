# Designs and Declarations

How to design and declare good C++ interfaces?

## Item 18: Make interfaces easy to use correctly and hard to use incorrectly

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

## Item 19: Treat class deisgn as type design.

## Item 20: Prefer pass-by-reference-to-`const` to pass-by-value.

References are implemented as pointers. Therefore, for built-in types as `int` it makes sense to pass by value. Therefore, it could make sense to pass "small" custom types by value instead of by references.
But even with small ones, there can be problems. Some compilers don't handle them the same way as built-in types. For instance, even if your custom type is based on `int`, some compiler won't allow to put them in registers. References being implemented as pointers, therefore as `int`, they will be put in registers.

Furthermore, it avoids slicing problem: if you pass a derived class to a function which expects a base class, a copy will be created by a copy only of the "base" part of the class, leaving us with a real base class instance, which is almost never what we really want. Using reference makes it act polymorphically.

!!! success Summary
    - Good interfaces are easy to use correctly and hard to use incorrectly.

## Item 21: Don't try to return a reference when you must return an object.

## Item 22: Declare data members `private`

--> data member shouldn't be `public`
--> all the arguments against `public` data members apply equally to `protected` ones.
