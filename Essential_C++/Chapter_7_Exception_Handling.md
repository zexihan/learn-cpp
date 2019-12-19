# Chapter 7 Exception Handling

## 7.1 抛出异常（Throwing an Exception）

```cpp
inline void Triangular_iterator::check_integrity()
{
    if (_index >= Triangular::_max_elems)
        iterator_overflow ex = iterator_overflow(_index, Triangular::_max_elems);
        throw ex;

    if (_index >= Triangular::_elems.size())
        Triangular::gen_elements(_index + 1);
}
```

所谓异常（exceptions）是某种对象，对简单的异常对象可以设计为整数或字符串。大部分时候，被抛出的异常都属于特定的异常类。

## 7.2 捕捉异常（Catching an Exception）

我们可以利用单一或连串的catch子句来捕捉（catch）被抛出的异常对象。catch子句由3部分组成：关键词catch、小括号内的一个型别或对象、大括号内的一组语句（用以处理异常）。

```cpp
extern void log_message(const char*);
extern string err_messages[];
extern ostream log_file;

bool some_function()
{
    bool status = true;

    // ... 

    catch(int errno) {
        log_message(err_messages[errno]);
        status = false;
    }
    catch(const char *str) {
        log_message(str);
        status = false;
    }
    catch(iterator_overflow &iof) {
        iof.what_happened(log_fiole);
        status = false;
    }
    return status;
}

// which process the following 3 exceptions
throw 42;
throw "panic: no buffer!";
throw iterator_overflow(_index, Triangular::_max_elem);
```

## 7.3 提炼异常（Trying for an Exception）

```cpp
bool has_elem(Triangular_iterator first,
              Triangular_iterator last, int elem)
{
    bool status = true;

    try
    {
        while (first != last)
        {
            if (*first == elem)
                return status;
            ++first;
        }
    }
    // try块内的程序代码执行时，如果有抛出任何异常。
    // 我们只捕捉型别为iterator_overflow的异常
    catch(iterator_overflow &iof)
    {
        log_message(iof.what_happened());
        log_message("check if iterators address same container");
    }

    status = false;
    return status;
}
```

## 7.4 局部资源管理（Local Resource Management）

skip

## 7.5 标准异常（The Standard Exceptions）

skip