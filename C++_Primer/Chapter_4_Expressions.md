# Chapter 4 Expressions

## 4.9 The sizeof Operator

The sizeof operator returns the size, in bytes, of an expression or a type name. The operator is right associative. The result of sizeof is a constant expression of type size_t. The operator takes one of two forms:

* sizeof (type)
* sizeof expr

```cpp
Sales_data data, *p;
sizeof(Sales_data) // size required to hold an object of type Sales_data
sizeof data; // size of data's type, i.e., sizeof(Sales_data)
sizeof p; // size of a pointer
sizeof *p; // size of the type to which p points, i.e., sizeof(Sales_data)
sizeof data.revenue; // size of the type of Sales_data's revenue member
sizeof Sales_data::revenue; // alternative way to get the size of revenue
```

The result of applying sizeof depends in part on the type involved:

* sizeof char or an expression of type char is guaranteed to be 1.
* sizeof a reference type returns the size of an object of the referenced type.
* sizeof a pointer returns the size needed hold a pointer.
* sizeof a dereferenced pointer returns the size of an object of the type to which the pointer points; the pointer need not be valid.
* sizeof an array is the size of the entire array. It is equivalent to taking the sizeof the element type times the number of elements in the array. Note that sizeof does not convert the array to a pointer.
* sizeof a string or a vector returns only the size of the fixed part of these types; it does not return the size used by the object's elements.

Because sizeof returns the size of the entire array, we can determine the number of elements in an array by dividing the array size by the element size:

```cpp
// sizeof(ia)/sizeof(*ia) returns the number of elements in ia
constexpr size_t sz = sizeof(ia)/sizeof(*ia);
int arr2[sz]; // ok sizeof returns a constant expression
```

Because sizeof returns a constant expression, we can use the result of a sizeof expression to specify the dimension of an array.

## 4.11 Type Conversions

### Explicit Conversions

```cpp
// Named Casts
cast-name<type>(expression);

// static_cast
// cast used to force floating-point division
double slope = static_cast<double>(j) / i;
void* p = &d; // ok: address of any nonconst object can be stored in a void*
// ok: converts void* back to the original pointer type
double *dp = static_cast<double*>(p);

// const_cast: changes only a low-level const in its operand
const char *pc;
char *p = const_cast<char*>(pc); // ok: but writing through p is undefined

const char *cp;
// error: static_cast can't cast away const
char *q = static_cast<char*>(cp);
static_cast<string>(cp); // ok: converts string literal to string
const_cast<string>(cp); // error: const_cast only changes constness

// reinterpret_cast: performs a low-level reinterpretation of the bit pattern of its operands
int *ip;
char *pc = reinterpret_cast<char*>(ip);

// Old-Style Casts
type (expr); // function-style cast notation
(type) expr; // C-language-style cast notation

char *pc = (char*) ip; // ip is a pointer to int
```