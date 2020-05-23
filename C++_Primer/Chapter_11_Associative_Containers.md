# Chapter 11 Associative Containers

Associative and sequential containers differ from one another in a fundamental way: Elements in an associative are stored and retrieved by a key. In contrast, elements in a sequential container are stored and accessed sequentially by their position in the container.

Associative Container Types

```cpp
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

**Using a `map`**

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

**Using a `set`**

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

### 11.2.1 Defining an Associative Container

```cpp
map<string, size_t> word_count; // empty
// [c++11] list initialization
set<string> exclude = {"the", "but", "and", "or", "an", "a", "The", "But", "And", "Or", "An", "A"};
// three elements; authors maps last name to first
map<string, string> authors = {{"Joyce", "James"}, {"Austen", "Jane"}, {"Dickens", "Charles"}};
```

**Initializing a `multimap` or `multiset`**

```cpp
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

### 11.2.2 Requirements on Key Type

**Key Types for Ordered Containers**

* Two keys cannot both be “less than” each other; if k1 is “less than” k2, then k2 must never be “less than” k1.
* If k1 is “less than” k2 and k2 is “less than” k3, then k1 must be “less than” k3.
* If there are two keys, and neither key is “less than” the other, then we’ll say that those keys are “equivalent.” If k1 is “equivalent” to k2 and k2 is “equivalent” to k3, then k1 must be “equivalent” to k3.

In practice, what’s important is that a type that defines a < operator that “behaves normally” can be used as a key.

**Using a Comparison Function for the Key Type**

```cpp
bool comareIsbn(const Sales_data &lhs, const Sales_data &rhs)
{
    return lhs.isbn() < rhs.isbn();
}

// bookstore can have several transactions with the same ISBN
// elements in bookstore will be in ISBN order
multiset<Sales_data, decltype(compareIsbn) *> bookstore(compareIsbn);
```

### 11.2.3 The `pair` Type

```cpp
#include <utility>
pair<string, string> anon; // holds two strings
pair<string, size_t> word_count; // holds a string and an size_t
pair<string, vector<int>> line; // holds string and vector<int>

pair<string, string> author{"James", "Joyce"};
```

Unlike other library types, the data members of `pair` are public. These members are named first and second, respectively. We access these members using the normal member access notation.

Operations on `pair`s

```cpp
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

**A Function to Create `pair` Objects**

```cpp
pair<string, int> process(vector<string> &v)
{
    // process v
    if (!v.empty())
        // [c++11] list initialize
        return {v.back(), v.back().size()}; 
    else
        return pair<string, int>(); // explicitly constructed return value
}

if (!v.empty())
    return pair<string, int>(v.back(), v.back().size());

if (!v.empty())
    return make_pair(v.back(), v.back().size());
