# Chapter 19

## 19.1 Controlling Memory Allocation

```cpp

```

## 19.2 Run-Time Type Identification

```cpp

```

## 19.3 Enumerations

Enumerations let us group together sets of integral constants. Like classes, each enumeration defines a new type. Enumerations are literal types.

C++ has two kinds of enumerations: scoped and unscoped. The new standard introduced scoped enumerations.

```cpp
enum class open_modes {input, output, append}; // scoped [c++11]

enum color {red, yellow, green}; unscoped enumeration
// unnamed, unscoped enum
enum {floatPrec = 6, doublePrec = 10, double_doublePrec = 10};
```

**Enumerators**

```cpp
enum color {red, yellow, green}; // unscoped enumeration
enum stoplight {red, yellow, green}; // error: redefines enumerators
enum class peppers {red, yellow, green}; // ok: enumerators are hidden

color eyes = green; // ok: enumerators are in scope for an unscoped enumeration
peppers p = green; // error: enumerators from peppers are not in scope; color::green is in scope but has the wrong type
color hair = color::red; // ok: we can explicitly access the enumerators
peppers p2 = peppers::red; // ok: using red from peppers
```

By default, enumerator values start at 0 and each enumerator has a value 1 greater than the preceding one. However, we can also supply initializers for one or more enumerators:

```cpp
enum class intTypes {
    charTyp = 8, shortTyp = 16, intTyp = 16,
    longTyp = 32, long_longTyp = 64
};
```

## 19.4 Pointer to Class Member

```cpp

```

## 19.5 Nested Classes

```cpp

```

## 19.6 `union`: A Space-Saving Class

A `union` is a special kind of class. A `union` may have multiple data members, but at any point in time, only one of the members may have a value. When a value is assigned to one member of the `union`, all other members become undefined. The amount of storage allocated for a `union` is at least as much as is needed to contain its largest data member. Like any class, a `union` defines a new type.

**Defining a `union`**

```cpp
// objects of type Token have a single member, which could be of any of the listed types
union Token {
    // members are public by default
    char cval;
    int ival;
    double dval;
};
```

**Using a `union` Type**

```cpp
Token first_token = {'a'}; // initializes the cval member
Token last_token; // uninitialized Token object
Token *pt = new Token; // pointer to an uninitialized Token object

last_token.cval = 'z';
pt->ival = 42;
```

**Anonymous `union`s**

```cpp
union { // anonymous union
    char cval;
    int ival;
    double dval;
}; // defines an unnamed object, whose members we can access directly
cval = 'c'; // assigns a new value to the unnamed, anonymous union object
ival = 42; // that object now holds the value 42
```

## 19.7 Local Classes

```cpp

```

## 19.8 Inherently Nonportable Features

```cpp

```
