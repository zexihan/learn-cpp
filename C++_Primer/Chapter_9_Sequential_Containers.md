# Chapter 9 Sequential Containers

## 9.1 Overview of the Sequential Containers

```cpp
// Sequential Container Types
vector // Flexible-size array. Supports fast random access. Inserting or deleting elements other than at the back may be slow.
deque // Double-ended queue. Supports fast random access. Fast insert/delete at front or back.
list // Doubly linked list. Supports only bidirectional sequential access. Fast insert/delete at any point in the list.
forward_list // [c++11] Singly linked list. Supports only sequential access in one direction. Fast insert/delete at any point in the list.
array // [c++11] Fixed-size array. Supports fast random access. Cannot add or remove elements.
string // A specialized container, similar to vector, that contains characters. Fast random access. Fast insert/delete at the back.
```

## 9.2 Container Library Overview

**Container Operations**

```cpp
// Type Aliases
iterator // Type of the iterator for theis container type
const_iterator // Iterator type that can read but not change its elements
size_type // Unsigned integral type big enough to hold the size of the largest possible container of this container type
difference_type // Signed integral type big enough to hold the distance between two iterators
value_type // Element type
reference // Element's lvalue type; synonym for value_type&
const_reference // Element's const lvalue type (i.e., const value_type&)
```

```cpp
// Construction
C c; // Default constructor, empty container
C c1(c2); // Construct c1 as a copy of c2
C c(b, e); // Copy elements from the range denoted by iterators b and e
C c{a, b, c...}; // List initialize c
```

```cpp
// Assignment and swap
c1 = c2 // Replace elements in c1 with those in c2;
c1 = {a, b, c...} // Replace elements in c1 with those in the list (not valid for array)
a.swap(b) // Swap elements in a with those in b
swap(a, b) // Equivalent to a.swap(b)
```

```cpp
// Size
c.size() // Number of elements in c (not valid for forward_list)
c.max_size() // Maximum number of elements c can hold
c.empty() // false if c has any elements, true otherwise
```

```cpp
// Add/Remove Elements (not valid for array)
c.insert(args) // Copy element(s) as specified by args into c
c.emplace(inits) // Use inits to construct an element in c
c.erase(args) // Remove element(s) specified by args
c.clear() // Remove all elements from c; returns void
```

```cpp
// Equality and Relational Operators
==, != // Equality valid for all container types
<, <=, >, >= // Relationals (not valid for unordered associative containers)
```

```cpp
// Obtain Iterators
c.begin(), c.end() // Return iterator to the first, one past the last element in c
c.cbegin(), c.cend() // Return const_iterator
```

```cpp
// Additional Members of Reversible Containers (not valid for forward_list)
reverse_iterator // Iterator that addresses elements in reverse order
const_reverse_iterator // Reverse iterator that cannot write the elements
c.rbegin(), c.rend() // Return iterator to the last, one past the first element in c
c.crbegin, c.crend() // Return const_reverse_iterator
```

**Defining and Itializing Containers**

```cpp
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

### 9.2.1 Iterators

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

### 9.2.2 Container Type Members

```cpp
// iter is the iterator type defined by list<string>
list<string>::iterator iter;

// count is the difference_type defined by vector<int>
vector<int>::difference_type count;
```

### 9.2.3 `begin` and `end` Members

```cpp
list<string> a = {"Milton", "Shakespeare", "Austen"};
auto it1 = a.begin(); // list<string>::iterator
auto it2 = a.rbegin(); // list<string>::reverse_iterator
auto it3 = a.cbegin(); // list<string>::const_iterator
auto it3 = a.crbegin(); // list<string>::const_reverse_iterator
```

[c++11]

```cpp
// type is explicitly specified
list<string>::iterator it5 = a.begin();
list<string>::const_iterator it6 = a.begin();
// iterator or const_iterator depending on a's type of a
auto it7 = a.begin(); // const_iterator only if a is const
auto it8 = a.cbegin(); // it 8 is const_iterator
```

When write access is not needed, use cbegin and cend.

### 9.2.4 Defining and Initializing a Container

```cpp
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
```

**List Initialization [c++11]**

```cpp
list<string> authors = {"Milton", "Shakespeare", "Austen"};
vector<const char*> articles = {"a", "an", "the"};
```

**Sequential Container Size-Related Constructors**

```cpp
vector<int> ivec(10, -1); // ten int elements, each initialized to -1
list<string> svec(10, "hi!"); // ten strings; each element is "hi!"
forward_list<int> ivec(10); // ten elements, each initialized to 0
deque<string> svec(10); // ten elements, each an empty string
```

**Library arrays have Fixed Size**

```cpp
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

