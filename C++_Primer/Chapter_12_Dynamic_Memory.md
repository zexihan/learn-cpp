# Chapter 12 Dynamic Memory

Global objects are allocated at program start-up and destroyed when the program ends. Local, automatic objects are created and destroyed when the block in which they are defined is entered and exited. Local static objects are allocated before their first use and are destroyed when the program ends.

In addition to supporting automatic and static objects, C++ lets us allocate objects dynamically. Dynamically allocated objects have a lifetime that is independent of where they are created; they exist until they are explicitly freed.

## 12.1 Dynamic Memory and Smart Pointers

The new library defines two kinds of smart pointers that differ in how they manage their underlying pointers: `shared_ptr`, which allows multiple pointers to refer to the same object, and `unique_ptr`, which "owns" the object to which it points.

### The `shared_ptr` Class

```cpp
#include <memory>

shared_ptr<string> p1;
shared_ptr<list<int>> p2;

if (p1 && p1->empty())
    *p1 = "h1";
```

Operations Common to `shared_ptr` and `unique_ptr`

```cpp
shared_ptr<T> sp // null smart pointer that can point to objects of type T
unique_ptr<T> up 
p // use p as a condition; true if p points to an object
*p // dereference p to get the object to which p points
p->mem // synonym for (*p).mem
p.get() // returns the pointer in p
swap(p, q) // swaps the pointers in p and q
p.swap(q)
```

Operations specific to `shared_ptr`

```cpp
make_shared<T> (args) // returns a shared_ptr pointing to a dynamically allocated object of type T. Use args to initialize that object
shared_ptr<T> p(q) // p is a copy of the shared_ptr q; increments the count in q. The pointer in q must be convertible to T
p = q // p and q are shared_ptrs holding pointers that can be converted to one another. Decrements p's reference count and increments q's count; deletes p's existing memory if p's count goes to 0
p.unique() // returns true if p.use_count() is one; false otherwise
p.use_count() // returns the number of objects sharing with p; may be a slow operation, intended primarily for debugging purposes
```

**The `make_shared` Function**

```cpp
shared_ptr<int> p3 = make_shared<int>(42);
shared_ptr<string> p4 = make_shared<string>(10, '9');
shared_ptr<int> p5 = make_shared<int>();
auto p6 = make_shared<vector<string>>();
```

**Copying and Assigning `shared_ptr`s**

```cpp
auto p = make_shared<int>(42);
auto q(p);

auto r = make_shared<int>(42); // int to which r points has one user
r = q; // assign to r, making it point to a different address
       // increase the use count for the object to which q points
       // reduce the use count of the obejct to which r had pointed
       // the object r had pointed to has no users; that object is automatically freed
```

**`shared_ptr`s Automatically Destroy Their Objects and Automatically Free the Associated Memory**

```cpp
shared_ptr<Foo> factory(T arg)
{
    // process arg as appropriate
    // shared_ptr will take care of deleting this memory
    return make_shared<Foo>(arg);
}

void use_factory(T arg)
{
    shared_ptr<Foo> p = factory(arg);
    // use p
} // p goes out of scope; the memory to which p points is automatically freed

shared_ptr<Foo> use_factory(T arg)
{
    shared_ptr<Foo> p = factory(arg);
    // use p
    return p; // reference count is incremented when we return p
} // p goes out of scope; the memory to which p points is not freed

```

**Classes with Resources That Have Dynamic Lifetime**

Programs tend to use dynamic memory for one of three purposes:

1. They don’t know how many objects they’ll need
2. They don’t know the precise type of the objects they need
3. They want to share data between several objects

```cpp
vector<string> v1; // empty vector
{ // new scope
    vector<string> v2 = {"a", "an", "the"};
    v1 = v2; // copies the elements from v2 into v1
} // v2 is destroyed, which destroys the elements in v2
// v1 has three elements, which are copies of the ones originally in v2


// Unlike the containers, we want Blob objects that are copies of one another to share the same elements. That is, when we copy a Blob, the original and the copy should refer to the same underlying elements.
Blob<string> b1; // empty Blob
{ // new scope
    Blob<string> b2 = {"a", "an", "the"};
    b1 = b2; // b1 and b2 share the same elements
} // b2 is destroyed, but the elements in b2 must not be destroyed
// b1 points to the elements originally created in b2
```

One common reason to use dynamic memory is to allow multiple objects to share the same state.

**Defomomg tje `StrBlob` Class**

