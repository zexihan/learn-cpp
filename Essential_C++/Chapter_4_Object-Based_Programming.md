# Chapter 4 Object-Based Programming

一般而言，class由两部分组成：一组公开的（public）操作函数和运算符，以及一组私有的（private）实现细节。这些操作函数和运算符被称为class's member function（成员函数），并代表这个class的公开接口。class的private实现细目可由member functions的定义式以及与此class相关的任何数据组成。

## 4.1 如何实现一个class

```cpp
class Stack {
public: 
    // 任何操作函数如果执行成功，就返回true
    // pop和peak会将字符串内容置于elem内
    bool push(const string&);
    bool pop(string &elem);
    bool peek(string &elem);

    bool empty();
    bool full();

    // size()定义于class本身内
    // 其它members则仅仅只是声明
    int size() { return _stack.size() }

private: 
    vector<string> _stack;
}
```

所有member functions都必须在class主体内进行声明，可在主体外进行定义。主体内定义的member function自动被视为inline函数。

```cpp
// 主体外定义member function
inline bool
Stack::empty()
{
    return _stack.empty();
}

bool
Stack::pop(string %elem)
{
    if (empty())
        return false;
    
    elem = _stack.back();
    _stack.pop_back();
    return true;
}
```

对inline函数而言，定义于class主体内或主体外并没有什么分别。然而像non-member inline function一样，它也应该被置于头文件中。class定义式及其inline member function通常都会被放在与class同名的头文件中，接扩展名.hpp。

non-inline member functions应该在程序代码文件中定义，该文件通常和class同名，其后接扩展名.cpp。

```cpp
inline bool Stack::full()
    { return _stack.size() == _stack.max_size(); }

bool Stack::peek(string &elem)
{
    if (empty())
        return false;
    
    elem = _stack.back();
    return true;
}

bool Stack::push(const string &elem)
{
    if (fill())
        return false;
    
    _stack.push_back(elem);
    return true;
}
```

## 4.2 什么是Constructor（构造函数）和Destructors（析构函数）

constructors的函数名称必须与class名称相同。constructor不应指定返回型别，也不需返回任何值。它可以被重载。

```cpp
class Triangular {
public:
    // 一组重载的constructors
    Triangular(); // default constructors
    Triangular(int len);
    Triangular(int len, int beg_pos);

    // ...
    
private:
    int _length;
    int _beg_pos;
    int _next;
}
```

最简单的constructor是所谓的default constructor。它不需要任何引数（arguments）。这意味着两种情况。第一，它不接受任何参数：

```cpp
Triangular::Triangular()
{
    _length = 1;
    _beg_pos = 1;
    _next = 0;
}
```

第二，它为每个参数提供了默认值：

```cpp
class Triangular {
public:
    Triangular(int len = 1, int bp = 1);
};

Triangular::Triangular(int len, int bp)
{
    _length = len > 0 ? len : 1;
    _beg_pos = bp > 0 ? bp : 1;
    _next = _beg_pos - 1;
}
```

由于我们为两个整数提供了默认值，所以default constructor同时支持原本的3个constructors。

### Member Initialization List（成员初值表）

constructor定义式的第二种初始化语法。

```cpp
Triangular::Triangular(const Triangular &rhs)
    : _length(rhs._length),
      _beg_pos(rhs._beg_pos), _next(rhs._beg_pos - 1)
{ } // empty
```

```cpp
class Triangular {
public:
    // ...
private:
    string _name;
    int _next, _length, _beg_pos;
};

Triangular::Triangular(int len, int bp)
    : _name("Triangular")
{
    _length = len > 0 ? len : 1;
    _beg_pos = bp > 0 ? bp : 1;
    _next = _beg_pos - 1;
}
```

所谓destructor是用户自定义的一个class member。一旦某个class提供有destructor，当其objects结束生命时，便会自动调用destructor处理善后。destructor主要用来释放在constructor中或对象生命周期中配置的资源。

destructor的名称有严格规定：class名称再加上‘~’前导符号。它绝对不会有返回值，也没有任何参数。正由于其参数表是空的，所以也绝不可能被重载。

```cpp
class Matrix {
public:
    Matrix(int row, int col)
        : _row(row), _col(col)
    {
        // constructor进行资源的配置
        _pmat = new double[row * col];
    }

    ~Matrix()
    {
        delete [] _pmat;
    }

    private: 
        int _row, _col;
        double* _pmat;
}
```

### Memberwise Initialization（成员逐一初始化）

```cpp
Triangular tri1(8);
Triangular tri2 = tri1;
```

默认情形之下，当我们以某个class object作为另一个object的初值时，class data members会被依次复制，此所谓default memberwise initialization。

但这对Matrix class而言并不适当，会使得两个对象的_pmat都寻址到heap内的同一个数组。我们可以通过为Matrix提供另一个copy constructor达到目的。

```cpp
Matrix::Matrix(const Matrix &rhs)
    : _row(rhs._row), _col(rhs._col)
{
    int elem_cnt = _row * _col;
    _pmat = new double[elem_cnt];

    for (int ix = 0; ix < elem_cnt; ++ix)
        _pmat[ix] = rhs._pmat[ix];
}
```

## 4.3 何谓mutable（可变）和const（不变）

class设计者必须在member function身上标注const，以此告诉编译器：这个member function不会更动class object的内容。

```cpp
class Triangular {
public:
    // const member functions
    int length() const { return _length; }
    int beg_pos() const { return _beg_pos; }
    int elem(int pos) const;

    // non-const member functions
    bool next(int &val);
    void next_reset() { _next = _beg_pos - 1; }

private:
    int _length;
    int _beg_pos;
    int _next;

    // static data members
    static vector<int> _elems;
}
```