### 9.2.5 Assignment and `swap`

```cpp
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

**Using `assign` (sequential Containers Only)**

```cpp
list<string> names;
vector<const char*> oldstyle;
names = oldstyle; // error: container types don't match
// ok: can convert from const char* to string
names.assign(oldstype.cbegin(), oldstyle.cend());

// equivalent to slist1.clear()
// followed by slist1.insert(slist1.begin(), 10, "Hiya!");
list<string> slist1(1); // one element, which is the empty string
slist1.assign(10, "Hiya!"); // ten elements; each one is Hiya!
```

**Using `swap`**

```cpp
vector<string> svec1(10); // vector with ten elements
vector<string> svec2(24); // vector with 24 elements
swap(svec1, svec2);
```

### 9.2.7 Relational Operators

```cpp
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

### 9.3.1 Adding Elements to a Sequential Container

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

**Using `push_back`**

```cpp
// read from standard input, putting each word onto the end of container
string word;
while (cin >> word)
    container.push_back(word);

void pluralize(size_t cnt, string &word)
{
    if (cnt > 1)
        word.push_back('s'); // same as word += 's'
}
```

**Using push_front**

```cpp
list<int> ilist;
// add elements to the start of ilist
for (size_t ix = 0; ix != 4; ++ix)
    ilist.push_front(ix);
```

**Adding Elements at a Specified Point in the Container**

```cpp
slist.insert(iter, "Hello!"); // insert "Hello!" just before iter

vector<string> svec;
list<string> slist;
// equivalent to calling slist.pushfront("Hello!");
slist.insert(slist.begin(), "Hello!");
// no push_front on vector but we can insert before begin()
// warning: inserting anywhere but at the end of a vector might be slow
svec.insert(svec.begin(), "Hello!");
```

**Inserting a Range of Elements**

```cpp
svec.insert(svec.end(), 10, "Anna");

vector<string> v = {"quasi", "simba", "frollo", "scar"};
// insert the last two elements of v at the beginning of list
slist.insert(slist.begin(), v.end() - 2, v.end());
slist.insert(slist.end(), {"these", "words", "will", "go", "at", "the", "end"});
// run-time error: iterators denoting the range to copy from
// must not refer to the same container as the one we are changing
slist.insert(slist.begin(), slist.begin(), slist.end());
```

[c++11] Under the new standard, the versions of insert that take a count or a range return an iterator to the first element that was inserted.

**Using the Return from insert**

```cpp
list<string> lst;
auto iter = lst.begin();
while (cin >> word)
    iter = lst.insert(iter, word); // same as calling push_front
```

**Using the Emplace Operations [c++11]**

```cpp
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

### 9.3.2 Accessing Elements

Operations to Access Elements in a Sequential Container

```cpp
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
```

**The Access Members Return References**

```cpp
if (!c.empty()) {
    c.front() = 42; // assigns 42 to the first element in c
    auto &v = c.back(); // get a reference to the last element
    v = 1024; // changes the element in c
    auto v2 = c.back(); // v2 is not a reference; it's a copy of c.back()
    v2 = 0; // no change to the element in c
}
```

**Subscripting and Safe Random Access**

```cpp
vector<string> svec; // empty vector
cout << svec[0]; // run-time error: there are no elements in svec!
cout << svec.at[0]; // throws an out_of_range exception
```

### 9.3.3 Erasing Elements

`erase` Operations on Sequential Containers

```cpp
c.pop_back() // Removes last element in c. Undefined if c is empty. Returns void.
c.pop_front() // Removes first element in c. Undefined if c is empty. Returns void.
c.erase(p) // Removes the element denoted by the iterator p and returns an iterator to the element after the one deleted or the off-the-end iterator if p denotes the last element. Undefined if p is the off-the-end iterator.
c.erase(b, e) // Removes the range of elements denoted by the iterators b and e. Returns an iterator to the element after the last one that was deleted, or an off-the-end iterator if e is itself an off-the-end iterator.
c.clear() // Removes all the elements in c. Returns void.
```

**The `pop_front` and `pop_back` Members**

```cpp
while (!ilist.empty()) {
    process(ilist.front()); // do something with the current top of ilist
    ilist.pop_front(); // done; remove the first element
}
```

**Removing an Element from within the Container**

```cpp
list<int> lst = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
auto it = lst.begin();
while (it != lst.end())
    if (*it % 2) // if the element is odd
        it = lst.erase(it); // erase this element
    else
        ++it;