```cpp
class StrBlob {
public:
    typedef std::vector<std::string>::size_type size_type;
    StrBlob();
    StrBlob(std::initializer_list<std::string> il);
    size_type size() const { return data->size(); }
    bool empty() const { return data->empty(); }
    // add and remove elements
    void push_back(const std::string &t) {data-
    >push_back(t);}
    void pop_back();
    // element access
    std::string& front();
    std::string& back();
private:
    std::shared_ptr<std::vector<std::string>> data;
    // throws msg if data[i] isn't valid
    void check(size_type i, const std::string &msg) const;
};
```

**StrBlob Constructors**

```cpp
StrBlob::StrBlob()
    : data(make_shared<vector<string>>()) { }
StrBlob::StrBlob(initializer_list<string> il)
    : data(make_shared<vector<string>>(il)) { }
```

**Element Access Members**

```cpp
void StrBlob::check(size_type i, const string &msg) const
{
    if (i >= data->size())
        throw out_of_range(msg);
}

string& StrBlob::front()
{
    // if the vector is empty, check will throw
    check(0, "front on empty StrBlob");
    return data->front();
}
string& StrBlob::back()
{
    check(0, "back on empty StrBlob");
    return data->back();
}
void StrBlob::pop_back()
{
    check(0, "pop_back on empty StrBlob");
    data->pop_back();
}
```

### 12.1.2 Managing Memory Directly

**Using `new` to Dynamically Allocate and Initialize Objects**

```cpp
int *pi = new int;
string *ps = new string;

int *pi = new int(1024);
string *ps = new string(10, '9');
vector<int> *pv = new vector<int>{0, 1, 2, 3, 4, 5, 6, 7, 8, 9};

string *ps1 = new string; // default initialized to the empty string
string *ps = new string(); // value initialized to the empty string
int *pi1 = new int; // default initialized; *pi1 is undefined
int *pi2 = new int(); // value initialized to 0; *pi2 is 0

auto p1 = new auto(obj);
auto p2 = new auto{a,b,c}; // error
```

**Dynamically Allocated `const` Objects**

```cpp
// Like any other const, a dynamically allocated const object must be initialized.
const int *pci = new const int(1024);
const string *pcs = new const string;
```

**Memory Exhaustion**

```cpp
// if allocation fails, new returns a null pointer
int *p1 = new int; // if allocation fails, new throws std::bad_alloc
int *p2 = new (nothrow) int; // if allocation fails, new returns a null
pointer
```

**Freeing Dynamic Memory**

```cpp
 delete p;
```

**Pointer Values and delete**
 
```cpp
int i, *pi1 = &i, *pi2 = nullptr;
double *pd = new double(33), *pd2 = pd;
delete i; // error: i is not a pointer
delete pi1; // undefined: pi1 refers to a local
delete pd; // ok
delete pd2; // undefined: the memory pointed to by pd2 was already freed
delete pi2; // ok: it is always ok to delete a null pointer

const int *pci = new const int(1024);
delete pci;
```

**Dynamically Allocated Objects Exist until They Are Freed**

```cpp
// factory returns a pointer to a dynamically allocated object
Foo* factory(T arg)
{
    // process arg as appropriate
    return new Foo(arg); // caller is responsible for deleting this memory
}

void use_factory(T arg)
{
    Foo *p = factory(arg);
    // use p but do not delete it
} // p goes out of scope, but the memory to which p points is not freed!
```

Dynamic memory managed through built-in pointers (rather than smart pointers) exists until it is explicitly freed.

```cpp
void use_factory(T arg)
{
    Foo *p = factory(arg);
    // use p
    delete p; // remember to free the memory now that we no longer need it
}

Foo* use_factory(T arg)
{
    Foo *p = factory(arg);
    // use p
    return p; // caller must delete the memory
}
```

**Resetting the Value of a Pointer after a `delete` ... Provides Only Limited Protection**

A dangling pointer is one that refers to memory that once held an object but no longer does so.

```cpp
int *p(new int(42)); // p points to dynamic memory
auto q = p; // p and q point to the same memory
delete p; // invalidates both p and q
p = nullptr; // indicates that p is no longer bound to an object
```

*** 12.1.3 Using `shared_ptr`s with `new`

```cpp
shared_ptr<double> p1;
shared_ptr<int> p2(new int(42));
```

Other Ways to Define and Change `shared_ptr`s

