# Chapter 3 Strings, Vectors, and Arrays

## 3.1 Namespace using Declarations

Scope operator (::) says that the compiler should look in the scope of the left-hand operand for the name of the right-hand operand.

**A Separate using Declaration Is Required for Each Name**

```cpp
#include <iostream>
// using declarations for names from the standard library
using std::cin;
using std::cout; using std::endl;
int main() {
    cout << "Enter two numbers:" << endl;
    int v1, v2;
    cin >> v1 >> v2;
    cout << "The sum of " << v1 << " and " << v2
         << " is " << v1 + v2 << endl;
    return 0;
}
```

**Headers Should Not Include using Declarations**

## 3.2 Library string Type

```cpp
#include <string>
using std::string;
```

### 3.2.1 Defining and Initializing strings

```cpp
string s1;
string s2 = s1;
string s3 = "hiya";
string s4(10, 'c);
```

### 3.2.2 Operations on strings

```cpp
os << s
is >> s
getline(is, s)
s.empty()
s.size()
s[n]
s1 + s2
s1 = s2
s1 == s2
s1 != s2
<, <=, >, >=
```

**The string::size_type** Type

[c++11] size returns a string::size_type value. Although we don't know the precise type of string::size_type, we do know that it is an unsigned type big enough to hold the size of any string. Any variable used to store the result from the string size operation should be of type string::size_type.

```cpp
auto len = line.size(); // len has type string::size_type
```

### 3.2.3 Dealing with the Characters in a string

```cpp
#include <cctype>
isalnum(c)
isalpha(c)
isdigit(c)
islower(c)
ispunct(c)
isspace(c)
isupper(c)
tolower(c)
toupper(c)
```

**[c++11] Processing Every Character? Use Range-Based for**

```cpp
string str("some string");
for (auto c : str)
    cout << c << endl;
```

**Using a Range for to Change the Characters in a string**

```cpp
string s("Hello World!!!");
for (auto &c : s)
    c = toupper(c);
cout << s << endl;
```

**Using a Subscript for Iteration**

```cpp
for (decltype(s.size()) index = 0; 
     index != s.size() && !isspace(s[index]); ++index)
        s[index] = toupper(s[index]);
```

## 3.3 Library vector Type

```cpp
#include <vector>
using std::vector;

vector<int> ivec;
vector<Sales_item> Sales_vec;
vector<vector<string>> file;
```

### 3.3.1 Defining and Initializing vectors

```cpp
vector<T> v1
vector<T> v2(v1)
vector<T> v2 = v1
vector<T> v3(n, val)
vector<T> v4(n)
vector<T> v5{a, b, c...}
vector<T> v5 = {a, b, c...}
```

**[c++11] List Initializing a vector**

```cpp
vector<string> articles = {"a", "an", "the"};

vector<string> v1{"a", "an", "the"}; // list initialization
vector<string> v1("a", "an", "the"); // error
```

### 3.3.2 Adding Elements to a vector

```cpp
vector<int> v2;
for (int i = 0; i != 100; ++i)
    v2.push_back(i);
```

**Key Concept: vectors Grow Efficiently**

### 3.3.3 Other vector Operations

```cpp
v.empty()
v.size()
v.push_back(t)
v[n]
v1 = v2
v1 = {a, b, c...}
v1 == v2
v1 != v2
<, <=, >, >=
```

## 3.4 Introducing Iterators

### 3.4.1 Using Iterators

```cpp
auto b = v.begin(), e = v.end();
```

**Iterator Operations**

```cpp
*iter
iter->mem

++iter
--iter
iter1 == iter2
iter1 != iter2
```

```cpp
string s("some string");
if (s.begin() != s.end()) {
    auto it = s.begin();
    *it = toupper(*it);
}
```

**Moving Iterators from One Element to Another**

```cpp
for (auto it = s.begin(); it != s.end() ** !isspace(*it); ++it)
    *it = toupper(*it);
```

**Iterator Types**

```cpp
vector<int>::iterator it;
string::iterator it2;
vector<int>::const_iterator it3;
string::const_iterator it4;
```

