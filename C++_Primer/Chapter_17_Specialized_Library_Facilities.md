# Chapter 17 Specialized Library Facilities

## 17.1 The tuple Type

```cpp
// Operations on tuples
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

### Defining and Initializing tuples

```cpp
tuple<size_t, size_t, size_t> threeD; // all three members set to 0
tuple<string, vector<double>, int, list<int>>
someVal("constants", {3.14, 2.718}, 42, {0,1,2,3,4,5});

tuple<size_t, size_t, size_t> threeD = {1,2,3}; // error
tuple<size_t, size_t, size_t> threeD{1,2,3}; // ok

// tuple that represents a bookstore transaction: ISBN, count, price per book
auto item = make_tuple("0-999-78345-X", 3, 20.00);


// Accessing the Members of a tuple
auto book = get<0>(item); // returns the first member of item
auto cnt = get<1>(item); // returns the second member of item
auto price = get<2>(item)/cnt; // returns the last member of item
get<2>(item) *= 0.8; // apply 20% discount

typedef decltype(item) trans; // trans is the type of item
// returns the number of members in object's of type trans
size_t sz = tuple_size<trans>::value; // returns 3
// cnt has the same type as the second member in item
tuple_element<1, trans>::type cnt = get<1>(item); // cnt is an int


// Relational and Equality Operators
tuple<string, string> duo("1", "2");
tuple<size_t, size_t> twoD(1, 2);
bool b = (duo == twoD); // error: can't compare a size_t and a string
tuple<size_t, size_t, size_t> threeD(1, 2, 3);
b = (twoD < threeD); // error: differing number of members
tuple<size_t, size_t> origin(0, 0);
b = (origin < twoD); // ok: b is true
```

### Using a tuple to Return Multiple Values

```cpp
// each element in files holds the transactions for a particular store
vector<vector<Sales_data>> files;

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

## 17.2 The bitset Type

```cpp

```

## 17.3 Regular Expressions

```cpp

```

## 17.4 Random Numbers

```cpp

```

## 17.5 The IO Library Revisited

```cpp

```
