# C++17 New Features

Reference: https://en.cppreference.com/w/cpp/17

## 1 New language features

### 1.1 fold-expressions

Reduces a parameter pack over a binary operator.

```cpp
(pack op ...) // unary right fold
(... op pack) // unary left fold
(pack op ... op init) // binary right fold
(init op ... op pack) // binary left fold
```

op	-	any of the following 32 binary operators: + - * / % ^ & | = < > << >> += -= *= /= %= ^= &= |= <<= >>= == != <= >= && || , .* ->*. In a binary fold, both ops must be the same.

```cpp
template<typename... Args>
bool all(Args... args) { return (... && args); }
 
bool b = all(true, true, true, false);
 // within all(), the unary left fold expands as
 //  return ((true && true) && true) && false;
 // b is false
```

### 1.2 class template argument deduction

```cpp
std::pair p(2, 4.5);     // deduces to std::pair<int, double> p(2, 4.5);
std::tuple t(4, 3, 2.5); // same as auto t = std::make_tuple(4, 3, 2.5);
std::less l;             // same as std::less<void> l;
```

### 1.3 auto non-type template parameters

### 1.4 compile-time if constexpr

### 1.5 inline variables

### 1.6 structured bindings

### 1.7 initializers for if and switch

### 1.8 u8-char

### 1.9 simplified nested namespaces

### 1.10 using-declaration can declare multiple names

### 1.11 made noexcept part of type system

### 1.12 new order of evaluation rules

### 1.13 guaranteed copy elision

### 1.14 lambda capture of *this

### 1.15 constexpr lambda

### 1.16 attribute namespaces don't have to repeat

### 1.17 new attributes [[fallthrough]] [[nodiscard]] and [[maybe_unused]].

### 1.18 __has_include


## 2 New headers

### 2.1 `<any>`

`any` is one of the newest features of C++17 that provides a type-safe container to store single value of any type.

```cpp
#include <any> 
#include <iostream> 
#include <string> 
using namespace std; 
  
int main() 
{ 
    try { 
  
        // Integer 42:  Using the copy initialisation 
        any value = 42; 
        cout << "\n Value: "
             << any_cast<int>(value); 
  
        // Using the assignment operator 
        // to store a string 
        value = "Hello World"; 
        cout << "\n Value: "
             << any_cast<const char*>(value); 
  
        // Using the parametrized constructor 
        any val(19.0); 
        cout << " \n Value: "
             << any_cast<double>(val); 
  
        any val_brace{ string("Brace Initialisation") }; 
        cout << " \n Value: "
             << any_cast<string>(val_brace); 
    } 
  
    catch (bad_any_cast& e) { 
        cout << "\n"
             << e.what(); 
    } 
    return 0; 
} 
```

### 2.2 `<optional>`

### 2.3 `<variant>`

### 2.4 `<memory_resource>`

### 2.5 `<string_view>`

### 2.6 `<charconv>`

### 2.7 `<execution>`

### 2.8 `<filesystem>`

## 3 New library features

### 3.1 In utility

#### 3.1.1 tuple

```cpp
#include <tuple>
#include <iostream>
#include <string>
#include <stdexcept>
 
std::tuple<double, char, std::string> get_student(int id)
{
    if (id == 0) return std::make_tuple(3.8, 'A', "Lisa Simpson");
    if (id == 1) return std::make_tuple(2.9, 'C', "Milhouse Van Houten");
    if (id == 2) return std::make_tuple(1.7, 'D', "Ralph Wiggum");
    throw std::invalid_argument("id");
}
 
int main()
{
    auto student0 = get_student(0);
    std::cout << "ID: 0, "
              << "GPA: " << std::get<0>(student0) << ", "
              << "grade: " << std::get<1>(student0) << ", "
              << "name: " << std::get<2>(student0) << '\n';
 
    double gpa1;
    char grade1;
    std::string name1;
    std::tie(gpa1, grade1, name1) = get_student(1);
    std::cout << "ID: 1, "
              << "GPA: " << gpa1 << ", "
              << "grade: " << grade1 << ", "
              << "name: " << name1 << '\n';
 
    // C++17 structured binding:
    auto [ gpa2, grade2, name2 ] = get_student(2);
    std::cout << "ID: 2, "
              << "GPA: " << gpa2 << ", "
              << "grade: " << grade2 << ", "
              << "name: " << name2 << '\n';
}
```

### 3.2 In memory

### 3.3 In types

#### 3.3.1 byte

```cpp
#include <iostream>
#include <cstddef>
 
int main()
{
    std::byte b{42};
    std::cout << std::to_integer<int>(b) << "\n";
 
    // b *= 2 compilation error
    b <<= 1;
    std::cout << std::to_integer<int>(b) << "\n";
}
```

### 3.4 In algorithm

#### 3.4.2 execution policies

```cpp
class sequenced_policy { ... } // std::execution::seq
class parallel_policy { ... } // std::execution::par
class parallel_unsequencd_policy { ... } // std::execution::par_unseq
class unsequenced_policy { ... } // [c++20] std::execution::unseq
```

When using parallel execution policy, it is the programmer's responsibility to avoid data races and deadlocks:

```cpp
#include <execution>
int a[] = {0,1};
std::vector<int> v;
std::for_each(std::execution::par, std::begin(a), std::end(a), [&](int i) {
  v.push_back(i*2+1); // Error: data race
});
```

```cpp
std::atomic<int> x{0};
int a[] = {1,2};
std::for_each(std::execution::par, std::begin(a), std::end(a), [&](int) {
  x.fetch_add(1, std::memory_order_relaxed);
  while (x.load(std::memory_order_relaxed) == 1) { } // Error: assumes execution order
});
```

```cpp
int x = 0;
std::mutex m;
int a[] = {1,2};
std::for_each(std::execution::par, std::begin(a), std::end(a), [&](int) {
  std::lock_guard<std::mutex> guard(m);
  ++x; // correct
});
```

#### 3.4.4 inclusive_scan and exclusive_scan

```cpp
#include <functional>
#include <iostream>
#include <iterator>
#include <numeric>
#include <vector>
 
int main()
{
  std::vector data {3, 1, 4, 1, 5, 9, 2, 6};
 
  std::cout << "exclusive sum: ";
  std::exclusive_scan(data.begin(), data.end(),
		      std::ostream_iterator<int>(std::cout, " "),
		      0);
  std::cout << "\ninclusive sum: ";
  std::inclusive_scan(data.begin(), data.end(),
		      std::ostream_iterator<int>(std::cout, " "));
 
  std::cout << "\n\nexclusive product: ";  
  std::exclusive_scan(data.begin(), data.end(),
		      std::ostream_iterator<int>(std::cout, " "),
		      1, std::multiplies<>{});		      
  std::cout << "\ninclusive product: ";
  std::inclusive_scan(data.begin(), data.end(),
		      std::ostream_iterator<int>(std::cout, " "),
		      std::multiplies<>{});		      
}
```

### 3.5 container related

### 3.6 In numeric

### 3.7 Others