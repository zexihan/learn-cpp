# Chapter 2 Variables and Basic Types

## 2.1 Primitive Built-in Types

### 2.1.1 Arithmetic Types

```cpp
bool // NA

char // 8 bits
wchar_t // 16 bits
char16_t // 16 bits
char32_t // 32 bits

short // 16 bits
int // 16 bits
long // 32 bits
long long // 64 bits [c++11]

float // 6 significant digits
double // 10 significant digits
long double // 10 significant digits
```

**Signed and Unsigned Types**

```cpp
unsigned int // equivalent to unsigned
unsigned long
unsigned long long

char
signed char // 8-bit, holds values from -127 to 127 (or from -128 to 128)
unsigned char // 8-bit, holds values from 0 to 255 inclusive
```

### 2.1.2 Type Conversions

Don't mix siugned and unsigned types.

### 2.1.3 Literals

A value is known as a literal because its value self-evident.

**Integer and Floating-Point Literals**

```cpp
20 // decimal
024 // octal
0x14 // hexadecimal
```

We can override the default types of literals by using a suffix.

```cpp
// Character and Character String Literals
// Prefix
u // char16_t
U // Char32_t
L // wchar_t
u8 // string literals only, char

// Integer Literals
// Suffix
u or U // unsigned
l or L // long
ll or LL // long long

// Floating-Point Literals
f or F // float
l or L // long double
```

Using scientific notation, the exponent is indicated by either E or e.

By default, floating-point literals have type double.

**Character and Character String Literals**

```cpp
'a' // character literal
"Hello World!" // string literal
```

The type of a string literal is array of constant chars. The compiler appends a null character('\0') to every string literal.

```cpp
// multiline string literal
std::cout << "a really, really long string literal "
             "that spans two lines" << std::endl;
```

## 2.2 Variables

### 2.2.1 Variable Definitions

**List Initialization** [c++11]

```cpp
int units_sold = 0;
int units_sold = {0};
int units_sold{0};
int units_sold(0);

long double ld = 3.1415926536;
int a{ld}, b = {ld}; // error: narrowing conversion required
int c(ld), d - ld;
```

***Default Initialization*

When we define a variable without an initializaer, the variable is default initialized.

```cpp
std::string empty; // empty implicitly initialized to the empty string
Sales_item item; // default-initialized Sales_item object
```

### 2.2.2 Variable Declaractions and Definitions

A declaration makes a name known to the program. A file that wants to use a name defined elsewhere includes a declaration for that name. A definition creats the associated entity.

```cpp
extern int i; // declares but does not define i
int j; // declares and defines j

extern double pi = 3.1416; // definition
```

## 2.3 Compound Types

### 2.3.1 References

```cpp
int ival = 1024;
int &refVal = ival; // refVal refers to (is another name for) ival
int &refVal2; // error: a reference must be initialized
```

### 2.3.2 Pointers

```cpp
int *ip1, *ip2; // both ip1 and ip2 are pointers to int
double dp, *dp2; // dp2 is a pointer to double; dp is a double
```

**Taking the Address of an Object**

```cpp
int ival = 42;
int *p = &ival; // p holds the address of ival; p is a pointer to ival
```

**Pointer Value**

The value (i.e., the address) stored in a pointer can be in one of four states:

1. It can point to an object.
2. It can point to the location just immediately past the end of an object.
3. It can be a null pointer, indicating that it is not bound to any object.
4. It can be invalid; values other than the preceding three are invalid.

**Using a Pointer to Access an Object**

We may dereference only a valid pointer that points to an object.

```cpp
int ival = 42;
int *p = &ival;
cout << *p;

*p = 0;
cout << *p;
```

**Null Pointers**

A null pointer does not point to any object. Code can check whether a pointer is null before attempting to use it.

```cpp
int *p1 = nullptr; // equivalent to int *p1 = 0; [c++11]
int *p2 = 0;
// must #include cstdlib
int *p3 = NULL;
```

**void\* Pointers**

A void* pointer holds an address, but the type of the object at that address is unknown.

```cpp
double obj = 3.14, *pd = *obj;
void *pv = &obj;
pv = pd;
```

There are only a limited number of things we can do with a void* pointer: We can compare it to another pointer, we can pass it to or return it from a function, and we can assign it to another void* pointer. We cannot use a void* to operate on the object it addresses--we don't know that object's type, and the type determines what operations we can perform on the object.

