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

## 9.3 Sequential Container Operations

### Adding Elements to a Sequential Container

```cpp
// Operations That Add Elements to a Sequential Container
c.push_back(t) // Creates an element with value t or constructed from args at the end of c. Returns void.
c.emplace_back(args)
c.push_front(t) // Creates an element with value t or constructed from args on the front of c. Returns void.
c.emplace_front(args)
c.insert(p, t) // Creates an element with value t or constructed from args before the element denoted by iterator p. Returns an iterator referring to the element that was added.
c.emplace(p, args)
c.insert(p, n, t) // Inserts n elements with value t before the element denoted by iterator p. Returns an iterator to the first element inserted; if n is zero, returns p.
c.insert(p, b, e) // Inserts the elements from the range denoted by iterators b and e before the element denoted by iterator p. b and e may not refer to elements in c. Returns an iterator to the first element inserted; if the range is empty, returns p.
c.insert(p, i) // i1 is a braced list of element values. Inserts the given values before the element denoted by the iterator p. Returns an iterator to the first inserted element; if the list is empty returns p.
```

```cpp
// Using push_back
// read from standard input, putting each word onto the end of container
string word;
while (cin >> word)
    container.push_back(word);

void pluralize(size_t cnt, string &word)
{
    if (cnt > 1)
        word.push_back('s'); // same as word += 's'
}

// Using push_front
list<int> ilist;
// add elements to the start of ilist
for (size_t ix = 0; ix != 4; ++ix)
    ilist.push_front(ix);


// Adding Elements at a Specified Point in the Container
slist.insert(iter, "Hello!"); // insert "Hello!" just before iter

vector<string> svec;
list<string> slist;
// equivalent to calling slist.pushfront("Hello!");
slist.insert(slist.begin(), "Hello!");
// no push_front on vector but we can insert before begin()
// warning: inserting anywhere but at the end of a vector might be slow
svec.insert(svec.begin(), "Hello!");


// Inserting a Range of Elements
svec.insert(svec.end(), 10, "Anna");

vector<string> v = {"quasi", "simba", "frollo", "scar"};
// insert the last two elements of v at the beginning of list
slist.insert(slist.begin(), v.end() - 2, v.end());
slist.insert(slist.end(), {"these", "words", "will", "go", "at", "the", "end"});
// run-time error: iterators denoting the range to copy from
// must not refer to the same container as the one we are changing
slist.insert(slist.begin(), slist.begin(), slist.end());

// Under the new standard, the versions of insert that take a count or a range return an iterator to the first element that was inserted.
// Using the Return from insert
list<string> lst;
auto iter = lst.begin();
while (cin >> word)
    iter = lst.insert(iter, word); // same as calling push_front


// Using the Emplace Operations
// assuming c holds Sales_data elements
// construct a Sales_data object at the end of c
// uses the three-argument Sales_data constructor
c.emplace_back("978-0590353403", 25, 15.99);
// error: there is no version of push_back that takes three arguments
c.push_back("978-0590353403", 25, 15.99);
// ok: we create a tempoary Sales_data object to pass to push_back
c.push_back(Sales_data("978-0590353403", 25, 15.99));

// iter refers to an element in c, which holds Sales_data elements
c.emplace_back(); // uses the Sales_data default constructor
c.emplace(iter, "999-999999999"); // uses Sales_data(string)
// uses the Sales_data constructor that takes an ISBN, a count, and a price
c.emplace_front("978-0590353403", 25, 15.99);
```

### Accessing Elements

```cpp
// Operations to Access Elements in a Sequential Container
// at and subscript operator valid only for string, vector, deque, and array. back not valid for forward_list.
c.back() // Returns a reference to the last element in c. Undefined if c is empty.
c.front() // Returns a reference to the first element in c. Undefined if c is empty.
c[n] // Returns a reference to the element indexed by the unsigned integral value n. Undefined if n >= c.size().
c.at(n) // Returns a reference to the element indexed by n. If the index is out of range, throws an out_of_range exception.
```

```cpp
// check that there are elements before dereferencing an iterator or calling front or back
if (!c.empty()) {
    // val and val2 are copies of the value of the first element in c
    auto val = *c.begin(), val2 = c.front();
    // val3 and val4 are copies of the last element in c
    auto last = c.end();
    auto val3 = *(--last); // can't decrement forward_list iterators
    auto val4 = c.back(); // not supported by forward_list
}


// The Access Members Return References
if (!c.empty()) {
    c.front() = 42; // assigns 42 to the first element in c
    auto &v = c.back(); // get a reference to the last element
    v = 1024; // changes the element in c
    auto v2 = c.back(); // v2 is not a reference; it's a copy of c.back()
    v2 = 0; // no change to the element in c
}


// Subscripting and Safe Random Access
vector<string> svec; // empty vector
cout << svec[0]; // run-time error: there are no elements in svec!
cout << svec.at[0]; // throws an out_of_range exception
```

### Erasing Elements

```cpp
// erase Operations on Sequential Containers
c.pop_back() // Removes last element in c. Undefined if c is empty. Returns void.
c.pop_front() // Removes first element in c. Undefined if c is empty. Returns void.
c.erase(p) // Removes the element denoted by the iterator p and returns an iterator to the element after the one deleted or the off-the-end iterator if p denotes the last element. Undefined if p is the off-the-end iterator.
c.erase(b, e) // Removes the range of elements denoted by the iterators b and e. Returns an iterator to the element after the last one that was deleted, or an off-the-end iterator if e is itself an off-the-end iterator.
c.clear() // Removes all the elements in c. Returns void.


// The pop_front and pop_back Members
while (!ilist.empty()) {
    process(ilist.front()); // do something with the current top of ilist
    ilist.pop_front(); // done; remove the first element
}


// Removing an Element from within the Container
list<int> lst = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
auto it = lst.begin();
while (it != lst.end())
    if (*it % 2) // if the element is odd
        it = lst.erase(it); // erase this element
    else
        ++it;


// Removing Multiple Elements
// delete the range of elements between two iterators
// returns an iterator to the element just after the last removed element
elem1 = slist.erase(elem1, elem2); // after the call elem1 == elem2

slist.clear(); // delete all the elements within the container
slist.erase(slist.begin(), slist.end()); // equivalent
```

## 9.4 How a vector Grows

## 9.5 Additional string Operations

## 9.6 Container Adaptors