```

**Removing Multiple Elements**

```cpp
// delete the range of elements between two iterators
// returns an iterator to the element just after the last removed element
elem1 = slist.erase(elem1, elem2); // after the call elem1 == elem2

slist.clear(); // delete all the elements within the container
slist.erase(slist.begin(), slist.end()); // equivalent
```

### 9.3.4 Specialized `forward_list` Operations

Operations to Insert or Remove Elements in a `forward_list`

```cpp
lst.before_begin()
lst.cbefore_begin()

lst.insert_after(p, t)
lst.insert_after(p, n, t)
lst.insert_after(p, b, e)
lst.insert_after(p, il)

emplace_after(p, args)

lst.erase_after(p)
lst.erase_after(b, e)
```

### 9.3.5 Resizing a Container

```cpp
list<int> ilist(10, 42); // ten ints: each has value 42
ilist.resize(15); // adds five elements of value 0 to the back of ilist
ilist.resize(25, -1); // adds ten elements of value -1 to the back of ilist
ilist.resize(5); // erases 20 elements from the back of ilist
```

## 9.4 How a `vector` Grows

To avoid these costs, library implementors use allocation strategies that reduce the number of times the container is reallocated. When they have to get new memory, vector and string implementations typically allocate capacity beyond what is immediately needed. The container holds this storage in reserve and uses it to allocate new elements as they are added. Thus, there is no need to reallocate the container
for each new element.

**Members to Manage Capacity**

```cpp
// Container Size Management
c.shrink_to_fit() // Request to reduce capacity() to equal size().
c.capacity() // Number of elements c can have before reallocation is necessary.
c.reserve(n) // Allocate space for at least n elements.
```

**`capacity` and `size`**

```cpp
vector<int> ivec;
// size should be zero; capacity is implementation defined
cout << "ivec: size: " << ivec.size()
     << " capacity: " << ivec.capacity() << endl;
// give ivec 24 elements
for (vector<int>::size_type ix = 0; ix != 24; ++ix)
    ivec.push_back(ix);

// size should be 24; capacity will be >= 24 and is implementation defined
cout << "ivec: size: " << ivec.size()
     << " capacity: " << ivec.capacity() << endl;


ivec.reserve(50); // sets capacity to at least 50; might be more
// size should be 24; capacity will be >= 50 and is implementation defined
cout << "ivec: size: " << ivec.size()
     << " capacity: " << ivec.capacity() << endl;

// add elements to use up the excess capacity
while (ivec.size() != ivec.capacity())
ivec.push_back(0);
// capacity should be unchanged and size and capacity are now equal
cout << "ivec: size: " << ivec.size()
     << " capacity: " << ivec.capacity() << endl;

ivec.push_back(42); // add one more element
// size should be 51; capacity will be >= 51 and is implementation defined
cout << "ivec: size: " << ivec.size()
     << " capacity: " << ivec.capacity() << endl;

ivec.shrink_to_fit(); // ask for the memory to be returned
// size should be unchanged; capacity is implementation defined
cout << "ivec: size: " << ivec.size()
     << " capacity: " << ivec.capacity() << endl;
```

## 9.5 Additional `string` Operations

### 9.5.1 Other Ways to Construct `string`s

Additional Ways to Construct `string`s

```cpp
string s(cp, n); // s is a copy of the first n characters in the array to which cp points. That array must have at least n characters.
string s(s2, pos2); // s is a copy of the characters in the string s2 starting at the index pos2. Undefined if pos2 > s2.size().
string s(s2, pos2, len2); // s is a copy of len2 characters from s2 starting at the index pos2. Undefined if pos2 > s2.size().  Regardless of the value of len2, copies at most s2.size() - pos2 characters.
```

```cpp
const char *cp = "Hello World!!!"; // null-terminated array
char noNull[] = {'H', 'i'}; // not null terminated
string s1(cp); // copy up to the null in cp; s1 == "Hello World!!!"
string s2(noNull,2); // copy two characters from no_null; s2 == "Hi"
string s3(noNull); // undefined: noNull not null terminated
string s4(cp + 6, 5);// copy 5 characters starting at cp[6]; s4 == "World"
string s5(s1, 6, 5); // copy 5 characters starting at s1[6]; s5 == "World"
string s6(s1, 6); // copy from s1 [6] to end of s1; s6 == "World!!!"
string s7(s1,6,20); // ok, copies only to end of s1; s7 == "World!!!"
string s8(s1, 16); // throws an out_of_range exception
```

**The `substr` Operation**

```cpp
string s("hello world");
string s2 = s.substr(0, 5); // s2 = hello
string s3 = s.substr(6); // s3 = world
string s4 = s.substr(6, 11); // s4 = world
string s5 = s.substr(12); // throws an out_of_range exception
```

### 9.5.2 Other Ways to Change a `string`

```cpp
s.insert(s.size(), 5, '!'); // insert five exclamation points at the end of s
s.erase(s.size() - 5, 5); // erase the last five characters from s

