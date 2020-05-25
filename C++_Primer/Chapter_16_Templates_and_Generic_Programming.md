# Chpater 16 Templates and Generic Programming

A template is a blueprint or formula for creating classes or functions. When we use a generic type, such as `vector`, or a generic function , such as `find`, we supply the information needed to transform that blueprint into a specific class or function. That transformation happens during compilation.

## 16.1 Defining a Template

```cpp
// return 0 if the values are equal, -1 if v1 is smaller, 1 if v2 is smaller
int compare(const string &v1, const string &v2) {
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}
int compare(const double &v1, const double &v2) {
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}
```

### 16.1.1 Function Templates

```cpp
template <typename T>
int compare(const T &v1, const T &v2) {
    if (v1 < v2) return -1;
    if (v2 < v1) return 1;
    return 0;
}
```

**Instantiating a Function Template**

```cpp
cout << compare(1, 0) << endl; // T is int
```

**Template Type Parameters**

```cpp
// ok: same type used for the return type and parameter
template <typename T> T foo(T* p) {
    T tmp = *p; // tmp will have the type to which p points
    // ...
    return tmp;
}

// ok: no distinction between typename and class in a template parameter list
template <typename T, class U> calc (const T&, const U&);
```
### 16.1.2 Class Templates

## 16.2 Template Argument Deduction

## 16.3 Overloading and Templates

## 16.4 Variadic Templates

## 16.5 Template Specializations
