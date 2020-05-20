# Chapter 5 Statements

## 5.6 try Blocks and Exception Handling

In C++, exception handling involves

* `throw` expressions, which the detecting part uses to indicate that it encountered something it can't handle. We say that a `throw` raises an exception.
* `try` blocks, which the handling part uses to deal with an exception. A `try` block starts with the keyword `try` and ends with one or more `catch` clauses. Exceptions thrown from code executed inside a try block are usually handled by one of the catch clauses. Because they "handle" the exception, catch clauses are also known as exception handlers.
* A set of `exception` classes that are used to pass information about what happened between a `throw` and an associated `catch`.

### 5.6.1 A throw Expression

```cpp
Sales_item item1, item2;
cin >> item1 >> item2;
// first check that item1 and item2 represent the same book
if (item1.isbn() == item2.isbn()) {
    cout << item1 + item2 << endl;
    return 0; // indicate success
} else {
    cerr << "Data must refer to same ISBN" << endl;
    return -1; // indicate failure
}
```
->

```cpp
// first check the data are for the same item
if (item1.isbn() != item2.isbn())
    throw runtime_error("Data must refer to same ISBN");
// if we're still here, the ISBNs are the same
cout << item1 + item2 << endl;
```

### 5.6.2 The try Block

```cpp
try {
    program-statements
} catch (exception-declaration) {
    handler-statements
} catch (exception-declaration) {
    handler-statements
} // ...
```

```cpp
while (cin >> item1 >> item2) {
    try {
        // execute code that will add the two Sales_items
        // if the addition fails, the code throws a runtime_error exception
    } catch (runtime_error err) {
        cout << err.what()
             << "\nTry Again ? Enter y or n" << endl;
        char c;
        cin >> c;
        if (!cin || c == 'n')
            break; // break out of the while loop
    }
}
```

### 5.6.3 Standard Exceptions

* The `exception` header defines the most general kind of exception class named `exception`. 
* The `stdexcept` header defines several general-purpose exception classes., which are lists below.

```cpp
exception
runtime_error
range_error
overflow_error
underflow_error
logic_error
domain_error
invalid_argument
length_error
out_of_range
```

* The `new` header defines the `bad_alloc` exception type.
* The `type_info` header defines the `bad_cast` exception type.