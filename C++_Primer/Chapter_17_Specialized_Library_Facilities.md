# Chapter 17 Specialized Library Facilities

## 17.1 The `tuple` Type [c++11]

Operations on `tuple`s

```cpp
#include <tuple>
tuple<T1, T2, ..., Tn> t; // t is a tuple with as many members as there are types T1...Tn. The members are value initialized
tuple<T1, T2, ..., Tn> t(v1, v2, ..., vn); // t is a tuple with types T1...Tn in which each member is initialized from the corresponding initializer, vi. This constructor is explicit
make_tuple(v1, v2, ..., vn) // Returns a tuple initialized from the given initializers. The type of the tuple is inferred from the types of the initializers
t1 == t2 // Two tuples are equal if they have the same number of members and if each pair of members are equal. Uses each member's underlying == operators. Once a member is found to be unequal, subsequent members are not tested
t1 != t2
t1 relop t2 // relational operations on tuples using dictionary ordering. The tuples must have the same number of members. Members of t1 are comared with the corresponding members from t2 using the < operator
get<i>(t) // Returns a reference to the ith data member of t; if t is an lvalue, the result is an lvalue reference; otherwise, it is an rvalue reference. All members of a tuple are public
tuple_size<tupleType>::value // A class template that can be instantiated by a tuple type and has a public constexpr static data member named value of type size_t that is number of members in the specified tuple type
tuple_element<i, tupleType>::type // A class template that can be instantiated by an integral constant and a tuple type and has a public member named type that is the type of the specified members in the specified tuple type
```

### 17.1.1 Defining and Initializing `tuple`s

```cpp
tuple<size_t, size_t, size_t> threeD; // all three members set to 0
tuple<string, vector<double>, int, list<int>>
someVal("constants", {3.14, 2.718}, 42, {0,1,2,3,4,5});

tuple<size_t, size_t, size_t> threeD = {1,2,3}; // error
tuple<size_t, size_t, size_t> threeD{1,2,3}; // ok

// tuple that represents a bookstore transaction: ISBN, count, price per book
auto item = make_tuple("0-999-78345-X", 3, 20.00);
```

**Accessing the Members of a `tuple`**

```cpp
auto book = get<0>(item); // returns the first member of item
auto cnt = get<1>(item); // returns the second member of item
auto price = get<2>(item)/cnt; // returns the last member of item
get<2>(item) *= 0.8; // apply 20% discount

typedef decltype(item) trans; // trans is the type of item
// returns the number of members in object's of type trans
size_t sz = tuple_size<trans>::value; // returns 3
// cnt has the same type as the second member in item
tuple_element<1, trans>::type cnt = get<1>(item); // cnt is an int
```

**Relational and Equality Operators**

```cpp
tuple<string, string> duo("1", "2");
tuple<size_t, size_t> twoD(1, 2);
bool b = (duo == twoD); // error: can't compare a size_t and a string
tuple<size_t, size_t, size_t> threeD(1, 2, 3);
b = (twoD < threeD); // error: differing number of members
tuple<size_t, size_t> origin(0, 0);
b = (origin < twoD); // ok: b is true
```

### 17.1.2 Using a `tuple` to Return Multiple Values

```cpp
// each element in files holds the transactions for a particular store
vector<vector<Sales_data>> files;
```

**A Function That Returns a `tuple`**

```cpp
// matches has three members: an index of a store and iterators into that store's vector
typedef tuple<vector<Sales_data>::size_type,
              vector<Sales_data>::const_iterator,
              vector<Sales_data>::const_iterator> matches;
// files holds the transactions for every store
// findBook returns a vector with an entry for each store that sold the given book
vector<matches>
findBook(const vector<vector<Sales_data>> &files,
         const string &book)
{
    vector<matches> ret; // initially empty
    // for each store find the range of matching books, if any
    for (auto it = files.cbegin(); it != files.cend(); ++it)
    {
        // find the range of Sales_data that have the same ISBN
        auto found = equal_range(it->cbegin(), it->cend(),
        book, compareIsbn);
        if (found.first != found.second) // this store had sales
            // remember the index of this store and the matching range
            ret.push_back(make_tuple(it - files.cbegin(),
            found.first,
            found.second));
    }
    return ret; // empty if no matches found
}
```