const char *cp = "Stately, plump Buck";
s.assign(cp, 7); // s == "Stately"
s.insert(s.size(), cp + 7); // s == "Stately, plump Buck"

string s = "some string", s2 = "some other string";
s.insert(0, s2); // insert a copy of s2 before position 0 in s
// insert s2.size() characters from s2 starting at s2[0] before s[0]
s.insert(0, s2, 0, s2.size());
```

**The `append` and `replace` Functions**

```cpp
string s("C++ Primer"), s2 = s; // initialize s and s2 to "C++ Primer"
s.insert(s.size(), " 4th Ed."); // s == "C++ Primer 4th Ed."
s2.append(" 4th Ed."); // equivalent: appends " 4th Ed." to s2; s == s2
```

Operations to Modify `string`s

```cpp
s.insert(pos, args) // Insert characters specified by args before pos. pos can be an index or an iterator. Versions taking an index return a reference to s; those taking an iterator return an iterator denoting the first inserted character.
s.erase(pos, len) // Remove len characters starting at position pos. If len is omitted, removes characters from pos to the end of the s. Returns a reference to s.
s.assign(args) // Replace characters in s according to args. Returns a reference to s.
s.append(args) // Append args to s. Returns a reference to s.
s.replace(range, args) // Remove range of characters from s and replace them with the characters formed by args. range is either an index and a length or a pair of iterators into s. Returns a reference to s.

// args can be one of the following
str // The string str.
str, pos, len // Up to len characters from str starting at pos.
cp, len // Up to len characters from the character array pointed to by cp.
cp // Null-terminated array pointed to by pointer cp.
n, c // n copies of character c.
b, e // Characters in the range formed by iterators b and e.
initializer list // Comma-separated list of characters enclosed in braces.
```

```cpp
// equivalent way to replace "4th" by "5th"
s.erase(11, 3); // s == "C++ Primer Ed."
s.insert(11, "5th"); // s == "C++ Primer 5th Ed."
// starting at position 11, erase three characters and then insert "5th"
s2.replace(11, 3, "5th"); // equivalent: s == s2
s.replace(11, 3, "Fifth"); // s == "C++ Primer Fifth Ed."
```

### 9.5.3 `string` Search Operations

**`string` Search Operations**

```cpp
s.find(args) // Find the first occurrence of args in s.
s.rfind(args) // Find the last occurrence of args in s.
s.find_first_of(args) // Find the first occurrence of any character from args in s.
s.find_last_of(args) // Find the last occurrence of any character from args in s.
s.find_first_not_of(args) // Find the character in s that is not in args.
s.find_last_not_of(args) // Find the last character in s that is not in args.

// args must be one of
c, pos // Look for the character c starting at position pos in s. pos defaults to 0.
s2, pos // Look for the string s2 starting at position pos in s. pos defaults to 0.
cp, pos // Look for the C-style null-terminated string pointed to by the pointer cp. Start looking at position pos in s. pos defaults to c.
cp, pos, n // Look for the first n characters in the array pointed to by the pointer cp. Start looking at position pos in s. No default for pos or n.
```

The string search functions return string::size_type, which is an unsigned type. As a result, it is a bad idea to use an int, or other signed type, to hold the return from these functions.

```cpp
string name("AnnaBelle");
auto pos1 = name.find("Anna"); // pos1 == 0

string lowercase("annabelle");
pos1 = lowercase.find("Anna"); // pos1 == npos

string numbers("0123456789"), name("r2d2");
// returns 1, i.e., the index of the first digit in name
auto pos = name.find_first_of(numbers);