const修饰词紧接于函数参数表之后，凡是在class主体以外定义者，如果它是一个const member function，那就必须同时在声明式与定义式中都指定const。

```cpp
int Triangular::elem(int pose) const
    { return _elems[pos - 1]; }
```

```cpp
class val_class {
public:
    val_class(const BigClass &v)
        : _val(v) { }
    
    // wrong!
    BigClass& val() const { return _val; }

private:
    BigClass _val;
}
```

返回一个non-const reference指向_val，实际上等于将_val开放出去，允许程序在其它地方加以修改。由于member functions可以根据const与否而重载，因此有个方法可以解决问题：提供两份定义，一为const版本，一为non-const版本。non-const class object 会调用non-const版的val()，const class object则会调用const版的val()。

```cpp
class val_class {
public:
    const BigClass& val() const { return _val; }
    BigClass& val() { return _val; }
    // ...
}

void example(const BigClass *pbc, BigClass &rbc)
{
    pbc->val(); // call const version
    rbc.val(); // call non-const version
}
```

### Mutable Data Member（可变的数据成员）

将_next表示为mutable，我们就可以宣城：对_next所做的改变并不会破坏class object的常数性。

```cpp
class Triangular {
public:
    bool next(int &val) const;
    void next_reset() const { _next = _beg_pos - 1; }
    // ...

private:
    mutable int _next;
    int _beg_pos;
    int _length;
};

int sum(const Triangular &trian)
{
    if (!train.length())
        return 0;
    int val, sum = 0;
    trian.next_reset();
    while (trian.next(val))
        sum += val;
    return sum;
}
```

现在，next()和next_reset()既可以修改_next的值，又可以被声明为const member functions，sum()中的const object trian也可以调用next_reset()和next()更动_next值。

## 4.4 什么是this指针

```cpp
Triangular tr1(8);
Triangular tr2(8, 9);

// copy tr2 to tr1
copy(&tr1, tr2);

Triangular& Triangular::copy(Triangular *this, const Triangular &rhs)
{
    if (this != &rhs)
    {
        this->_length = rhs._length;
        this->_beg_pos = rhs._beg_pos;
        this->_next = rhs._beg_pos - 1;
    }
    return *this;
};
```

## 4.5 Static Class Member（静态的类成员）

static data members用来表示唯一一份可共享的members。它可以在同型的所有对象中被存取。

```cpp
class Triangular {
public:
    // ...
private:
    static vector<int> _elems;
};

vector<int> Triangular::_elems;
int Triangular::_initial_size = 8;

Triangular::Triangular(int len, int beg_pos)
    : _length(len > 0 ? len : 1), 
      _beg_pos(beg_pos > 0 ? : beg_pos : 1)
{
    _next = _beg_pos - 1;
    int elem_cnt = _beg_pos + _length - 1;

    if (_elems.size() < elem_cnt)
        gen_elements(elem_cnt);
}

class intBuffer {
public:
    // ...
private:
    static const int_buf_size = 1024;
    int _buffer[_buf_size];
};
```

### Static Member Function（静态成员函数）

member function只有在“不存取任何non-static members”的条件下才能够被声明为static，声明方式是在声明式之前加上关键词static。当我们在class主体外部进行member functions的定义时，不需要重复加上关键词static（这个规则也适用于static data members）。

```cpp
class Triangular {
public:
    static bool is_elem(int);
    static void gen_elements(int length);
    static void gen_elems_to_value(int value);
    static void display(int length, int beg_pos, ostream &os = cout);
    // ...
private:
    static const int _max_elems = 1024;
    static vector<int> _elems;
}

bool Triangular::is_elem(int value)
{
    if (!_elems.size() || _elems[_elems.size()-1] < value)
        gen_elems_to_value(value);
    
    vector<int>::iterator found_it;
    vector<int>::iterator end_it = _elems.end();

    found_it = find(_elems.begin(), end_it, value);
    return found_it != end_it;
}

#include "Triangular.h";

int main()
{
    // ...
    bool is_elem = Triangular::is_elem(ival);
}
```


## 4.6 打造一个Iterator Class

```cpp
class Triangular_iterator
{
public: 
    Triangular_iterator(int index) : _index(index - 1) { }
    bool operator==(const Triangular_iterator&) const;
    bool operator!=(const Triangular_iterator&) const;
    int operator*() const;
    Triangular_iterator& operator++(); // prefix
    Triangular_iterator operator++(int); // postfix
private:
    void check_integrity() const;
    int _index;
};
```

Definitions of the operators are skipped here.

运算符重载的规则：

* 不可以引入新的运算符。除了., .*, ::, ?:4个运算符，其它的运算符皆可被重载。
* 运算符的操作数（operand）数目不可改变。每个二元运算符都需要两个操作数，每个一元运算符都需要恰好一个操作数。因此，我们无法定义出一个equality运算符，并令它接受两个以上或两个以下的操作数。
* 运算符的优先级不可改变。
* 运算符函数的参数列中，必须至少有一个参数为class型别。也就是说，我们无法为诸如指针之类的non-class型别，重新定义其原已存在的运算符，当然更无法为它引进新运算符。

### 嵌套型别（Nested Types）

typedef可以为某个型别设定另一个不同的名称：

```cpp
typedef existing_type new_name;

Fibonacci::iterator fit = fib.begin();
string::iterator sit = file_name.begin();
```

## 4.7 合作关系必须建立在友谊的基础上

skip

## 4.8 实现一个copy assignment operator

skip

## 4.9 实现一个function object

skip

## 4.10 将iostream运算符重载

skip

## 4.11 指针：指向Class Member Functions

skip