```

## 11.3 Operations on Associative Containers

**Associative Container Additional Type Aliases**

```cpp
key_type // Type of the key for this container type
mapped_type // Type associated with each key; map types only
value_type // For sets, sameas the key_type; For maos, pair<const key_type, mapped_type>
```

```cpp
set<string>::value_type v1; // v1 is a string
set<string>::key_type v2; // v2 is a string
map>string, int>::value_type v3; // v3 is a pair<const string, int>
map<string, int>::key_type v4; // v4 is a string
map<string, int>::mapped_type v5; // v5 is an int
```

### 11.3.1 Associative Container Iterators

When we dereference an iterator, we get a reference to a value of the container’s value_type.

```cpp
// get an iterator to an element in word_count
auto map_it = word_count.begin();
// *map_it is a reference to a pair<const string, size_t> object
cout << map_it->first; // prints the key for this element
cout << " " << map_it->second; // prints the value of the element
map_it->first = "new key"; // error: key is const
++map_it->second; // ok: we can change the value through an iterator
```

It is essential to remember that the value_type of a map is a pair and we can change the value but not the key member of that pair.

**Iterators for sets Are const**

```cpp
set<int> iset = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
set<int>::iterator set_it = iset.begin();
if (set_it != set.end()) {
    *set_it = 42; // error: keys in a set are read-only
    cout << *set_it << endl; // ok: can read the key
}
```

**Iterating across an Associative Container**

```cpp
// get an iterator positioned on the first element
auto map_it = word_count.cbegin();
// compare the current iterator to the off-the-end iterator
while (map_it != word_count.cend()) {
    // dereference the iterator to print the element key--value pairs
    cout << map_it->first << " occurs "
         << map_it->second << " times" << endl;
    ++map_it; // increment the iterator to denote the next element
}
```

**Associative Containers and Algorithms**

In general, we do not use the generic algorithms (Chapter 10) with the associative containers. The fact that the keys are const means that we cannot pass associative container iterators to algorithms that write to or reorder container elements. Such algorithms need to write to the elements. The elements in the set types are const, and those in maps are pairs whose first element is const.

### 11.3.2 Adding Elements

```cpp
vector<int> ivec = {2, 4, 6, 8, 2, 4, 6, 8}; // ivec has eight elements
set<int> set2; // empty set
set2.insert(ivec.cbegin(), ivec.cend()); // set2 has four elements
set2.insert({1, 3, 5, 7, 1, 3, 5, 7}); // set2 has eight elements
```

**Associative Container insert Operations**

```cpp
c.insert(v) // v value_type object; args are used to construct an element.
c.emplace(args) // For map and set, the element is inserted (or constructed) only if an element with the given key is not already in c. Returns a pair containing an iterator referring to the element with the given key and a bool indicating whether the element was inserted. For multimap and multiset, inserts (or constructs) the given element and returns an iterator to the new element.
c.insert(b, e) // b and e are iterators that denote a range of c::value_type values;
c.insert(il) // il is a braced list of such values. Returns void. For map and set, inserts the elements with keys that are not already in c. For multimap and multiset inserts, each element in the range.
c.insert(p, v) // Like insert(v) (or emplace(args)), but uses iterator p as a hint for where to begin the search for where the new element should be stored. Returns an iterator to the element with the given key.
c.emplace(p, args)
```

**Adding Elements to a `map`**

```cpp
// four ways to add word to word_count
word_count.insert({word, 1}); // [c++11]
word_count.insert(make_pair(word, 1));
word_count.insert(pair<string, size_t>(word, 1));
word_count.insert(map<string, site_t>::value_type(word, 1));
```

**Testing the Return from insert**

For the containers that have unique keys, the versions of `insert` and `emplace` that add a single element return a `pair` that lets us know whether the insertion happened. The `first` member of the `pair` is an iterator to the element with the given key; the `second` is a `bool` indicating whether that element was inserted, or was already there. If the key is already in the container, then `insert` does nothing, and the `bool` portion of the return value is `false`. If the key isn’t present, then the element is inserted and the `bool` is `true`.

```cpp
// more verbose way to count number of times each word occurs in the input
map<string, size_t> word_count; // empty map from string to size_t
string word;
while (cin >> word) {
    // inserts an element with key equal to word and value 1;
    // if word is already in word_count, insert does nothing
    auto ret = word_count.insert({word, 1});
    if (!ret.second) // word was already in word_count
        ++ret.first->second;
}
```

**Unwinding the Syntax**

```cpp
++((ret.first)->second); // equivalent expression


pair<map<string, size_t>::iterator, bool> ret = word_count.insert(make_pair(word, 1));
```

**Adding Elements to `multiset` or `multimap`**

```cpp
multimap<string, string> authors;
// adds the first element with the key Barth, John
authors.insert({"Barth, John", "Sot-Weed Factor"});
// ok: adds the second element with the key Barth, John
authors.insert({"Barth, John", "Lost in the Funhouse"});
```

### 11.3.3 Erasing Elements

Removing Elements from an Associative Container

```cpp
c.erase(k) // Removes every element with key k from c. Return size_type indicating the number of elements removed.
c.erase(p) // Removes the element denoted by the iterator p from c. p must refer to an actual element in c; it must not be equal to c.end(). Returns an iterator to the element after p or c.end() if p denotes the last element in c.
c.erase(b, e) // Removes the elements in the range denoted by the iterator pair b, e. Returns e.
```

```cpp
// erase on a key returns the number of elements removed
if (word_count.erase(removal_word))
    cout << "ok: " << removal_word << " removed\n";
else cout << "oops: " << removal_word << " not found!\n";

