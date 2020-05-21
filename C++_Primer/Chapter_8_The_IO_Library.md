# Chapter 8 The IO Library

## 8.1 The IO Classes

```cpp
// Header
#include <iostream>
// Type
istream, wistream // reads from a stream
ostream, wostream // writes to a stream
iostream, wiostream // reads and writes a stream
```

```cpp
// Header
#include <fstream>
// Type
ifstream, wifstream // reads from a file
ofstream, wofstream // writes to a file
fstream, wfstream // reads and writes a file
```

```cpp
// Header
#include <sstream>
// Type
istringstream, wistringstream // reads from a string
ostringstream, wostringstream // writes to a string
stringstream, wstringstream // reads and writes a string
```

**Flushing the Output Buffer**

```cpp
cout << "hi!" << endl; // writes hi and a newline, then flushes the buffer
cout << "hi!" << flush; // writes hi, then flushes the buffer; adds no data
cout << "hi!" << ends; // writes hi and a null, then flushes the buffer
```

## 8.2 File Input and Output

```cpp
ifstream in(ifile); // construct an ifstream and open the given file
ofstream out; // output file stream that is not associate with any file
```

```cpp
ifstream input(argv[1]);
ofstream output(argv[2]);
Sales_data total;
if (read(input, total)) {
    Sales_data trans;
    while(read(input, trans)) {
        if (total.isbn() == trans,isbn())
            total.combine(trans);
        else {
            print(output, total) << endl;
        }
    }
    print(output, total) << endl;
} else 
    cerr << "No data?!" << endl;
```

**The `open` and `close` Members**

```cpp
ifstream in(ifile);
ofstream out;
out.open(ifile + ".copy");

if (out) // check that the open succeeded

in.close();
in.open(ifile + "2");
```

### 8.2.2 File Modes

```cpp
in
out
app
ate
trunc
binary
```

```cpp
file1 is truncated in each of these cases
ofstream out("file1"); // out and trunc are implicit
ofstream out2("file1", ofstream::out); // trunc is implicit
ofstream out3("file1", ofstream::out | ofstream::trunc);

// to preserve the file's contents, we must explicitly specify app mode
ofstream app("file2", ofstream::app); // out is implicit
ofstream app2("file2", ofstream::out | ofstream::app)
```

## 8.3 `string` Streams


