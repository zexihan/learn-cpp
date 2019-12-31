# Chapter 9 Sequential Containers

## 9.1 Overview of the Sequential Containers

```cpp
// Sequential Container Types
vector // Flexible-size array. Supports fast random access. Inserting or deleting elements other than at the back may be slow.
deque // Double-ended queue. Supports fast random access. Fast insert/delete at front or back.
list // Doubly linked list. Supports only bidirectional sequential access. Fast insert/delete at any point in the list.
forward_list // Singly linked list. Supports only sequential access in one direction. Fast insert/delete at any point in the list.
array // Fixed-size array. Supports fast random access. Cannot add or remove elements.
string // A specialized container, similar to vector, that contains characters. Fast random access. Fast insert/delete at the back.
```

## 9.2 Container Library Overview

```cpp
// Container Operations
// Type Aliases
iterator // Type of the iterator for theis container type
const_iterator // Iterator type that can read but not change its elements
size_type // Unsigned integral type big enough to hold the size of the largest possible container of this container type
difference_type // Signed integral type big enough to hold the distance between two iterators
value_type // Element type
reference // Element's lvalue type; synonym for value_type&
const_reference // Element's const lvalue type (i.e., const value_type&)

// Construction
C c; // Default constructor, empty container
C c1(c2); // Construct c1 as a copy of c2
C c(b, e); // Copy elements from the range denoted by iterators b and e
C c{a, b, c...}; // List initialize c

// Assignment and swap
c1 = c2 // Replace elements in c1 with those in c2;
c1 = {a, b, c...} // Replace elements in c1 with those in the list (not valid for array)
a.swap(b) // Swap elements in a with those in b
swap(a, b) // Equivalent to a.swap(b)

// Size
c.size() // Number of elements in c (not valid for forward_list)
c.max_size() // Maximum number of elements c can hold
c.empty() // false if c has any elements, true otherwise

// Add/Remove Elements (not valid for array)
c.insert(args) // Copy element(s) as specified by args into c
c.emplace(inits) // Use inits to construct an element in c
c.erase(args) // Remove element(s) specified by args
c.clear() // Remove all elements from c; returns void

// Equality and Relational Operators
==, != // Equality valid for all container types
<, <=, >, >= // Relationals (not valid for unordered associative containers)

// Obtain Iterators
c.begin(), c.end() // Return iterator to the first, one past the last element in c
c.cbegin(), c.cend() // Return const_iterator

// Additional Members of Reversible Containers (not valid for forward_list)
reverse_iterator // Iterator that addresses elements in reverse order
const_reverse_iterator // Reverse iterator that cannot write the elements
c.rbegin(), c.rend() // Return iterator to the last, one past the first element in c
c.crbegin, c.crend() // Return const_reverse_iterator
```

```cpp
// Defining and Itializing Containers
C c; // Default constructor. If C is array, then the elements in c are default-initialized; otherwise c is empty.
C c1(c2) // c1 is a copy of c2. c1 and c2 must have the same type (i.e., they must be the same container type and hold the same element type; for array must also have the same size).
C c1 = c2 
C c{a, b, c...} // c is a copy of the elements in the initializer list. Type of elements in the list must be compatible with the element type of C. For array, the list must have same number or fewer elements than the size of the array, any missing elements are value-initialized.
C c = {a, b, c...}
C c(b, e) // c is a copy of the elements in the range denoted by iterators b and e. Type of the elements must be compatible with the element type of C. (Not valid for array.)

// Constructors that take a size are valid for sequential containers (not including array only)
C seq(n) // seq has n value-initialized elements; this constructor is explicit (not valid for string.)
C seq(n, t) // seq has n elements with value t.
```

```cpp
list<Sales_data>
deque<double>

vector<vector<string>> lines; // vector of vectors

// assume noDefault is a type without a default constructor
vector<noDefault> v1(10, init); // ok: element initializer supplied
vector<noDefault> v2(10); // error: must supply an element initialier
```

### Iterators

**Requirements on Iterators Forming an Iterator Range**

Two iterators, begin and end, form an iterator range, if
* They refer to elements of, or one past the end of, the same container, and
* It is possible to reach end by incrementing begin. In other words, end must not precede begin.

```cpp
while (begin != end) {
    *begin = val; // ok: range isn't empty so begin denotes an element
    ++begin; // advance the iterator to get the next element
}
```

### Container Type Members

