# C++14 New Features

Reference: https://en.cppreference.com/w/cpp/14

## 1 New language features

### 1.1 variable templates

```cpp
template<class T>
constexpr T pi = T(3.1415926535897932385L); // variable template

template<class T>
T circular_area(T r) { // function template 
    return pi<T> * r * r; // pi<T> is a variable instantiation
}
```

### 1.2 polymorphic lambdas

If `auto` is used as a type of a parameter, the lambda is a generic lambda.

```cpp
// generic lambda, operator() is a template with two parameters
auto glambda = [](auto a, auto &&b) { return a < b; };
bool b = glambda(3, 3.14); // ok

// generic lambda, operator() is a template with one parameter
auto vglambda = [](auto printer) {
    return [=](auto&&... ts) // generic lambda, ts is a parameter pack
    { 
        printer(std::forward<decltype(ts)>(ts)...);
        return [=] { printer(ts...); }; // nullary lambda (takes no parameters)
    };
};
auto p = vglambda([](auto v1, auto v2, auto v3) { std::cout << v1 << v2 << v3; });
auto q = p(1, 'a', 3.14); // outputs 1a3.14
q();                      // outputs 1a3.14

```

### 1.3 new/delete elision

### 1.4 relaxed restrictions on constexpr functions

### 1.5 binary literals

```cpp
// The following variables are initialized to the same value:
int d = 42;
int o = 052;
int x = 0x2a;
int X = 0X2A;
int b = 0b101010; // C++14

// The following variables are also initialized to the same value:
unsigned long long l1 = 18446744073709550592ull; // C++11
unsigned long long l2 = 18'446'744'073'709'550'592llu; // C++14
unsigned long long l3 = 1844'6744'0737'0955'0592uLL; // C++14
unsigned long long l4 = 184467'440737'0'95505'92LLU; // C++14
```

### 1.6 digit separators

### 1.7 return type deduction for functions

```cpp
int x = 1;
auto f() { return x; }        // return type is int
const auto& f() { return x; } // return type is const int&


int x = 1;
decltype(auto) f() { return x; }  // return type is int, same as decltype(x)
decltype(auto) f() { return(x); } // return type is int&, same as decltype((x))


auto f(bool val)
{
    if (val) return 123; // deduces return type int
    else return 3.14f;   // error: deduces return type float
}


auto f() {}              // returns void
auto g() { return f(); } // returns void
auto* x() {}             // error: cannot deduce auto* from void


auto sum(int i)
{
    if (i == 1)
        return i;              // sum’s return type is int
    else
        return sum(i - 1) + i; // okay: sum’s return type is already known
}


auto func () { return {1, 2, 3}; } // error
```

### 1.8 aggregate initialization for classes with brace-or-equal initializers

## 2 New library features

### 2.1 std::make_unique

Constructs an object of type `T` and wraps it in a `std::unique_ptr`.

```cpp
#include <iostream>
#include <memory>
 
struct Vec3 {
    int x, y, z;
    Vec3() : x(0), y(0), z(0) { }
    Vec3(int x, int y, int z) :x(x), y(y), z(z) { }
    friend std::ostream& operator<<(std::ostream& os, Vec3& v) {
        return os << '{' << "x:" << v.x << " y:" << v.y << " z:" << v.z  << '}';
    }
};
 
int main() {
    // Use the default constructor.
    std::unique_ptr<Vec3> v1 = std::make_unique<Vec3>();
    // Use the constructor that matches these arguments
    std::unique_ptr<Vec3> v2 = std::make_unique<Vec3>(0, 1, 2);
    // Create a unique_ptr to an array of 5 elements
    std::unique_ptr<Vec3[]> v3 = std::make_unique<Vec3[]>(5);
 
    std::cout << "make_unique<Vec3>():      " << *v1 << '\n'
              << "make_unique<Vec3>(0,1,2): " << *v2 << '\n'
              << "make_unique<Vec3[]>(5):   " << '\n';
    for (int i = 0; i < 5; i++) {
        std::cout << "     " << v3[i] << '\n';
    }
}
```

### 2.2 std::shared_timed_mutex and std::shared_lock

### 2.3 std::inteder_sequence

The class template `std::integer_sequence` represents a compile-time sequence of integers. When used as an argument to a function template, the parameter pack Ints can be deduced and used in pack expansion.

```cpp
#include <tuple>
#include <iostream>
#include <array>
#include <utility>
 
// debugging aid
template<typename T, T... ints>
void print_sequence(std::integer_sequence<T, ints...> int_seq)
{
    std::cout << "The sequence of size " << int_seq.size() << ": ";
    ((std::cout << ints << ' '),...);
    std::cout << '\n';
}
 
// Convert array into a tuple
template<typename Array, std::size_t... I>
auto a2t_impl(const Array& a, std::index_sequence<I...>)
{
    return std::make_tuple(a[I]...);
}
 
template<typename T, std::size_t N, typename Indices = std::make_index_sequence<N>>
auto a2t(const std::array<T, N>& a)
{
    return a2t_impl(a, Indices{});
}
 
// pretty-print a tuple
 
template<class Ch, class Tr, class Tuple, std::size_t... Is>
void print_tuple_impl(std::basic_ostream<Ch,Tr>& os,
                      const Tuple& t,
                      std::index_sequence<Is...>)
{
    ((os << (Is == 0? "" : ", ") << std::get<Is>(t)), ...);
}
 
template<class Ch, class Tr, class... Args>
auto& operator<<(std::basic_ostream<Ch, Tr>& os,
                 const std::tuple<Args...>& t)
{
    os << "(";
    print_tuple_impl(os, t, std::index_sequence_for<Args...>{});
    return os << ")";
}
 
 
int main()
{
    print_sequence(std::integer_sequence<unsigned, 9, 2, 5, 1, 9, 1, 6>{});
    print_sequence(std::make_integer_sequence<int, 20>{});
    print_sequence(std::make_index_sequence<10>{});
    print_sequence(std::index_sequence_for<float, std::iostream, char>{});
 
    std::array<int, 4> array = {1,2,3,4};
 
    // convert an array into a tuple
    auto tuple = a2t(array);
    static_assert(std::is_same<decltype(tuple),
                               std::tuple<int, int, int, int>>::value, "");
 
    // print it to cout
    std::cout << tuple << '\n';
 
}
```

### 2.4 std::exchange

### 2.5 std::quoted