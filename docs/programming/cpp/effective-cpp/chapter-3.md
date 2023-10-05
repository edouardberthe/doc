# Resource Management

*A resource is something that, once you're done using it, you need to return to the system.*

## Item 13: Use objects to manage resources

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
3. `priority()`
will be done (we only now that 2. will be after 1. of course).

Therefore, if the order is 1. -> 3. -> 2. and 3. raises an exception, the `Widget*` pointer will never be passed to the smart pointer, and therefore never be `delete`d.

!!! success "Use the following"

   ``` cpp
   std::shared_ptr<Widget> pW( new Widget );
   processWidget( pw, priority() );
   ```