```cpp
// iter is the iterator type defined by list<string>
list<string>::iterator iter;

// count is the difference_type defined by vector<int>
vector<int>::difference_type count;

// begin and end Members
list<string> a = {"Milton", "Shakespeare", "Austen"};
auto it1 = a.begin(); // list<string>::iterator
auto it2 = a.rbegin(); // list<string>::reverse_iterator
auto it3 = a.cbegin(); // list<string>::const_iterator
auto it3 = a.crbegin(); // list<string>::const_reverse_iterator

// type is explicitly specified
list<string>::iterator it5 = a.begin();
list<string>::const_iterator it6 = a.begin();
// iterator or const_iterator depending on a's type of a
auto it7 = a.begin(); // const_iterator only if a is const
auto it8 = a.cbegin(); // it 8 is const_iterator
```

When write access is not needed, use cbegin and cend.

```cpp
// Defining and Initializing a Container
// each container has three elements, initialized from the given initializers
list<string> authors = {"Milton", "Shakespeare", "Austen"};
vector<const char*> articles = {"a", "an", "the"};
list<string> list2(authors); // ok: types match
deque<string> authList(authors); // error: container types don't match
vector<string> words(articles); // error: element types must match
// ok: converts const char* elements to string
forward_list<string> words(artivles.begin(), articles.end());

// copies up to but not including the element denoted by it
deque<string> authList(authors.begin(), it);

vector<int> ivec(10, -1); // ten int elements, each initialized to -1
list<string> svec(10, "hi!"); // ten strings; each element is "hi!"
forward_list<int> ivec(10); // ten elements, each initialized to 0
deque<string> svec(10); // ten elements, each an empty string

// Library arrays have Fixed Size
array<int, 42> // type is: array that holds 42 ints
array<string, 10> // type is: array that holds 10 strings

array<int, 10>::size_type i; // array type includes element type and size
array<int>::size_type j; // error: array<int> is not a type

array<int, 10> ia1; // ten default-initialized ints
array<int, 10> ia2 = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9}; // list initialization
array<int, 10> ia3 = {42}; // ia3[0] is zero, remaining elements are 0

int digs[10] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
int cpy[10] = digs; // error: no copy or assignment for built-in arrays
array<int, 10> digits = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
array<int, 10> copy = digits; // ok: so long as array types match
```

```cpp
// Assignment and swap
c1 = c2; // replace the contents of c1 with a copy of the elements in c2
c1 = {a, b, c}; // after the assignment c1 has size 3

// Container Assignment Operations
c1 = c2 // Replace the elements in c1 with copies of the elements in c2. c1 and c2 must be the same type.
c = {a, b, c...} // Replace the elements in c1 with copies of the elements in the initializer list. (Not valid for array.)
swap(c1, c2) // Exchange elements in c1 with those in c2. c1 and c2 must be the same type. swap is usually much faster than copying elements from c2 to c1.
c1.swap(c2)

// asign operations not valid for associative containers or array
seq.assign(b, e) // Replace elements in seq with those in the range denoted by iterators
seq.assign(i1) // Replace the elements in seq with those in the initializer list i1.
seq.assign(n, t) // Replace the elements in seq with n elements with value t.

array<int, 10> a1 = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
array<int, 10> a2 = {0}; // elements all have value 0
a1 = a2; // replaces elements in a1
a2 = {0}; // error: cannot assign to an array from a braced list
```

```cpp
// Using assign (sequential Containers Only)
list<string> names;
vector<const char*> oldstyle;
names = oldstyle; // error: container types don't match
// ok: can convert from const char* to string
names.assign(oldstype.cbegin(), oldstyle.cend());

// equivalent to slist1.clear()
// followed by slist1.insert(slist1.begin(), 10, "Hiya!");
list<string> slist1(1); // one element, which is the empty string
slist1.assign(10, "Hiya!"); // ten elements; each one is Hiya!

// Using swap
vector<string> svec1(10); // vector with ten elements
vector<string> svec2(24); // vector with 24 elements
swap(svec1, svec2);
```

```cpp
// Relational Operators
vector<int> v1 = { 1, 3, 5, 7, 9, 12 };
vector<int> v2 = { 1, 3, 9 };
vector<int> v3 = { 1, 3, 5, 7 };
vector<int> v4 = { 1, 3, 5, 7, 9, 12 };
v1 < v2 // true; v1 and v2 differ at element [2]: v1[2] is less than v2[2]
v1 < v3 // false; all elements are equal, but v3 has fewer of them;
v1 == v4 // true; each element is equal and v1 and v4 have the same size()
v1 == v2 // false; v2 has fewer elements than v1

vector<Sales_data> storeA, storeB;
if (storeA < storeB) // error: Sales_data has no less-than operator
```

## Sequential Container Operations
