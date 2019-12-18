# Chapter 5 Object-Oriented Programming

## 5.1 面向对象（Object-Oriented）编程概念

面向对象编程概念的两项最主要的特性是：继承（inheritance）和多态（polymorphism）。前者使我们得以将一群相关的类组织起来，并让我们得以分享其间的共通数据和操作行为，后者让我们在这些类之上进行编程时，可以如同操纵单一个体，而非相互独立的类，并赋予我们更多弹性来加入或移除任何特定类。

在C++中，父类被称为基类（base class），子类被称为派生类（derived class）。

多态（polymorphism）让基类的pointer或reference得以十分透明地（transparently）指向其任何一个派生类的对象。

在非面向对象的编程风格中，mat.check_in();，编译器在执行之前的编译时期就依据mat所属的类决定究竟执行起哪一个check_in()函数，这种方式被称为静态绑定（static binding）。而面向对象编程方法中，编译器无法得知究竟那一份check_in()函数会被调用，仅能在执行过程中依据mat所寻址的实际对象来决定调用哪一个check_in()。“找出实际被调用的究竟是哪一个派生类的check_in()函数”这一决议操作会延迟至执行期（run-time）才进行，此即动态绑定（Dynamic binding）。



## 5.2 漫游：面向对象编程思维

```cpp
class LibMat {
public:
    LibMat() { cout << "LibMat::LibMat() default constructor!\n"; }
    virtual ~LibMat() { cout << "LibMat::~LibMat() destructor!\n"; }
    virtual void print() const { cout << "LibMat::print() -- I am a LibMat object!\n"; }
};
```

```cpp
class Book : public LibMat {
public:
    Book(const string &title, const string &author)
        : _title(title), _author(author) {
        cout << "Book::Book( " << _title
             << ", " << _author << " ) constructor\n";
    }

    virtual ~Book() {
        cout << "Book::~Book() destructor!\n";
    }

    virtual void print() const {
        cout << "Book::print() -- I am a Book object!\n"
             << "My title is: " << _title << '\n'
             << "My author is: " << _author << endl;
    }

    const string& title() const { return _title }
    const string& author() const { return _author }

protected:
    string _title;
    string _author;
};
```

被声明为protected的所有成员都可以被派生类直接取用，除此之外，都不得直接取用protected成员。

```cpp
class AudioBook : public Book {
public:
    AudioBook( const string &title,
               const string &author, const string &narrator )
        : Book(title, author),
          _narrator(narrator)
    {
        cout << "AudioBook::AudioBook( " << _title
             << ", " << _author
             << ", " << _narrator
             << " ) constructor\n";
    }

    ~AudioBook()
    {
        cout << "AudioBook::~AudioBook() destructor!\n";
    }

    virtual void print() const {
        cout << "AudioBook::print() -- I am an AudioBook object!\n"
             << "My title is: " << _title << '\n'
             << "My author is: " << _author << '\n'
             << "My narrator is: " << _narrator << endl;
    }
    const string& narrator() const { return _narrator; }

protected:
    string _narrator;
}
```

## 5.3 不带继承的多态（Polymorphism without Inheritance）

skip

## 5.4 定义一个抽象基类（Abstract Base Class）

定义抽象类的第一个步骤就是找出所有子类共通的操作行为。下一步，便是设法找出哪些操作行为与型别相依（type-dependent），也就是说有哪些操作行为必须根据不同的派生类而有不同的实现方式。第三步便是试着找出每个操作行为的存取层级（access level）。

```cpp
class num_sequence{
public:
    virtual ~num_sequence(){};

    virtual int elem(int pos) const = 0;
    virtual const char* what_am_i() const = 0;
    static int max_elems() { return _max_elems; }
    virtual ostream& print(ostream &os = cout) const = 0;

protected:
    virtual void gen_elems(int pos) const = 0;
    bool check_integrity(int pos) const;

    const static int _max_elems = 1024;
}
```

每个虚拟函数，要么得有其定义，要么将虚拟函数赋值为0，可设为纯虚拟函数（pure virtual function）.

## 5.5 定义一个派生类

```cpp

```

## 5.6 运用继承体系（Using an Inheritance Hierarchy）

```cpp

```

## 5.7 基类应该多么抽象

```cpp

```

## 5.8 初始化、析构、复制（Initialization, Destruction, and Copy）

```cpp

```

## 5.9 在派生类中定义一个虚拟函数

```cpp

```

## 5.10 执行期的型别鉴定机制（Run-Time Type Identification）

```cpp

```
