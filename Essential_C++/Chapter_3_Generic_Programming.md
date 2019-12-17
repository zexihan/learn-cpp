# Chapter 3 Generic Programming

Standard Template Library (STL) 主要又两种组件构成：一是容器（container），包括vector, list, set, map等类，另一种组件是用以操作这些容器类的所谓泛型算法（generic algorithm），包括find(), sort(), replace(), merge() 等等。 

## 3.1 指针的算术运算（The Arithmetic of Pointers）

```cpp
template <typename elemType>
elemType* find(const elemType *first, const elemType *last, const elemType &value)
{
    if (!fisrt || !last)
        return 0;
    
    for ( ; first != last; ++first)
    {
        if (*fisrt == value)
            return first;
    }
    return 0;
}

template <typename elemType>
inline elemType* begin(const vector<elemType> &vec)
{ return vec.empty() ? 0 ：&vec[0] }
```

## 3.2 了解Iterators（泛型指针）

```cpp
vector<string> svec;
vector<string>::iterator iter = svec.begin();

const vector<string> cs_vec;
vector<string>::const_iterator iter = cs_vec.begin();

cout << "string value of element: " << *iter;
cout << "( " << iter->size() << " ): " << *iter << endl;

template <typename elemType>
void display(const vector<elemType> &vec, ostream &os)
{
    vector<elemType>::const_iterator iter = vec.begin();
    vector<elemType>::const_iterator end_it = vec.end();

    for ( ; iter != end_it; ++iter)
        os << *iter << ' ';
    os << endl;
}

template <typename IteratorType, typename elemType> 
IteratorType find(IteratorType first, IteratorType last, const elemType &value)
{
    for ( ; first != last; ++first)
        if (value == *first)
            return first
    return last;
}

const int asize = 8;
int ia[asize] = {1, 1, 2, 3, 5, 8, 13, 21};

vector<int> ivec(ia, ia+asize);
list<int> ilist(ia, ia+asize);

int *pia = find(ia, ia+asize, 1024);
if (pia != ia+asize)
    // found it

vector<int>::iterator it;
it = find(ivec.begein(), ivec.end(), 1024);
if (it != ivec.end())
    // found it

list<int>::iterator iter;
iter = find(ilist.begin(), ilist.end(), 1024);
if (iter != ilist.end())
    // found it
```

* **Search algorithm:** find(), count(), adjacent_find(), find_if(), count_if(), binary_Search(), find_first_of()
* **Sorting and ordering:** merge(), partial_sort(), partition(), random_shuffle(), reverse(), rotate(), sort()
* **Copy, deletion, substitution:** copy(), remove(), remove_if(), replace(), replace_if(), swap(), unique()
* **Relational:** equal(), includes(), mismatch()
* **Generation and mutation:** fill(), for_each(), generate(), transform()
* **Numeric:** accumulate(), adjacent_difference(), partial_sum(), inner_product()
* **Set:** set_union(), set_difference()


## 3.3 所有容器的共通操作

* equality（==）和 inequality（!=）运算符，返回true或false
* assignment（=）运算符，将某个容器复制给另一个容器
* empty() 会在容器无任何元素时返回true，否则返回false。
* size() 传用容器内当前含有的元素数目
* clean() 删除所有元素

```cpp
void comp(vector<int> &v1, vector<int> &v2)
{
    if (v1 == v2)
        return;
    
    if (v1.empty() || v2.empty())
        return
    
    vector<int> t;

    t = v1.size() > v2.size() ? v1 : v2;

    t.clear();
}
```

* begin() 返回一个iterator，指向容器的第一个元素
* end() 返回一个iterator，指向容器的最后一个元素的下一个位置
* insert() 将单一或某个范围内的元素安插到容器内
* erase() 将容器内的单一元素或某个范围内的元素删除

## 3.4 使用序列式容器（Sequential Containers）

```cpp
#include <vector>
#include <list>
#include <deque>

// 定义序列式容器对象的方式有5种
// 1. 产生空的容器
list<string> slist;
vector<int> ivec;

// 2. 产生特定大小的容器，每个元素都以其默认值作为初值（int, double: 0）
list<int> ilist(1024);
vector<string> svec(32);

// 3. 产生特定大小的容器，并为每个元素指定初值
vector<int> ivec(10, -1);
list<string> slist(16, "unassigned");

// 4. 通过一对iterators产生容器，这对iterators用来标示一整组作为初值的元素区间
int ia[8] = {1, 1, 2, 3, 5, 8, 13, 21};
vector<int> fib(ia, ia+8);

// 5. 根据某个容器产生出新容器，复制原容器内的元素，作为新容器的初值。
list<string> slist;
list<string> slist2(slist);
```