## 2.4 const Qualifier

```cpp
const int bufSize = 512; // input buffer size
bufSize = 512; // error: attempt to write to const object

const int i = get_size(); // ok: initialized at run time
const int j = 42; // ok: initialized at compile time
const int k; // error: k is uninitialized const

// Initialization and const
int i = 42;
const int ci = i; // ok: the value in i is copied into ci
int j = ci; // ok: the value in ci is copied into j

// By Default, const Objects Are Local to a File
// file_1.cc defines and initializes a const that is accessible to other files
extern const int bufSize = fcn();
// file_1.h
extern const int bufSize; // same bufSize as defined in file_1.cc
```

### 2.4.1 References to const

As with any other object, we can bind a reference to an object of a const type. To do so we use a reference to const, which is a reference that refers to a const type. Unlike an ordinary reference, a reference to const cannot be used to change the object to which the reference is bound:

```cpp
const int ci = 1024;
const int &r1 = ci; // ok: both reference and underlying object are const
r1 = 42; // error: r1 is a reference to const
int &r2 = ci; // error: non const reference to a const object

// Terminology: const Reference is a Reference to const

// Initialization and References to const
int i = 42;
const int &r1 = i; // we can bind a const int& to a plain int object
const int &r2 = 42; // ok: r1 is a reference to const
const int &r3 = r1 * 2; // ok: r3 is a reference to const
int &r4 = r * 2; // error: r4 is a plain, non const reference

// A Reference to const May Refer to an Object That Is Not const
// It is important to realize that a reference to const restricts only what we can do through that reference.
int i = 42;
int &r1 = i; // r1 bound to i
const int &r2 = i; // r2 also bound to i; but cannot be used to change i
r1 = 0; // r1 is not const; i is now 0
r2 = 0; // error: r2 is a reference to const
```

### 2.4.2 Pointers and const

As with references, we can define pointers that point to either const or nonconst types. Like a reference to const, a pointer to const (§ 2.4.1, p. 61) may not be used to change the object to which the pointer points. We may store the address of a const object only in a pointer to const:

```cpp
const double pi = 3.14; // pi is const; its value may not be changed
double *ptr = &pi; // error: ptr is a plain pointer
const double *cptr = &pi; // ok: cptr may point to a double that is const
*cptr = 42; // error: cannot assign to *cptr

double dval = 3.14; // dval is a double; its value can be changed
cptr = &dval; // ok: but can't change dval through cptr
```

### 2.4.3 Top-Level const

We use the term top-level const to indicate that the pointer itself is a const. When a pointer can point to a const object, we refer to that const as a low-level const.

```cpp
int i = 0;
int *const p1 = &i; // we can't change the value of p1; const is top-level
const int ci = 42; // we cannot change ci; const is top-level
const int *p2 = &ci; // we can change p2; const is low-level
const int *const p3 = p2; // right-most const is top-level, left-most is not
const int &r = ci; // const in reference types is always low-level

i = ci; // ok: copying the value of ci; top-level const in ci is ignored
p2 = p3; // ok: pointed-to type matches; top-level const in p3 is ignored
```

### 2.4.4 constexpr and Constant Expressions

A constant expression is an expression whose value cannot change and that can be evaluated at **compile time**. A **literal** is a constant expression. A const object that is initialized from a constant expression is also a constant expression. 

```cpp
const int max_files = 20; // max_files is a constant expression
const int limit = max_files + 1; // limit is a constant expression
int staff_size = 27; // staff_size is not a constant expression
const int sz = get_size(); // sz is not a constant expression
```

**[c++11] constexpr Variables:** Under the new standard, we can ask the compiler to verify that a variable is a constant expression by declaring the variable in a constexpr declaration. Variables declared as constexpr are implicitly const and must be initialized by constant expressions:

```cpp
constexpr int mf = 20; // 20 is a constant expression
constexpr int limit = mf + 1; // mf + 1 is a constant expression
constexpr int sz = size(); // ok only if size is a constexpr function
```

Generally, it is a good idea to use constexpr for variables that you intend to use as constant expressions.

**Pointers and constexpr** 

It is important to understand that when we define a pointer in a constexpr declaration, the constexpr specifier applies to the pointer, not the type to which the pointer points:

```cpp
const int *p = nullptr; // p is a pointer to a const int
constexpr int *q = nullptr; // q is a const pointer to int
```