**Using a `tuple` Returned by a Function**

```cpp
void reportResults(istream &in, ostream &os, const vector<vector<Sales_data>> &files)
{
    string s;
    while (in >> s)
    {
        auto trans = findBook(files, s);
        if (trans.empty())
        {
            cout << s << " not found in any stores" << endl;
            continue;
        }
        for (const auto &store : trans)
            os << "store " << get<0>(store) << " sales: "
               << accumulate(get<1>(store), get<2>(store), Sales_data(s))
               << endl;
    }
}
```

## 17.2 The `bitset` Type

```cpp

```

## 17.3 Regular Expressions

Regular Expression Library Components

```cpp
regex // Class that represents a regular expression
regex_match // Matches a sequence of characters against a regular expression
regex_search // Finds the first subsequence that matches the regular expression
regex_replace // Replaces a regular expression using a given format
sregex_iterator // Iterator adapter that calls regex_search to iterate through the matches in a string
smatch // Container class that holds the results of searching a string
ssub_match // Results for a matched subexpression in a string
```

Arguments to `regex_search` and `regex_match`

```cpp
(seq, m, r, mft) // Look for the regular expression in the regex object r in the character sequence seq. seq can be a string, a pair of iterators denoting a range, or a pointer to a null-terminated character array. m is a match object, which is used to hold details about the match. m and seq must have compatible types. mft is an optional regex_constants::match_flag_type value. 
(seq, r, mft)
```

### 17.3.1 Using the Regular Expression Library

```cpp
#include <regex>
// find the characters ei that follow a character other than c
string pattern("[^c]ei");
// we want the whole word in which our pattern appears
pattern = "[[:alpha:]]*" + pattern + "[[:alpha:]]*";
regex r(pattern); // construct a regex to find pattern
smatch results; // define an object to hold the results of a search
// define a string that has text that does and doesn't match pattern
string test_str = "receipt freind theif receive";
// use r to find a match to pattern in test_str
if (regex_search(test_str, results, r)) // if there is a match
    cout << results.str() << endl; // print the matching word
```

## 17.4 Random Numbers

[c++11] Random Number Library Components

```cpp
Engine // Types that generate a sequence of random unsigned integers
Distribution // Types that use an engine to return numbers according to a particular probability distribution
```

C++ programs should not use the library `rand` function. Instead, they should use the `default_random_engine` along with an appropriate distribution object.

### 17.4.1 Random-Number Engines and Distribution

```cpp
#include <random>
default_random_engine e;
for (size_t i = 0; i < 10; ++i)
    cout << e() << " ";
```

Random Number Engine Operations

```cpp
Engine e; // Default constructor; uses the default seed for the engine type
Engine e(s); // Uses the integral value s as the seed
e.seed(s) // Reset the state of the engine using the seed s
e.min() // The smallest and largest numbers this generator will generate
e.max()
Engine::result_type // The unsigned integral type this engine generates
e.discard(u) // Advance the engine by u steps; u has type unsigned long long
```

**Distribution Types and Engines**

```cpp
// uniformly distributed from 0 to 9 inclusive
uniform_int_distribution<unsigned> u(0,9);
default_random_engine e; // generates unsigned random integers
for (size_t i = 0; i < 10; ++i)
    // u uses e as a source of numbers
    // each call returns a uniformly distributed value in the specified range
    cout << u(e) << " ";
```

The distribution types define a call operator that takes a random-number engine as its argument. The distribution object uses its engine argument to produce random numbers that the distribution object maps to the specified distribution.

**Comparing Random Engines and the `rand` Function**

```cpp
cout << "min: " << e.min() << " max: " << e.max() << endl;
```

**Engines Generate a Sequence of Numbers**