```cpp
shared_ptr<T> p(q) // p manages the object to which the built-in pointer q points; q must point to memory allocated by new and must be convertible to T*
shared_ptrM<T> p(u) // p assumes ownership from the unique-ptr u; makes u null
shared_ptr<T> p(q, d) // p assumes ownership for the object to which the built-in pointer q points; q must be convertible to T. p will use the callable object d in place of delete to free q
shared_ptr<T> p(p2, d) // p is a copy of the shared_ptr p2 except that p uses the callable object d in place of delete
p.reset() // If p is the only shared_ptr pointing at its object, reset frees p's existing object. If the optional built-in pointer q is passed, makes p point to q, otherwise makes p null. If d is supplied, will call d to free q otherwise uses delete to free q.
p.reset(q)
p.reset(q, d)

shared_ptr<int> p1 = new int(1024); // error: must use direct initialization
shared_ptr<int> p2(new int(1024)); // ok: use direct initialization

shared_ptr<int> clone(int p) {
    return new int(p); // error: implicit conversion to shared_ptr<int>
}

shared_ptr<int> clone(int p) {
    // ok: explicitly create a shared_ptr<int> from int*
    return shared_ptr<int>(new int(p));
}
```

**Don't Mix Ordinary Pointers and Smart Pointers ...**

```cpp
// ptr is created and initialized when process is called
void process(shared_ptr<int> ptr)
{
    // use ptr
} // ptr goes out of scope and is destroyed

shared_ptr<int> p(new int(42)); // reference count is 1
process(p); // copying p increments its count; in process the reference count is 2
int i = *p; // ok: reference count is 1

int *x(new int(1024)); // dangerous: x is a plain pointer, not a smart
pointer
process(x); // error: cannot convert int* to shared_ptr<int>
process(shared_ptr<int>(x)); // legal, but the memory will be deleted!
int j = *x; // undefined: x is a dangling pointer!
```

**...and Don't Use `get` to Initialize or Assign Another Smart Pointer**

```cpp
shared_ptr<int> p(new int(42)); // reference count is 1
int *q = p.get(); // ok: but don't use q in any way that might delete its
pointer
{ // new block
    // undefined: two independent shared_ptrs point to the same memory
    shared_ptr<int>(q);
} // block ends, q is destroyed, and the memory to which q points is freed
int foo = *p; // undefined; the memory to which p points was freed
```

**Other `shared_ptr` Operations**

```cpp
p = new int(1024); // error: cannot assign a pointer to a shared_ptr
p.reset(new int(1024)); // ok: p points to a new object

if (!p.unique())
    p.reset(new string(*p)); // we aren't alone; allocate a new copy
*p += newVal; // now that we know we're the only pointer, okay to change this object
```

### 12.1.4 Smart Pointers and Exceptions

### 12.1.5 `unique_ptr`

A `unique_ptr` "owns" the object to which it points. Unlike shared_ptr, only one `unique_ptr` at a time can point to a given object. The object to which a `unique_ptr` points is destroyed when the `unique_ptr` is destroyed.

`unique_ptr` Operations

```cpp
unique_ptr<T> u1 // Nullunique_ptrs that can point to objects of type T. u1 will use delete to free its pointer; u2 will use a callable object of type D to free its pointer
unique_ptr<T, D> u2
unique_ptr<T, D> u(d) // Null unique_ptr that point to objects of type T that uses d, which must be an object of type D in place of delete
u = nullptr // Deletes the object to which u points; makes u null
u.release() // Relinquishes control of the pointer u had held; returns the pointer u had held and makes u null
u.reset() // Deletes the object to which u points
u.reset(q) // If the built-in pointer q is supplied, makes u point to that object
u.reset(nullptr) // Otherwise makes u null
```

```cpp
unique_ptr<double> p1; // unique_ptr that can point at a double
unique_ptr<int> p2(new int(42)); // p2 points to int with value 42

unique_ptr<string> p1(new string("Stegosaurus"));
unique_ptr<string> p2(p1); // error: no copy for unique_ptr
unique_ptr<string> p3;
p3 = p2; // error: no assign for unique_ptr

// transfers ownership from p1 (which points to the string Stegosaurus) to p2
unique_ptr<string> p2(p1.release()); // release makes p1 null
unique_ptr<string> p3(new string("Trex"));
// transfers ownership from p3 to p2
p2.reset(p3.release()); // reset deletes the memory to which p2 had pointed

p2.release(); // WRONG: p2 won't free the memory and we've lost the pointer
auto p = p2.release(); // ok, but we must remember to delete(p)
```

...

## 12.2 Dynamic Arrays

```cpp

```

## 12.3 Using the Library: A Text-Query Program

```cpp

```