**The begin and end Operations**

```cpp
vector<int> v;
const vector<int> cv;
auto it1 = v.begin(); // it1 has type vector<int>::iterator
auto it2 = cv.begin(); // it2 has type vector<int>::const_iterator
```

[c++11] cbegin and cend

```cpp
auto it3 = v.cbegin(); // it3 has type vector<int>::const_iterator
```

### 3.4.2 Iterator Arithmetic

```cpp
iter + n
iter - n
iter1 += n
iter1 -= n
iter1 - iter2
>, >=, <, <=
```

## 3.5 Arrays

### 3.5.1 Defining and Initializing Built-in Arrays

```cpp
unsigned cnt = 42; // not a constant expression
constexpr unsigned sz = 42; // constant expression
int arr[10]; // array of ten ints
int *parr[sz]; // array of 42 pointers to int
string bad[cnt]; // error: cnt is not a constant expression
string strs[get_size()]; // ok if get_size is constexpr, error otherwise
```

**Explicitly Initializing Array Elements**

```cpp
const unsigned sz = 3;
int ia1[sz] = {0,1,2}; // array of three ints with values 0, 1, 2
int a2[] = {0, 1, 2}; // an array of dimension 3
int a3[5] = {0, 1, 2}; // equivalent to a3[] = {0, 1, 2, 0, 0}
string a4[3] = {"hi", "bye"}; // same as a4[] = {"hi", "bye", ""}
int a5[2] = {0,1,2}; // error: too many initializers
```

**Character Arrays Are Special**

```cpp
char a1[] = {'C', '+', '+'}; // list initialization, no null
char a2[] = {'C', '+', '+', '\0'}; // list initialization, explicit null
char a3[] = "C++"; // null terminator added
automatically
const char a4[6] = "Daniel"; // error: no space for the null!
```

**No Copy or Assignment**

```cpp
int a[] = {0, 1, 2}; // array of three ints
int a2[] = a; // error: cannot initialize one array with another
a2 = a; // error: cannot assign one array to another
```

### 3.5.2 Accessing the Elements of an Array

```cpp
// count the number of grades by clusters of ten: 0--9, 10--19, ... 90--99, 100
unsigned scores[11] = {}; // 11 buckets, all value initialized to 0
unsigned grade;
while (cin >> grade) {
    if (grade <= 100)
        ++scores[grade/10]; // increment the counter for the current cluster
}

for (auto i : scores)
    cout << i << " ";
cout << endl;
```

### 3.5.3 Pointers and Arrays

```cpp
int ia[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
auto ia2(&ia[0]); // equivalent to auto ia2(ia); ia2 is an int* that points to the first element in ia
```

**Pointers Are Iterators**

```cpp
int arr[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
int *p = arr;
++p;
```

**[c++11] The Library begin and end Functions**

```cpp
int ia[] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
int *beg = begin(ia); // pointer to the first element in ia
int *last = end(ia); // pointer one past the last element in ia
```

### 3.5.4 C-style Character Strings

```cpp
#include <cstring>

strlen(p)
strcmp(p1, p2)
strcat(p1, p2)
strcpy(p1, p2)
```

## 3.6 Multidimensional Arrays

```cpp
int ia[3][4]; // array of size 3; each element is an array of ints of size 4
```

**Initializing the Elements of a Multidimensional Array**

```cpp
int ia[3][4] = {
    {0, 1, 2, 3},
    {4, 5, 6, 7},
    {8, 9, 10, 11}
};

int ia[3][4] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11};
```

**Pointers and Multidimensional Arrays**

```cpp
int ia[3][4];
int (*p)[4] = ia; // p points to an array of four ints
p = &ia[2]; // p now points to the last element in ia
```

[c++11] Avoid having to write the type of a pointer into an array by using auto or decltype.

```cpp
for (auto p = ia; p != ia + 3; ++p) {
    for (auto q = *p; q != *p + 4; ++q)
        cout << *q << ' ';
    cout << endl;
}
```