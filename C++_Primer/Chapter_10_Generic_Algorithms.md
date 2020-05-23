# Chapter 10 Generic Algorithms

## 10.1 Overview

```cpp
int val = 42;
auto result = find(vec.cbegin(), vec.cend(), val);
cout << "The value" << val
     << (result == vec.cend()
         ? " is not present" : " is present") << endl;

string val = "a value";
auto result  = find(lst.cbegin(), lst.cend(), val);

int ia[] = {27, 210, 12, 47, 109, 83};
int val = 83;
int* result = find(begin(ia), end(ia), val);

auto result = find(ia + 1, ia + 4, val);
```

## 10.2 A First Look at the Algorithms

### 10.2.1 Read-Only Algorithms

```cpp
int sum = accumulate(vec.cbegin(), vec.cend(), 0);

string sum = accumulate(v.cbegin(), v.cend(), string(""));
// error: no + on const char*
string sum = accumulate(v.cbegin(), v.cend(), "");
```

```cpp
// roster2 should have at least as many elements as roster1
equal(roster1.cbegin(), roster1.cend(), roster2.cgein());
```

Because `equal` operates in term of iterators, we can call `equal` to compare elements in containers of different types. Moreover, the element types also need not be the same so long as we can use `==` to compare the element types. For example, `roster1` could be a `vector<string>` and `roster2` a `list<const char*>`.

However, `equal` makes one critically important assumption: It assumes that the second sequence is at least as big as the first. This algorithm potentially looks at every element in the first sequence. It assmes that there is a corresponding element for each of those elements in the second sequence.

### 10.2.2 Algorithms That Write Container Elements

```cpp
fill(vec.begin(), vec.end(), 0);
fill(vec.begin(), vec.begin() + vec.size()/2. 10);

vector<int> vec;
fill_n(vec.begin(), vec.size(), 0);

vector<int> vec;
// disaster: attempts to write to ten (nonexistent) elements in vec
fill_n(vec.begin(), 10, 0); 
```

**Introducing `back_inserter`**

```cpp
vector<int> vec;
auto it = back_inserter(vec); // assigning through it adds elements to vec
*it = 42;

vector<int> vec;
fill_n(back_inserter(vec), 10, 0); // appends ten elements to vec
```

**Copy Algorithms**

```cpp
int a1[] = {0,1,2,3,4,5,6,7,8,9};
int a2[sizeof(a1)/sizeof(*a1)]; // a2 has the same size as a1
// ret points just past the last element copied into a2
auto ret = copy(begin(a1), end(a1), a2); // copy a1 into a2

// replace any element with the value 0 with 42
replace(ilist.begin(), ilst.end(), 0, 42)

// use back_inserter to grow destination as needed
replace_copy(ilst.cbegin(), ilst.cend(), 
             back_inserter(ivec), 0, 42);
```

### 10.2.3 Algorithms That Reorder Container Elements

```cpp
void elimDups(vector<string> &words) {
    // sort words alphabetically so we can find the duplicates
    sort(words.begin(), words.end());
    // unique reorders the input range so that each word appears once in the front portion of the range and returns an iterator one past the unique range
    auto end_unique = unique(words.begin(), words.end());
    /// erase uses a vector operation to remove the nonunique elements
    words.erase(end_unique, words.end());
}
```

## 10.3 Customizing Operations

### 10.3.1 Passing a Function to an Algorithm

**Predicate**

A predicate is an expression that can be called and that returns a value that can be used as a condition.

```cpp
// comparison function to be used to sort by word length
bool isShorter(const string &s1, const string &s2) {
    return s1.size() < s2.size();
}
sort(words.begin(), words.end(), isShorter);
```

**Sorting Algorithms**

A stable sort maintains the original order among equal elements.

```cpp
elimDups(words); // put words in alphabetical order and remove duplicates
// resort by length, maintaining alphabetical order among words of the same length
stable_sort(words.begin(), words.end(), isShorter);
```

### 10.3.2 Lambda Expressions [c++11]

A lambda expression has the form

```cpp
[capture list] (parameter list) -> return type { function body }
```

where capture list is an (often empty) list of local variables defined in the enclosing function.

```cpp
auto f = [] { return 42; }
```

**Passing Arguments to a Lambda**

```cpp
[](const string &a, const string &b)
  { return a.size() < b.size(); }

// sort words by size, but maintain alphabetical order for words of the same size
stable_sort(words.begin(), words.end(),
            [](const string &a, const string &b)
              { return a.size() < b.size(); });
```

**Using the Capture List**

```cpp
[sz](const string &a)
    { return a.size() >= sz; };
```

**Calling `find_if`**

```cpp
// get an iterator to the first element whose size() is >= sz
auto wc = find_if(words.begin(), words.end(), 
                  [sz](const string &a)
                      { return a.size() >= sz; });
```

**The `for_each` Algorithm**

```cpp
// print words of the given size or longer, each one followed by a space
for_each(wc, words.end(), 
         [](const string &s){ cout << s << " "; });
```