constexpr imposes a top-level const on the objects it defines.

```cpp
constexpr int *np = nullptr; // np is a constant pointer to int that is null
int j = 0;
constexpr int i = 42; // type of i is const int

// i and j must be defined outside any function
constexpr const int *p = &i; // p is a constant pointer to the const int i
constexpr int *p1 = &j; // p1 is a constant pointer to the int j
```

## 2.5 Dealing with Types

### 2.5.1 Type Aliases

The type alias is a name that is a synonym for another type.

```cpp
// type alias
typedef double wages; // wages is a synonym for double
typedef wages base, *p; // base is a synonym for double, p for double*
```

[c++11] introduced a second way to define a type alias, via a alias declaration:

```cpp
// alias declaration
using SI = Sales_item; // SI is a synonyn for Sales_item
```

```cpp
wages hourly, weekly; // same as double hourly, weekly
SI item; // same as Sales_item item
```

**Pointers, const, and Type Aliases**

```cpp
// Pointers, const, and Type Aliases
typedef char *pstring;
const pstring cstr = 0; // cstr is a constant pointer to char
const pstring *ps; // ps is a pointer to a constant pointer to char
```

### 2.5.2 The auto Type Specifier [c++11]

```cpp
// the type of item is deduced from the type of the result of addingg val1 and val2
auto item = val1 + val2; // item initialized to the result of val1 + val2

auto i = 0, *p = &i; // ok: i is int and p is a pointer to int
auto sz = 0, pi = 3.14; // error: inconsistent types for sz and pi
```

**Compound Types, const, and auto**

```cpp
int i = 0, &r = i;
auto a = r; // a is an int (r is an alias for i, which has type int)

const int ci = i, &cr = ci;
auto b = ci; // b is an int (top-level const in ci is dropped)
auto c = cr; // c is an int (cr is an alias for ci whose const is top-level)
auto d = &i; // d is an int*(& of an int object is int*)
auto e = &ci; // e is const int*(& of a const object is low-level const)

const auto f = ci; // deduced type of ci is int; f has type const int

auto &g = ci; // g is a const int& that is bound to ci
auto &h = 42; // error: we can't bind a plain reference to a literal
const auto &j = 42; /// ok: we can bind a const reference to a literal

auto k = ci, &l = i; // k is int; l is int&
auto &m = ci, *p = &ci; // m is a const int&; p is a pointer to const int
// error: type deduced from i is int; type deduced from &ci is const int
auto &n = i, *p2 = &ci;
```

### 2.5.3 The decltype Type Specifier [c++11]

```cpp
decltype(f()) sum = x; // sum has whatever type f returns

const int ci = 0, &cj = ci; 
decltype(ci) x = 0; // x has type const int
decltype(cj) y = x; // y has type const int& and is bound to x
decltype(cj) z; // error: z is a reference and must be initializeds
```

**decltype and References**

```cpp
// decltype of an expression can be a reference type
int i = 42, *p = &i, &r = i;
decltype(r + 0) b; // ok: addition yields an int; b is an (uninitialized) int
decltype(*p) c; // error: c is int& and must be initialized

// decltype of a parenthesized variable is always a reference
decltype((i)) d; // error: d is int& and must be initialized
decltype(i) e; // ok: e is an (uninitialized) int
```

## 2.6 Defining Our Own Data Structures

### 2.6.1 Defining the Sales_data Type

```cpp
struct Sales_data {
    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
};

struct Sales_data {/* ... */} accum, trans, *salesptr;
// equivalent, but better way to define these objects
struct Sales_data {/* ... */};
Sales_data accum, trans, *salesptr;
```

**Class Data Members**

[c++11] When we create objects, the in-class initializers will be used to initialize the data members. Members without an initializer are default initialized.

In-class initializers must either be enclosed inside curly breaces or follow an = sign. We may not specify an in-class initializer inside parentheses.

### 2.6.2 Using the Sales_data Class

```cpp
#include <iostream>
#incldue <string>
#include "Sales_data.h"
int main() {
    Sales_data data1, data2;
    double price = 0;
    std::cin >> data1.bookNo >> data1.units_sold >> price;
    data1.revenue = data1.units_sold * price;
}
```

### 2.6.3 Writing Our Own Header Files

```cpp
#ifndef SALES_DATA_H
#define SALES_DATA_H
#inclde <string>
struct Sales_data {
    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
};
#endif
```