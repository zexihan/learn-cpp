# Chapter 2 Procedural Programming

## 2.1 如何撰写函数

```cpp
bool fibon_elem(int pos, int &elem)
{
    if (pos <= 0 || pos > 1024)
        { elem = 0; return false; }
    elem = 1
    int n_2 = 1, n_1 = 1;
    for (int ix = 3; ix <= pos; ++ix)
    {
        elem = n_2 + n_1;
        n_2 = n_1;
        n_1 = elem;
    }
    return true;
}


void print_msg(ostream &os, const string &msg)
{
    if (msg.empty())
        return;
    
    os << msg;
}
```

## 2.2 调用（invoking）一个函数

### Pass by Reference

```cpp
// pass by reference
void display(const vector<int> &vec)
{
    for (int ix = 0; ix < vec.size(); ++ix)
    {
        cout << vec[ix] << ' ';
        cout << endl;
    }
}

// use pointer
void display(const vector<int> *vec)
{
    if (!vec)
    {
        cout << "display(): the vector pointer is 0\n";
        return;
    }
    for (int ix = 0; ix < vec->size(); ++ix)
        cout << (*vec)[ix] << ' ';
    
    cout << endl;
}

int main()
{
    int ia[8] = {8, 32, 3, 13, 1, 21, 5, 2};
    vector<int> vec(ia, ia+8);

    cout << "vector before sort: ";
    display(&vec);

    // ...
}
```

### 生存空间（scope）及生存范围（extent）

函数内定义的对象，只存活于函数执行之际，如果将这些所谓局部对象的地址返回，会导致执行器错误。

为对象配置的内存，其存活期称为storage duration或extent。local extent vs. file extent。

对象在程序内的存活区域称为该对象的scope。local scope vs. file scope vs. static scope（该对象的内存在main() 开始执行之前便已经配置好）。

内建类型的对象，如果定义在file scope之内，必被初始化为0。但如果被定义于local scope之内，除非指定其初值，否则不会被初始化

### 动态内存管理

不论local scope或file extent，对我们而言都是由系统自动管理。第三种存储期形式称为dynamic extent（动态范围），其内存由程序的自由空间配置而来，也称为heap memory（堆内存），必须由程序员自行管理，其配置是通过new表达式来达成的，而其释放则经由delete表达式完成。

```cpp
int *pi;
pi = new int;

pi = new int(1024);

int *pia = new int[24];

delete pi;

delete [] pia;

// 无需检验pi是否非零，编译器会替我们检查
```

## 2.3 提供默认参数值（Default Parameter Values）

```cpp
void bubble_sort(vector<int> &vec, ofstream *ofil = 0)
{
    // ...
}

void display(const vector<int> &vec, ostream &os = cout)
{
    // ...
}


```

默认值的决议操作由最右边开始进行。

默认值只能够指定一次，可以在函数声明处或函数定义处。为了更高的可见度，我们将默认值置于函数声明处。


## 2.4 使用局部静态对象（Local Static Objects）

局部静态对象所处的内存空间，即使在不同的函数调用过程中，依然持续存在，也因此我们可以安全地返回elems的地址。

```cpp
const vector<int>* fibon_Seq(int size)
{
    const int max_size = 1024;
    static vector<int> elems;
    
    if (size <= 0 || size > max_size)
    {
        cerr << "fibon_Seq(): oops: invalid size: "
             << size << " -- can't fullfill request.\n";
        return 0;
    }
    
    for (int ix  = elems.size(); ix < size; ++ix)
    {
        if (ix == 0 || ix == 1)
            elems.push_back(1);
        else
            elems.push_back(elems[ix - 1] + elems[ix - 2]);
    }
    return &elems;
}
```

## 2.5 声明一个inline函数

将函数声明为inline，表示要求编译器在每个函数调用点上，将函数的内容展开。面对一个inline函数，编译器可将该函数的调用操作改以一份函数码副本取而代之。这使我们获得效率上的改善，其结果等于是把三个函数写入fibon_elem()内，但依然维持3个独立的运算单元。inline函数的定义常常被置于头文件中。

```cpp
bool is_size_ok(int size)
{
    // ...
}

const vector<int>* fibon_seq(int size)
{
    // call is_size_ok() ...
}

inline bool fibon_elem(int pos, int &elem)
{
    // call fibon_seq() ...
}
```

## 2.6 提供重载函数（Overloaded Functions）

编译器会将调用者提供的实际参数拿来和每个重载函数的参数比较，找出其中最适当的。

```cpp
void display_message(char ch);
void display_message(const string&);
void display_message(const string&, int);
void display_message(const string&, int, int);
```

## 2.7 定义并使用Template Functions（模板函数）

```cpp
void display_message(const string&, const vector<int>&);
void display_message(const string&, const vector<double>&);
void display_message(const string&, const vector<string>&);

// fucntion template
template <typename elemType>
void display_message(const string &msg, const vector<elemType> &vec)
{
    cout << msg;
    for (int ix = 0; ix < vec.size(); ++ix)
    {
        elemType t = vec[ix];
        cout << t << ' ';
    }
}

// overloaded function template
template <typename elemType>
void display_message(const string &msg, const vector<elemType> &vec);

template <typename elemType>
void display_message(const string &msg, const list<elemType> &lt);
```


## 2.8 函数指针（Pointers to Functions）带来更大的弹性

```cpp
const vector<int> *fibon_seq(int size);
const vector<int> *lucas_seq(int size);
const vector<int> *pell_seq(int size);
const vector<int> * triang_seq(int size);
const vector<int> *square_seq(int size);
const vector<int> *pent_seq(int size);

bool fibon_elem(int pos, int &elem)
{
    // call fibon_seq() ...
    const vector<int> *pseq = fibon_seq(pos);
}

bool seq_elem(int pos, int &elem, const vector<int>* (*seq_ptr)(int))
{
    // call seq_ptr()
    const vector<int> *pseq = seq_ptr(pos);
}

if (!seq_ptr)
    display_message("Internal Error: seq_ptr is set to null!");

const vector<int>* (*seq_ptr)(int) = 0;
seq_ptr = pell_seq; // obtain the function address

const vector<int>* (*seq_array[])(int) = {
    fibon_seq, lucas_seq, pell_seq,
    triang_seq, square_seq, pent_seq
};

enumns_type {
    ns_fibon, ns_lucas, ns_pell,
    ns_triang, ns_square, ns_pent
};

seq_ptr = seq_array[ns_pell];
```

## 2.9 设定头文件（Header Files）

函数的定义只能有一份，但是可以有很多份声明。inline函数的定义是例外，必须将inline函数的定义置于头文件中。

```cpp
// NumSeq.h
bool seq_elem(int pos, int &elem);
const vector<int> *fibon_seq(int size);
const vector<int> * lucas_seq(int size);

// 在file scope内定义的对象，如果可能被多个文件存取，就应该被声明于头文件中
const int seq_cnt = 6; // const object是一次定义规则下的例外
extern const vector<int>* (*seq_array[seq_cnt])(int); // 非const object，是一个指向const object的指针，extern使其定义变为声明
```

```cpp
#include "NumSeq.h"
void test_it()
{
    int elem = 0;
    if (seq_elem(1, elem) && elem == 1) // ...
}
```

