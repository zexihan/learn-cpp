# Chapter 7 Classes

## 7.1 Defining Abstract Data Types

```cpp
struct Sales_data {
    std::string isbn() const { return bookNo; }
    Sales_data& combine(const Sales_data&);
    double avg_price() const;

    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
};
```

**Introducing `this`**

Inside a member function, we can refer directly to the members of the object on which the function was called. Any direct use of a member of the class is assumed to be an implicit reference through this.

```cpp
std::string isbn() const { return this->bookNo; }
```

**Introducing `const` Member Functions**

The purpose of the `const` following the parameter list is to modify the type of the implicit `this` pointer. 

**Defining a Member Function outside the Class**

```cpp
double Sales_data::avg_price() const {
    if (units_sold)
        return revenue/units_sold;
    else
        return 0;
}
```

**Defining the Sales_data Constructors**

```cpp
struct Sales_data {
    // constructors added
    Sales_data() = default; // [c++11]
    Sales_data(const std::string &s): bookNo(s) { }
    Sales_data(const std::string &s, unsigned n, double p):
               bookNo(s), units_sold(n), revenue(p*n) { }
    Sales_data(std::istream &);
    // other members as before
    std::string isbn() const { return bookNo; }
    Sales_data& combine(const Sales_data&);
    double avg_price() const;
    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
}
```

**Defining a Constructor outside the Class Body**

```cpp
Sales_data::Sales_data(std::istream &is) {
    read(is, *this); // read will read a transaction from is into this object
}
```

## 7.2 Access Control and Encapsulation

```cpp
class Sales_data {
public: // access specifier added
    Sales_data() = default;
    Sales_data(const std::string &s): bookNo(s) { }
    Sales_data(const std::string &s, unsigned n, double p):
               bookNo(s), units_sold(n), revenue(p*n) { }
    Sales_data(std::istream &);
    std::string isbn() const { return bookNo; }
    Sales_data &combine(const Sales_data&);
private: // access specifier added
    double avg_price() const;
    std::string bookNo;
    unsigned units_sold = 0;
    double revenue = 0.0;
}
```

**Using the `class` or `struct` Keyword**

The only difference between `struct` and `class` is the default access level.

### 7.2.1 Friends

A class can allow another class or function to access its non`public` members by making that class or function a friend.

```cpp
class Sales_data {
    // friend declarations for nonmember Sales_data operations added
    friend Sales_data add(const Sales_data&, const Sales_data&);
    friend std::istream &read(std::istream&, Sales_data&);
    friend std::ostream &print(std::ostream&, const Sales_data&);
    // other members and access specifiers as before
    // ...
};
// declarations for nonmember parts of the Sales_data interface
Sales_data add(const Sales_data&, const Sales_data&);
std::istream &read(std::istream&, Sales_data&);
std::ostream &print(std::ostream&, const Sales_data&);
```

## 7.3 Additional Class Features

**`mutable` Data Members**

A `const` member function may change a `mutable` member.

```cpp
class Screen {
public: 
    void some_member() const;
private:
    mutable size_t access_ctr; // may change even in a const object
    // other members as before
};
void Screen::some_member() const {
    ++access_ctr; // keep a count of the calls to any member function
    // whatever other work this member needs to do
}
```

## 7.4 Class Scopes


## 7.5 Constructors Revisited

### 7.5.1 Constructor Initializer List

**Constructor Initializers Are Sometimes Required**

Members that are `const` or references must be initialized.

```cpp
class ConstRef {
public:
    ConstRef(int ii);
private:
    int i;
    const int ci;
    int &ri;
}
```

```cpp
// error: ci and ri must be initialized
ConstRef::ConstRef(int ii) { // assignments
    i = ii; // ok
    ci = ii; // error: cannot assign to a const
    ri = i; // error: ri was never initialized
}
```

```cpp
// ok: explicitly initialize reference and const members
ConstRef::ConstRef(int ii): i(ii), ci(ii), ri(i) { }
```

### 7.5.2 Delegating Constructors [c++11]

```cpp
class Sales_data {
public:
    // nondelegating constructor initializes members from corresponding arguments
    Sales_data(std::string s, unsigned cnt, double price):
        bookNo(s), units_sold(cnt), revenue(cnt*price) {

    }
    // remaining constructors all delegate to another constructor
    Sales_data(): Sales_data("", 0, 0) {}
    Sales_data(std::string s): Sales_data(s, 0, 0) {}
    Sales_data(std::istream &is): Sales_data() {
        read(is, *this);
    }
    // other members as before
}
```

## 7.6 `static` Class Members

Class sometimes need members that are associated with the class, rather than with individual objects of the class type.

**Declaring `static` Members**

```cpp
class Account {
public:
    void calculate() {
        amount += amount * interestRate;
    }
    static double rate() {
        return interestRate;
    }
    static void rate(double);
private:
    std::string owner;
    double amount;
    static double interestRate;
    static double initRate();
};
```

**Using a Class `static` Member**

```cpp
double r;
r = Account::rate(); // access a static member using the scope operator
```

```cpp
Account ac1;
Account *ac2 = &ac1;
// equivalent ways to call the static member rate function
r = ac1.rate(); // throughan Account object or reference
r = ac2->rate(); // through a pointer to an Account object
```

**Defining `static` Members**

When we define a `static` member outside the class, we do not repeat the static keyword. The keyword appears only with the declaration inside the class body.