```cpp
// almost surely the wrong way to generate a vector of random integers
// output from this function will be the same 100 numbers on every call!
vector<unsigned> bad_randVec()
{
    default_random_engine e;
    uniform_int_distribution<unsigned> u(0,9);
    vector<unsigned> ret;
    for (size_t i = 0; i < 100; ++i)
    ret.push_back(u(e));
    return ret;
}

vector<unsigned> v1(bad_randVec());
vector<unsigned> v2(bad_randVec());
// will print equal
cout << ((v1 == v2) ? "equal" : "not equal") << endl;
```

```cpp
// returns a vector of 100 uniformly distributed random numbers
vector<unsigned> good_randVec()
{
    // because engines and distributions retain state, they usually should be
    // defined as static so that new numbers are generated on each call
    static default_random_engine e;
    static uniform_int_distribution<unsigned> u(0,9);
    vector<unsigned> ret;
    for (size_t i = 0; i < 100; ++i)
    ret.push_back(u(e));
    return ret;
}
```

Because `e` and `u` are `static`, they will hold their state across calls to the function. The first call will use the first 100 random numbers from the sequence `u(e)` generates, the second call will get the next 100, and so on.

**Seeding a Generator**

```cpp
default_random_engine e1; // uses the default seed
default_random_engine e2(2147483646); // use the given seed value
// e3 and e4 will generate the same sequence because they use the same seed
default_random_engine e3; // uses the default seed value
e3.seed(32767); // call seed to set a new seed value
default_random_engine e4(32767); // set the seed value to 32767
for (size_t i = 0; i != 100; ++i) {
    if (e1() == e2())
        cout << "unseeded match at iteration: " << i << endl;
    if (e3() != e4())
        cout << "seeded differs at iteration: " << i << endl;
}

default_random_engine e1(time(0)); // a somewhat random seed
```

### 17.4.2 Other Kinds of Distributions

**Generating Random Real Numbers**

```cpp
default_random_engine e; // generates unsigned random integers
// uniformly distributed from 0 to 1 inclusive
uniform_real_distribution<double> u(0,1);
for (size_t i = 0; i < 10; ++i)
cout << u(e) << " ";
```

**Using the Distribution’s Default Result Type**

```cpp
// empty <> signify we want to use the default result type
uniform_real_distribution<> u(0,1); // generates double by default
```

**Generating Numbers That Are Not Uniformly Distributed**

```cpp
default_random_engine e; // generates random integers
normal_distribution<> n(4,1.5); // mean 4, standard deviation 1.5
vector<unsigned> vals(9); // nine elements each 0
for (size_t i = 0; i != 200; ++i) {
    unsigned v = lround(n(e)); // round to the nearest integer
    if (v < vals.size()) // if this result is in range
    ++vals[v]; // count how often each number appears
}
for (size_t j = 0; j != vals.size(); ++j)
    cout << j << ": " << string(vals[j], '*') << endl;
```

**The `bernoulli_distribution` Class**

```cpp
string resp;
default_random_engine e; // e has state, so it must be outside the loop!
bernoulli_distribution b; // 50/50 odds by default
do {
    bool first = b(e); // if true, the program will go first
    cout << (first ? "We go first"
                   : "You get to go first") << endl;
    // play the game passing the indicator of who goes first
    cout << ((play(first)) ? "sorry, you lost"
                           : "congrats, you won") << endl;
    cout << "play again? Enter 'yes' or 'no'" << endl;
} while (cin >> resp && resp[0] == 'y');
```

## 17.5 The IO Library Revisited

### 17.5.1 Formatted Input and Output

**Specifying How Much Precision to Print**

```cpp
// cout.precision reports the current precision value
cout << "Precision: " << cout.precision()
     << ", Value: " << sqrt(2.0) << endl;
// cout.precision(12) asks that 12 digits of precision be printed
cout.precision(12);
cout << "Precision: " << cout.precision()
     << ", Value: " << sqrt(2.0) << endl;
// alternative way to set precision using the setprecision manipulator
cout << setprecision(3);
cout << "Precision: " << cout.precision()
     << ", Value: " << sqrt(2.0) << endl;
```
