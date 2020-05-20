# Chapter 4 Expressions

## 4.4 Assignment Operators

[c++11] We can use a braced initializer list on the right-hand side:

```cpp
int k = 0;
k = 3.14159; // result: type int, value 3
k = {3.14}; // error: narrowing conversion
vector<int> vi; // initially empty
vi = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9}; // vi now has ten elements, values 0 throught 9
```

**Assignment Is Right Associative**

```cpp
int ival, jval;
ival = jval = 0; // ok: each assigned 0
```

**Assignment Has Low Precedence**

```cpp
int i;
while ((i = get_value()) != 42) {
    // do something ...
}
```

## 4.5 Increment and Decrement Operators

**Combining Dereference and Increment in a Single Expression**

```cpp
auto pbeg = v.begin();
while (pbeg != v.end() && *beg >= 0) 
    cout << *pbeg++ << endl;
```

The precedence of postfix increment is higher than that of the dereference operator, so `*pbeg++` is equivalent to `*(pbeg++)`. The subexpression `pbeg++` increments pbeg and yields a copy of the previous value of pbeg as its result. Accordingly, the operand of `*` is the unincremented value of `pbeg`. Thus, the statement prints the element to which pbeg originally pointed and increments `pbeg`.

## 4.6 The Member Access Operators

```cpp
string s1 = "a string", *p = &s1;
auto n = s1.size(); // run the size member of the string s1
n = (*p).size(); // run size on the object to which p points
n = p->size(); // equivalent to (*p).size()

*p.size(); // error: p is a pointer and has no member named size
```

## 4.7 The Conditional Operator

```cpp
string finalgrade = (grade < 60) ? "fail" : "pass";
```

## 4.8 The Bitwise Operator

```cpp
// Bitwise Operators (Left Associative)
~ // bitwise NOT, ~expr
<< // left shift, expr1 << expr2
>> // right shift, expr1 >> expr2
& // bitwise AND, expr1 & expr2
^ // bitwise XOR, expr1 ^ expr2
| // bitwise OR, expr1 | expr2
```

**Using Bitwise Operators**

```cpp
unsigned long quiz1 = 0; // 32 bits
1UL << 27 // generate a value with only bit number 27 set
quiz1 |= 1UL << 27; // indicate student number 27 passed
quiz1 &= ~(1UL << 27); // student number 27 failed
```

## 4.9 The sizeof Operator

The `sizeof` operator returns the size, in bytes, of an expression or a type name. The operator is right associative. The result of `sizeof` is a constant expression of type `size_t`. The operator takes one of two forms:

* sizeof (type)
* sizeof expr

```cpp
Sales_data data, *p;
sizeof(Sales_data) // size required to hold an object of type Sales_data
sizeof data; // size of data's type, i.e., sizeof(Sales_data)
sizeof p; // size of a pointer
sizeof *p; // size of the type to which p points, i.e., sizeof(Sales_data)
[c++11] sizeof data.revenue; // size of the type of Sales_data's revenue member
[c++11] sizeof Sales_data::revenue; // alternative way to get the size of revenue
```

[c++11] The result of applying sizeof depends in part on the type involved:

* `sizeof` char or an expression of type char is guaranteed to be 1.
* `sizeof` a reference type returns the size of an object of the referenced type.
* `sizeof` a pointer returns the size needed hold a pointer.
* `sizeof` a dereferenced pointer returns the size of an object of the type to which the pointer points; the pointer need not be valid.
* `sizeof` an array is the size of the entire array. It is equivalent to taking the sizeof the element type times the number of elements in the array. Note that sizeof does not convert the array to a pointer.
* `sizeof` a string or a vector returns only the size of the fixed part of these types; it does not return the size used by the object's elements.

Because `sizeof` returns the size of the entire array, we can determine the number of elements in an array by dividing the array size by the element size:

```cpp
// sizeof(ia)/sizeof(*ia) returns the number of elements in ia
constexpr size_t sz = sizeof(ia)/sizeof(*ia);
int arr2[sz]; // ok sizeof returns a constant expression
```

Because sizeof returns a constant expression, we can use the result of a sizeof expression to specify the dimension of an array.


## 4.10 Comma Operator

```cpp
vector<int>::size_type cnt = ivec.size();
for (vector<int>::size_type ix = 0; ix != ivec.size(); ++ix, --cnt)
    ivec[ix] = cnt;
```

## 4.11 Type Conversions

### 4.11.3 Explicit Conversions

**Named Casts**

`cast-name<type>(expression);`

```cpp
// static_cast
// cast used to force floating-point division
double slope = static_cast<double>(j) / i;
void* p = &d; // ok: address of any nonconst object can be stored in a void*
// ok: converts void* back to the original pointer type
double *dp = static_cast<double*>(p);
```

```cpp
// const_cast
// changes only a low-level const in its operand
const char *pc;
char *p = const_cast<char*>(pc); // ok: but writing through p is undefined

const char *cp;
// error: static_cast can't cast away const
char *q = static_cast<char*>(cp);
static_cast<string>(cp); // ok: converts string literal to string
const_cast<string>(cp); // error: const_cast only changes constness
```

```cpp
// reinterpret_cast
// performs a low-level reinterpretation of the bit pattern of its operands
int *ip;
char *pc = reinterpret_cast<char*>(ip);
```

```cpp
// Old-Style Casts
type (expr); // function-style cast notation
(type) expr; // C-language-style cast notation

char *pc = (char*) ip; // ip is a pointer to int
```