```cpp
#include <deque>
deque<int> a_line;
int ival;
while (cin >> ival)
{
    a_line.push_back(ival);
    int curr_value = a_line.front();

    // do sth

    a_line.pop_front();
}
```

push_front()和push_back()皆属于特殊化的insertion操作。每个容器除了拥有通用的insert()外，还支持4种变形：

* **iterator insert(iterator position, elemType value)** 可将value安插于position之前。它会返回一个iterator，指向被安插的元素
* **void insert(iterator position, int count, elemType value)** 可在position之前安插count个元素，这些元素的值皆和value相同
* **void insert(iterator1 position, iterator2 first, iterator2 last)** 可在position之前安插[first, last)所标示的各个元素
* **iterator insert(iterator position)** 可在position之前插入元素，此元素的初值为其所属型别的默认值

pop_front()和pop_back()皆属于特殊化的erase操作。每个容器除了拥有通用的erase()外，还支持两种变形：

* **iterator erase(iterator posit)** 可抹除posit所指的元素
* **iterator erase(iterator first, iterator last)** 可抹除[first, last]范围内的元素

上述两个erase()函数返回的iterator，皆指向被删除的最后一个元素的下一位置。

## 3.5 使用泛型算法

1. find()用于搜寻无序集合中是否存在某值。搜寻范围由iterators[first, last)标出。如果找到目标，find()会返回一个iterator指向该值，否则返回一个iterator指向last。
2. binary_search()用于已序集合的搜寻。如果搜寻到目标，就返回true；否则返回false。binary_search()比find()更有效率。
3. count()返回数值相符的元素数目。
4. search()比较某个容器内是否存在某个子序列。如果存在，则返回一个iterator指向子序列起始处；如果不存在，就返回一个iterator指向容器末尾。

```cpp
#include <algorithm>

extern bool grow_vec(vector<int>&, int);

bool is_elem(vector<int> &vec, int elem)
{
    // 泛型算法max_element()
    int max_value = max_element(vec.begin(), vec.end());
    if (max_value < elem)
        return grow_vec(vec, elem)
    if (max_value == elem)
        return true;
    
    vector<int> temp(vec.size());
    copy(vec.begin(), vec.end(), temp.begin());

    sort(temp.begin(), temp.end());

    return binary_search(temp.begin(), temp,end(), elem);
}
```

## 3.6 如何设计一个泛型算法

### Function Objects

所谓function objects，是某种class的实体对象，这类classes对function call运算符进行了重载操作，如此一来，可使function object被当成一般函数来使用，消除通过函数指针来调用函数时需付出的额外代价，提升效率。

标准程序库事先定义了一组function objects，分为三类：

* 6个算术运算：plus\<type>, minus\<type>, negate\<type>, multiplies\<type>, divides\<type>, modules\<type>
* 6个关系：less\<type>, less_equal\<type>, greater\<type>, greater_equal\<type>, equal_to\<type>, not_equal_to\<type>
* 3个逻辑运算：分别对应于&&，||，!运算符：logical_and\<type>, logical_or\<type>, logical_not\<type>

```cpp
#include <functional>
sort(vec.begin(), vec.end(), greater<int>());
binary_search(vec.begin(), vec.end(), elem, greater<int>());

// 泛型算法transform()
tramsform(fib.begin(), fib.end(), // 欲转换的元素范围
          pell.begin(), // 所指元素将应用于转换操作上
          fib_plus_pell.begin(), // 所指位置及后继空间用来存放转换结果
          plus<int>()); // 想要实施的转换操作
```

### Function Object Adapters

function object adapter会对function object进行修改操作。所谓binder adapter会将function object的参数绑定至某特定值身上，使binary function object转化为unary function object。

标准程序库提供了两个binder adapter：bind1st会将指定值绑定至第一操作数，bind2nd则将指定值绑定至第二操作数。

```cpp
template <typename InputIterator, typename OutputIterator, 
          typename ElemType, typename Comp>
OutputIterator
filter(InputIterator first, InputIterator last,
       OutputIterator at, const ElemType &val, Comp pred)
{
    while ((first = find_if(first, last, bin2nd(pred, val))) != last)
    {
        cout << "found value: " << *first << endl;

        *at++ = *first++;
    }
    return at;
}

int main()
{
    const int elem_size = 8;

    int ia[elem_size] = {12, 8. 43. 0. 6. 21. 3. 7};
    vector<int> ivec(ia, ia+elem_size);

    int ia2[elem_size];
    vector<int> ivec2(elem_size)；

    cout << "filtering integer array for values less than 8\n";
    filter(ia, ia+elem_size, ia2, elem_size, less<int>());

    cout << "filtering integer vector for values greater than 8\n";
    filter(ivec.begin(), ivec.end(), ivec2.begin(), elem_size, greater<int>());
}
```