### 10.3.3 Lambda Captures and Returns

**Capture by Value**

```cpp
void fcn1() {
    size_t v1 = 42; // local variable
    // copies v1 into the callable object named f
    auto f = [v1] { return v1; };
    v1 = 0;
    auto j = f(); // j is 42: f stored a copy of v1 when we created it
}
```

**Capture by Reference**

```cpp
void fcn2() {
    size_t v1 = 42; // local variable
    // the object f2 contains a reference to v1
    auto f2 = [&v1] { return v1; };
    v1 = 0;
    auto j = f2(); // j is 0; f2 refers to v1; it doesn't store it
}
```

**Implicit Captures**

Rather than explicitly listing the variables we want to use from the enclosing function, we can let the compiler infer which variables we use from the code in the lambda's body.

```cpp
// sz implicitly captured by value
wc = find_if(words.begin(), words.end(), 
             [=](const string &s) // or [&] by reference
                { return s.size() >= sz; });
```

**Specifying the lambda Return Type**

By default, if a lambda body contains any statements other than a `return`, that lambda is assumed to return `void`.

```cpp
transform(vi.begin(), vi.end(), ve.begin(),
          [](int i) { return i < 0 ? -i : i; });

// error: cannot deduce the return type for the lambda
transform(vi.begin(), vi.end(), ve.begin(),
          [](int i) { if (i < 0) return -i; else return i; });
```

[c++11] When we need to define a return type for a lambda, we must use a trailing return type:

```cpp
transform(vi.begin(), vi.end(), ve.begin(),
          [](int i) -> int 
          { if (i < 0) return -i; else return i; });
```

### 10.3.4 Binding Arguments

It is not easy to write a function to replace a lambda that captures local variables.

```cpp
bool check_size(const string &s, string::size_type sz) {
    return s.size() >= sz;
}
```

We can't use this function as an argument to `find_if`. `find_if` takes a unary predicate, so the callable passed to `find_if` must take a single argument. In order to use `check_size` in place of that lambda, we have to figure out how to pass an arguemnt to the `sz` parameter.

**The Library `bind` Function [c++11]**

The general form of a call to `bind` is:

```cpp
auto newCallable = bind(callable, arg_list);
```

```cpp
// check6 is a callable object that takes one argument of type string and calls check_size on its given string and the value 6
auto check6 = bin(check_size, _1, 6);

string s = "hello";
bool b1 = check6(s); // check6(s) calls check_size(s, 6)

auto wc = find_if(words.begin(), words.end(), 
                  bind(check_size, _1, sz));
```

**Using `placeholders` Names**

```cpp
using std::placeholders::_1;

using namespace std::placeholders;
```

**Arguments to `bind`**

```cpp
// g is a callable object that takes two arguments
auto g = bind(f, a, b, _2, c, _1);
```

In effect, this call to `bind` maps `g(_1, _2)` to `f(a, b, _2, c, _1)`.

**Using `bind` to Reorder Parameters**

**Binding Reference Parameters**

```cpp
for_each(words.begin(), words.end(), 
         [&os, c](const string &s) { os << s << c; });

ostream &print(ostream &os, const string &s, char c) {
    return os << s << c;
}

for_each(words.begin(), words.end(), 
         bind(print, ref(os), _1, ' '));
```

## 10.4 Revisiting Iterators

```cpp

```

## 10.5 Structure of Generic Algorithms

### 10.5.1 The Five Iterator Categories

Iterator Categories

```cpp
Input iterator
Output iterator
Forward iterator
Bidirectional iterator
Random-access iterator
```

### 10.5.2 Algorithm Parameter Patterns

```cpp
alg(beg, end, other args);
alg(beg, end, dest, other args);
alg(beg, end, beg2, other args);
alg(beg, end, beg2, end2, other args);
```

### 10.5.3 Algorithm Naming Convertions

**Some Algorithms Use Overloading to Pass a Predicate**

```cpp
unique(beg, end);
unique(beg, end, comp);
```

**Algorithm with _if Versions**

```cpp
find(beg, end, val);
find_if(beg, end, pred);
```

**Distinguishing Versions That Copy from Those That Do Not**

```cpp
reverse(beg, end);
reverse_copy(beg, end, dest);

remove_if(v1.begin, v1.end(),
          [](int i){ return i % 2; });
remove_copy_if(v1.begin, v1.end(), back_inserter(v2), 
               [](int i){ return i % 2; });
```

## 10.6 Container-Specific Algorithms

Algorithms That are Members of `list` and `forward_list`

```cpp
lst.merge(lst2)
lst.merge(lst2, comp)
lst.remove(val)
lst.remove_if(pred)
lst.reverse()
lst.sort()
lst.sort(comp)
lst.unique()
lst.unique(pred)
```

**The `splice` Members**

Arguments to the `list` and `forward_list` `splice` Members

```cpp
// Move elements from lst2 to lst
lst.splice(args) or flst.splice_after(args)

(p, lst2)
(p, lst2, p2)
(p, lst2, b, e)
```