# Chapter 6 Functions

## 6.2 Argument Passing

### 6.2.5 `main`: Handling Command-Line Options

```cpp
prog -d -o ofile data0
int main(int argc, char *argv[]) { ... }

argc = 5;
argv[0] = "prog"; // or argv[0] might point to an empty string
argv[1] = "-d";
argv[2] = "-o";
argv[3] = "ofile";
argv[4] = "data0";
argv[5] = 0;
```

### 6.2.6 Functions with Varying Parameters

**`initializer_list` Parameters**

[c++11] We can write a fucntion that takes an unknown number of arguments of a single type by using an `initializer_list` parameter. An `initializer_list` is a library type that represents an array of values of the specified type.

```cpp
#include <initializer_list>

// Operations on initializer_lists
initializer_list<T> lst;
initializer_list<T> lst{a, b, c...};
lst2(lst)
lst2 = lst
lst.size()
lst.begin()
lst.end()
```

Unlike `vector`, the elements in an `initializer_list` are always `const` values; there is no way to change the value of an element in an `initializer_list`.

```cpp
void error_msg(initializer_list<string> il) {
    for (auto beg = il.begin(); beg != il.end(); ++beg)
        cout << *beg << " ";
    cout << endl;
}

// expected, actual are strings
if (expected != actual)
    error_msg({"functionX", expected, actual});
else
    error_msg({"functionX", "okay"});
```

## 6.3 Return Types and the `return` Statement

### 6.3.2 Functions That Return a Value

**List Initializing the Return Value**

[c++11] Under the new standard, functions can return a braced list of values.

```cpp
vector<string> process() {
    // ...
    // expected and actual are strings
    if (expected.empty())
        return {}; // return an empty vector
    else if (expected == actual)
        return {"functionX", "okay"}; // return list-initialized vector
    else
        return {"functionX", expected, actual};
}
```

### 6.3.3 Returning a Pointer to an Array

Because we cannot copy an array, a function cannot return an array. However, a function can return a pointer or a reference to an array.

**Declaring a Function That Returns a Pointer to an Array**

```cpp
Type (*function(parameter_list))[dimension]
int (*func(int i))[10];
```

**Using a Trailing Return Type**

[c++11] Under the new standard, another way to simplify the declaration of `func` is by using a trailing return type. 

```cpp
// fcn takes an int argument and returns a pointer to an array of ten ints
auto func(int i) -> int(*)[10];
```

**Using decltype [c++11]**

```cpp
int odd[] = {1,3,5,7,9};
int even[] = {0,2,4,6,8};
//returns a pointer to an array of five int elements
decltype(odd) *arrPtr(int i) {
    return (i % 2) ?&odd : &even; // return a pointer to the array
}
```

## 6.4 Overloaded Functions

Functions that have the same name but different parameter lists and that appear in the same scope are overloaded.

```cpp
void print(const char *cp);
void print(const int *beg, const int *end);
void print(const int ia[], size_t size);
```

## 6.5 Features for Specialized Uses

### 6.5.1 Default Arguments

```cpp
typedef string::size_type sz;
string screen(sz ht = 24, sz wid = 80, char backgrnd = ' ')
```

**Calling Functions with Default Arguments**

```cpp
string window;
window = screen(); // screen(24, 90, ' ')
window = screen(66); // screen(66, 80, ' ')
window = screen(66, 256); // screen(66, 256, ' ')
window = screen(66, 256, '#') // screen(66, 256, '#')

window = screen(, , '?'); // error: can omit only trailing arguments
```

### 6.5.2 Inline and `constexpr` Functions

**`inline` Functions Avoid Function Call Overhead**

A function specified as inline (usually) is expanded "in line" at each call. The run-time overhead of making `shorterString` a function is thus removed. The `inline` mechanism is meant to optimize small, straight-line functions that are called frequently.

```cpp
cout << shorterString(s1, s2) << endl;

inline const string & shorterString(const string &s1, const string &s2) {
    return s1.size() <= s2.size() ? s1 : s2;
}
```

**`constexpr` Functions**

[c++11] A `constexpr` function is a function that can be used in a constant expression. The `return` type and the type of each parameter in a must be a literal type, and the function body must contain exactly one `return` statement.

```cpp
constexpr int new_sz() { return 42; }
constexpr int foo = new_sz(); // ok: foo is a constant expression
```

## 6.7 Pointers to Functions

```cpp
// compares lengths of two strings
bool lengthCompare(const string &, const string &);
// pf points to a function returning bool that takes two const string references
bool (*pf)(const string &, const string &);
```

**Using Function Pointers**

```cpp
pf = lengthCompare; // pf now points to the function names lengthCompare
pf = &lengthCompare; // equivalent assignment: address-of operator is optional
```

```cpp
bool b1 = pf("hello", "goodbye"); // calls lengthCompare
bool b2 = (*pf)("hello", "goodbye"); // equivalent call
bool b3 = lengthCompare("hello", "goodbye"); // equivalent call
```

**Function Pointer Parameters**

```cpp
void useBigger(const string &s1, const string &s2, 
               bool pf(const string &, const string &));
void useBigger(const string &s1, const string &s2, 
               bool (*pf)(const string &, const string &));
```

**Returning a Pointer to Function**

```cpp
using F = int(int*, int); // F is a function type, not a pointer
using PF = int(*)(int*, int); // PF is a pointer type
```

```cpp
PF f1(int); // ok: PF is a pointer to function; f1 returns a pointer to function
F f1(int); // error: F is a function type; f1 can't return a function
F *f1(int); // ok: explicitly specify that the return type is a pointer to function

int (*f1(int))(int*, int); // ok

auto f1(int) -> int (*)(int*, int); // using a trailing return
```

**Using `auto` or `decltype` from Function Pointer Types**

```cpp
string::size_type sumLength(const string&, const string&);
string::size_type largerLength(const string&, const string&);

// depending on the value of its string parameter
// getFcn returns a pointer to sumLength or to largerLength
decltype(sumLength) *getFcn(const string &);
```