另一种adapter是所谓的negator，它会逆转function object的真伪值。not1可逆转unary function object的真伪值，not2可逆转binary function object的真伪值。

```cpp
while ((iter = 
        find_if(iter, vec.end(), 
                not1(bind2nd(less<int>, 10)))) 
                != vec.end())
```

## 3.7 使用Map

```cpp
#include <map>
#include <string>
map<string, int> words;

words["vermeed"] = 1;

string tword;
while (cin >> tword)
    words[tword]++;

map<string, int>::iterator it = words.begin();
for ( ; it != words.end(); ++it)
    cout << "key: " << it->first
         << "value: " << it->second << endl; 
```

欲查询map内是否存在某个key，有3种方法

```cpp
// 1. 把key当索引使用
int count = 0;
if (!(count = words["vermeer"]))
    // vermeer并不存在于words mao内

// 2. map的find()函数
int count = 0;
map<string, int>::iterator it;

it = words.find("vermeer");
if (it != words.end())
    count = it->second;

// 3. map的count()函数
int count = 0;
string search_word("vermeer");

if (words.count(search_word))
    count = words[search_word];
```

## 3.8 使用Set

```cpp
#include <set>
#include <string>
set<string> word_exclusion;

while (cin >> tword)
{
    if (word_exclusion.count(tword))
        continue
    words[tword]++;
}

// 默认情形下，set元素皆依据其所属型别默认的less-than运算符进行排列
int ia[10] = {1, 3, 5, 8, 5, 3, 1, 5, 8, 1};

vector<int> vec(ia, ia+10);
set<int> iset(vec.begin(), vec.end()); // {1, 3, 5, 8}

iset.insert(ival);
iset.insert(vec.begin(), vec.end());

set<int>::iterator it = iset.begin();
for ( ; it != iset.end(); ++it)
    cout << *it << ' ';
cout << endl;
```

泛型算法中与set相关的算法：set_intersection(), set_union(), set_difference(), set_symmetric_difference()。

## 3.9 如何使用Iterator Inserters

标准程序库提供了3个所谓的insertion adapters，这些adapter让我们得以避免使用容器的assignment运算符。

* back_inserter()会以容器的push_back()函数取代assignment运算符。对vector来说，这是比较合适的inserter。派给back_inserter的引数，应该就是容器本身

```cpp
vector<int> result_vec;
unique_copy(ivec.begin(), ivec.end(), back_inserter(result_vec));
```

* inserter()会以容器的insert()函数取代assignment运算符。inserter()接受两个参数：一个是容器，一个是iterator，指向容器内的安插操作起始点

```cpp
vector<string> svec_res;
unique_copy(svec.begin(), svec.end(), inserter(svec_res, svec_res.end()));
```

* front_inserter()会以容器的push_front()函数取代assignment运算符。这个inserter只适用于list和deque

```cpp
list<int> ilist_clone;
copy(ilist.begin(), ilist.end(), front_inserter(ilist_clone));
```

```cpp
#include <iterator>

int main()
{
    const int elem_size = 8;

    int ia[elem_size] = {12, 8, 43, 0, 6, 21, 3, 7};
    vector<int> ivec(ia, ia+elem_size);

    int ia2[elem_size];
    vector<int> ivec2;

    cout << "filtering integer array for values less than 8\n";
    filter(ia, ia+elem_size, ia2, elem_size, less<int>());

    cout << "filtering integer vector for values greater than 8\n";
    filter(ivec.begin(), ivec.end(), back_inserter(ivec2), elem_size, greater<int>());
}
```

## 3.10 使用iostream Iterators

```cpp
#include <iostream>
#include <iterator>
#include <algorithm>
#include <vector>
#include <string>
using namespace std;

int main()
{
    istream_iterator<string> is(cin); // first
    istream_iterator<string> eof; // last

    vector<string> text;
    copy(is, eof, back_inserter(text));

    sort(text.begin(), text.end());

    ostream_iterator<string> os(cout, " ");
    copy(text.begin(), text.end(), os);
}
```

```cpp
#include <iostream>
#include <fstream>
#include <iterator>
#include <algorithm>
#include <vector>
#include <string>
using namespace std;

int main()
{
    ifstream in_file("input_file.txt");
    ofstream out_file("output_file.txt");

    if (!in_file || !out_file)
    {
        cerr << "!! unable to open the nucessary files.\n";
        return -1;
    }

    istream_iterator<string> is(in_file); // first
    istream_iterator<string> eof; // last

    vector<string> text;
    copy(is, eof, back_inserter(text));

    sort(text.begin(), text.end());

    ostream_iterator<string> os(out_file, " ");
    copy(text.begin(), text.end(), os);
}
```