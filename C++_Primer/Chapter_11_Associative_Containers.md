# Chapter 11 Associative Containers

```cpp
// Associative Container Types
// Elements Ordered by Key
map // Associative array; holds key-value pairs
set // Container in which the key is the value
multimap // map in which a key can appear multiple times
multiset // set in which a key can appear multiple times

// Unordered Collections
unordered_map // map organized by a hash function
unordered_set // set organized by a hash function
unordered_multimap // Hashed map; keys can appear multiple times
unordered_multiset // Hashed set; key can appear multiple times
```

## 11.1 Using an Associative Container

### Using a map

```cpp
map<string, size_t> word_count; // empty map from string to size_t
string word;
while (cin >> word)
    ++word_count[word]; // fetch and increment the counter for word
for (const auto &w : word_count) // for each element in the map
    // print the results
    cout << w.first << " occurs " << w.second
         << ((w.second > 1) ? " times" : "time") << endl;
```

### Using a set

```cpp
// count the number of times each word occurs in the input
map<string, size_t> word_count; // empty map from string to size_t
set<string> exclude = {"The", "But", "And", "Or", "An", "A", "the", "but", "and", "or", "an", "a"};
string word;
while (cin >> word)
    // count only words that are not in exclude
    if (exclude.find(word) == exclude.end())
        ++word_count[word]; // fetch and increment the counter for word
```

## 11.2 Overview of the Associative Containers

### Defining an Associative Container

```cpp
map<string, size_t> word_count; // empty
// list initialization
set<string> exclude = {"the", "but", "and", "or", "an", "a", "The", "But", "And", "Or", "An", "A"};
// three elements; authors maps last name to first
map<string, string> authors = {{"Joyce", "James"}, {"Austen", "Jane"}, {"Dickens", "Charles"}};

// Initializing a multimap or multiset
// define a vector with 20 elements, holding two copies of each number from 0 to 9
vector<int> ivec;
for (vector<int>::size_type i = 0; i != 10; ++i) {
    ivec.push_back(i);
    ivec.push_back(i); // duplicate copies of each number
}
// iset holds unique elements from ivec; miset holds all 20 elements
set<int> iset(ivec.cbegin(), ivec.cend());
multiset<int> miset(ivec.cbegin(), ivec.cend());
cout << ivec.size() << endl; // prints 20
cout << iset.size() << endl; // prints 10
cout << miset.size() << endl; // prints 20
```

### Requirements on Key Type

Key Types for Ordered Containers

* Two keys cannot both be “less than” each other; if k1 is “less than” k2, then k2 must never be “less than” k1.
* If k1 is “less than” k2 and k2 is “less than” k3, then k1 must be “less than” k3.
* If there are two keys, and neither key is “less than” the other, then we’ll say that those keys are “equivalent.” If k1 is “equivalent” to k2 and k2 is “equivalent” to k3, then k1 must be “equivalent” to k3.

In practice, what’s important is that a type that defines a < operator that “behaves normally” can be used as a key.

```cpp
// Using a Comparison Function for the Key Type
bool comareIsbn(const Sales_data &lhs, const Sales_data &rhs)
{
    return lhs.isbn() < rhs.isbn();
}

// bookstore can have several transactions with the same ISBN
// elements in bookstore will be in ISBN order
multiset<Sales_data, decltype(compareIsbn) *> bookstore(compareIsbn);
```

### The pair Type

```cpp
#include <utility>
pair<string, string> anon; // holds two strings
pair<string, size_t> word_count; // holds a string and an size_t
pair<string, vector<int>> line; // holds string and vector<int>

pair<string, string> author{"James", "Joyce"};
```

Unlike other library types, the data members of pair are public. These members are named first and second, respectively. We access these members using the normal member access notation.

```cpp
// Operations on pairs
pair<T1, T2> p; // p is a pair with value initialized members of types T1 and T2, respectively.
pair<T1, T2> p{v1, v2}; // p is a pair with types T1 and T2; the first and second members are initialized from v1 and v2, respectively.
pair<T1, T2> p = {v1, v2}; // Equivalent to p{v1, v2}.
make_pair(v1, v2) // Returns a pair initialized from v1 and v2. The type of the pair is inferred from the types of v1 and v2.
p.first // Return the (public) data member of p named first.
p.second // Returns the (public) data member of p named second.
p1 relop p2 // Relational operators(<, >, <=, >=). Relational operators are defined as dictionary ordering: For example, p1 < p2 is true if p1.first < p2.first or if !(p2.first < p1.first) && p1.second < p2.second. Uses the element's < operator.
p1 == p2 // Two pairs are equal if their first and second members are respectively equal. Uses the element's == operator.
p1 != p2
```

```cpp
// A Function to Create pair Objects
pair<string, int> process(vector<string> &v)
{
    // process v
    if (!v.empty())
        return {v.back(), v.back().size()}; // list initialize
    else
        return pair<string, int>(); // explicitly constructed return value
}

if (!v.empty())
    return pair<string, int>(v.back(), v.back().size());

if (!v.empty())
    return make_pair(v.back(), v.back().size());
```

## 11.3 Operations on Associative Containers

```cpp

```

## 11.4 The Unordered Containers

```cpp

```