auto cnt = authors.erase("Barth, John");
```

### 11.3.4 Subscripting a `map`

Subscript Operation for `map` and `unordered_map`

```cpp
c[k] // Returns the element with key k; if k is not in c, adds a new, value-initialized element with key k.
c.at(k) // Checked access to the element with key k; throws an out_of_range exception if k is not in c.
```

```cpp
map<string, size_t> word_count; // empty map
// insert a value-initialized element with key Anna; then assign 1 to its value
word_count["Anna"] = 1;
```

**Using the Value Returned from a Subscript Operation**

```cpp
cout << word_count["Anna"]; // fetch the element indexed by Anna; prints 1
++word_count["Anna"]; // fetch the element and add 1 to it
cout << word_count["Anna"]; // fetch the element and print it; prints 2
```

### 11.3.5 Accessing Elements

Operations to Find Elements in an Associative Container

```cpp
// lower_bound and upper_bound not valid for the unordered containers. 
// Subscript and at operations only for map and unordered_map that are not const.
c.find(k) // Returns an iterator to the (first) element with key k, or the off-the-end iterator if k is not in the container.
c.count(k) // Returns the number of elements with key k. For the containers with unique keys, the result is always zero or one.
c.lower_bound(k) // Returns an iterator to the first element with key not less than k.
c.upper_bound(k) // Returns an iterator to the first element with key greater than k.
c.equal_range(k) // Returns a pair of iterators denoting the elements with key k. If k is not present, both members are c.end().
```

```cpp
set<int> iset = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
iset.find(1); // returns an iterator that refers to the element with key == 1
iset.find(11); // returns the iterator == iset.end()
iset.count(1); // returns 1
iset.count(11); // returns 0
```

**Using `find` Instead of Subscript for `map`s**

For the `map` and `unordered_map` types, the subscript operator provides the simplest method of retrieving a value. However, as we’ve just seen, using a subscript has an important side effect: If that key is not already in the `map`, then subscript inserts an
element with that key.

```cpp
if (word.find("foobar") == word_count.end())
    cout << "foobar is not in the map" << endl;
```

**Finding Elements in a `multimap` or `multiset`**

```cpp
string search_item("Alain de Botton"); // author we'll look for
auto entries = authors.count(search_item); // number of elements
auto iter = authors.find(search_item); // first entry for this author
// loop through the number of entries there are for this author
while(entries) {
    cout << iter->second << endl; // print each title
    ++iter; // advance to the next title
    --entries; // keep track of how many we've printed
}
```

**A Different, Iterator-Oriented Solution**

```cpp
// definitions of authors and search_item as above
// beg and end denote the range of elements for this author
for (auto beg = authors.lower_bound(search_item), 
          end = authors.upper_bound(search_item);
     beg != end; ++beg)
    cout << beg->second << endl; // print each title
```

**The `equal_range` Functions**

```cpp
// definitions of authors and search_item as above
// pos holds iterators that denote the range of elements for this key
for (auto pos = authors.equal_range(search_item);
     pos.first != pos.second; ++pos.first)
    cout << pos.first->second << endl; // print each title
```

### 11.3.6 A Word Transformation Map

## 11.4 The Unordered Containers [c++11]

The new standard defines four unordered associative containers. Rather than using a comparison operation to organize their elements, these containers use a hash function and the key type’s == operator.

**Using an Unordered Container**

```cpp
// count occurrences, but the words won't be in alphabetical order
unordered_map<string, size_t> word_count;
string word;
while (cin >> word)
    ++word_count[word]; // fetch and increment the counter for word
for (const auto &w : word_count) // for each element in the map
    // print the results
    cout << w.first << " occurs " << w.second
         << ((w.second > 1) ? " times" : " time") << endl;
```

**Managing the Buckets**

Unordered Container Management Operations

A hash function is allowed to map elements with differing keys to the same bucket.

```cpp
// Bucket Interface
c.bucket_count()
c.max_bucket_count()
c.bucket_size(n)
c.bucket(k)

// Bucket Iteration
local_iterator
const_local_iterator
c.begin(n), c.end(n)
c.cbegin(n), c.cend(n)

// Hash Policy
c.load_factor()
c.max_local_factor()
c.rehash(n)
c.reserve(n)
```

**Requirements on Key Type for Unordered Containers**

```cpp
size_t hasher(const Sales_data &sd) {
    return hash<string>()(sd.isbn());
}
bool eqOp(const Sales_data &lhs, const Sales_data &rhs) {
    return lhs.isbn() == rhs.isbn();
}
```

```cpp
using SD_multiset = unordered_multiset<Sales_data, decltype(hasher)*, decltype(eqOp)*>;
// arguments are the bucket size and pointers to the hash function and equality operator
SD_multiset bookStore(42, hasher, eqOp);
```