string dept("03714p3");
// returns 5, which is the index to the character 'p'
auto pos = dept.find_first_not_of(numbers);
```

**Specifying Where to Start the Search**

```cpp
string::size_type pos = 0;
// each iteration finds the next number in name
while ((pos = name.find_first_of(numbers, pos))
!= string::npos) {
cout << "found number at index: " << pos
<< " element is " << name[pos] << endl;
++pos; // move to the next character
}
```

**Searching Backward**

```
string river("Mississippi");
auto first_pos = river.find("is"); // returns 1
auto last_pos = river.rfind("is"); // returns 4
```

### 9.5.4 The `compare` Functions

Like strcmp, s.compare returns zero or a positive or negative value depending on whether s is equal to, greater than, or less than the string formed from the given arguments.

```cpp
// Possible Arguments to s.compare
s2 // Compare s to s2.
pos1, n1, s2 // Compares n1 characters starting at pos1 from s to s2.
pos1, n1, s2, pos2, n2 // Compares n1 characters starting at pos1 from s to the n2 characters starting at pos2 in s2.
cp // Compares s to the null-terminated array pointed to by cp.
pos1, n1, cp // Compares n1 characters starting at pos1 from s to cp
pos1, n1, cp, n2 // Compares n1 characters starting at pos1 from s to n2 characters starting from the pointer cp.
```

### 9.5.5 Numeric Conversions

```cpp
int i = 42;
string s = to_string(i); // converts the int i to its character representation
```

Conversions between `string`s and Numbers

```cpp
to_string(val) // Overloaded functions returning the string representation of val. val can be any arithmetic type. There are versions of to_string for each floating-point type and integral type that is int or larger. Small integral types are promoted as usual.
stoi(s, p, b) // Return the initial substring of s that has numeric content as an int, long, unsigned long, long long, unsigned long long, respectively. b indicates the numeric base to use for the conversion; b defaults to 10. p is a pointer to a size_t in which to put the index of the first nonnumeric character in s; p defaults to 0, in which case the function does not store the index.
stol(s, p, b)
stoul(s, p, b)
stoll(s, p, b)
stoull(s, p, b)
stof(s, p) // Return the initial numeric substring in s as a float, double, or long double, respectively. p has the same behavior as described for the integer conversions.
stod(s, p)
stold(s, p)
```

```cpp
string s2 = "pi = 3.14";
// convert the first substring in s that starts with a digit, d = 3.14
d = stod(s2.substr(s2.find_first_of("+-.0123456789")));
```

## 9.6 Container Adaptors

In addition to the sequential containers, the library defines three sequential container adaptors: `stack`, `queue`, and `priority_queue`. An adaptor is a general concept in the library. There are container, iterator, and function adaptors. Essentially, an adaptor is a mechanism for making one thing act like another. A container adaptor takes an existing container type and makes it act like a different type.

Operations and Types Common to the Container Adaptors

```cpp
size_type // Type large enough to hold the size of the largest object of this type.
value_type // Element type.
container_type // Type of the underlying container on which the adaptor is implemented.
A a; // Create a new empty adaptor named a.
A a(c); // Create a new adaptor named a with a copy of the container c.
relational operators // Each adaptor supports all the relational operators: ==, !=, <, <=, >, >=. These operators return the result of comparing the underlying containers.
a.empty() // false if a has any elements, true otherwise.
a.size() // Number of elements in a.
swap(a, b) // Swaps the contents of a and b; a and b must have the same type, including the type of the container on which they are implemented.
a.swap(b)
```

**Defining an Adaptor**

```cpp
// assuming that deq is a deque<int>
stack<int> stk(deq); // copies elements from deq into stk

// By default both stack and queue are implemented in terms of deque, and a priority_queue is implemented on a vector. We can override the default container type by naming a sequential container as a second type argument when we create the adaptor:

// empty stack implemented on top of vector
stack<string, vector<string>> str_stk;
// str_stk2 is implemented on top of vector and initially holds a copy of svec
stack<string, vector<string>> str_stk2(svec);
```

**Stack Adaptor**

```cpp
#include <stack>
stack<int> intStack; // empty stack
// fill up the stack
for (size_t ix = 0; ix != 10; ++ix)
    intStack.push(ix); // intStackholds 0...9 inclusive
while (!intStack.empty()) { // while there are still values in intStack
    int value = intStack.top();
    // code that uses value
    intStack.pop(); // pop the top element, and repeat
}
```

Stack Operations

```cpp
// Uses deque by default; can be implemented on a list or vector as well.
s.pop() // Removes, but does not return, the top element from the stack.
s.push(item) // Creates a new top element on the stack by copying or moving item, or by  constructing the element from args.
s.emplace(args)
s.top() // Returns, but does not remove, the top element on the stack.
```

### The Queue Adaptors

`queue`, `priority_queue` Operations

```cpp
#include <queue>
// By default queue uses deque and priority_queue uses vector; queue can use a list or vector as well, priority_queue can use a deque
q.pop() // Removes, but does not return, the front element or highest-priority
q.front() // Returns, but does not remove, the front or back element of q. Valid only for queue.
q.back()
q.top() // Returns, but does not remove, the highest-priority element. Valid only for priority_queue.
q.push(item) // Create an element with value item or constructed from args at the end of the queue or in its appropriate position in priority_queue.
q.emplace(args)
```