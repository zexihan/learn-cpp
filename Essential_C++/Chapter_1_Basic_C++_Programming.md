# Chapter 1 Basic C++ Programming

## 1.1 如何撰写C++程序

```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string user_name;
    cout << "Please enter your first name: ";
    cin >> user_name;
    cout << '\n'
         << "Hello, "
         << user_name
         << " ... and godbye! \n";
    
    return 0;
}
```

std是标准程序库所驻之命名空间的名称，标准程序库所提供的任何事物（如string class以及cout, cin这两个iostream类对象）都被封装在命名空间std内。

命名空间（namespace）是一种将程序库名称封装起来的方法，以避免和应用程序发生命名冲突。

using directive; 是让命名空间中名称曝光的最简单方法。

## 1.2 对象的定义与初始化

```cpp
#include <string>
string user_name;

int usr_val;

int num_tries = 0;
int num_right = 0;

int num_tries = 0, num_right = 0;

int num_tires(0); // constructor syntax

#include <complex>
complex<double> purei(0, 7); // 尖括号表示complex是一个template class（模板类）

double usr_score = 0.0; // C++支持3种浮点数：float单精度，double双精度，long double扩充精度

char usr_more;
cout << "Try another sequence? Y/N";
cin >> usr_more;

bool go_for_it = true;

const int max_tries = 3;
const double pi = 3.14159;

max_tries = 42; // wrong
```

## 1.3 撰写表达式（Expressions）

```cpp
// 整数除法：5 / 3 == 1
// 余数运算：5 % 3 == 2

const int line_size = 8;
int cnt = 1;

cout << a_string
     << ( cnt % line_size ? ' ' : '\n'); // conditional operator

// o == false, not 0 == true

cnt += 2; // compound assignment

cnt++; // postfix
cnt--;

++cnt; //prefix
--cnt;

bool usr_more = true;
char usr_rsp;

if (usr_rsp == 'N')
    usr_more = false;
else if (usr_rsp == 'n')
    usr_more = false;

if (usr_rsp == 'N' || usr_rsp == 'n')
    usr_more = false;

if (password && 
    validate(password) && 
    acct = retrieve_acct_info(password))
    // 处理账户相关事务

if (usr_more == false)
if (!usr_more)
```

运算符的优先级
* NOT
* *, / , %
* +, -
* <, >, <=, >=
* AND
* OR
* assignment

## 1.4 条件（Conditional）语句和循环（Loop）语句

条件语句

```cpp
if (usr_guess == next_elem)
{
    // right actions
}
else
{
    // wrong actions
}

if (num_tries == 1)
    cout << "One";
else if (num_tries == 2)
    cout << "Two";
else 
    cout << "Others";

switch (num_tries)
{
    case 1:
        cout << "One";
        break;
    case 2:
        cout << "Two";
        break;
    case 3:
        cout << "Three";
        break;
    default:
        cout << "Others";
        break;
}

switch (next_char)
{
    case 'a': case 'A':
    case 'e': case 'E':
        ++vowel_cnt;
        break;
    // ...
}
```

循环语句

```cpp
while (next_seq == true)
{
    // Display number sequence
    while ((got_it == false) && (go_for_it == true))
    {
        // Guess again
    }
}

// break; 结束循环
// continue; 结束当前迭代
```

## 1.5 如何运用Arrays（数组）和Vectors（向量）

```cpp
const int seq_size = 18;
int pell_seq[seq_size];

#include <vector>
vector<int> pell_seq( seq_size );

pell_seq[0] = 1;
pell_seq[1] = 2;

for (int ix = 2; ix < seq_size; ++ix)
    pell_seq[ix] = pell_seq[ix - 2] + 2 * pell_seq[ix - 1];

for (init-statement; condition; expression)
    statement
// init-statement会在循环开始执行之前被执行一次
// condition会在每次循环迭代之前被计算出来
// expression会在循环每次迭代结束之后被计算，更改init-statement中被初始化的对象和condition中被检验的对象

int ix = 0;
// ...

for ( ; ix < seq_size; ++ix)
    // ...

int elem_seq[seq_size] = {
    1, 2, 3, 
    3, 4, 7, 
    2, 5, 12,
    3, 6, 10, 
    4, 9, 16, 
    5, 12, 22
};

int elem_seq[] = {
    1, 2, 3, 3, 4, 7, 2, 5, 12, 
    3, 6, 10, 4, 9, 16, 5, 12, 22
};

int elem_vals[seq_size] = {
    1, 2, 3, 3, 4, 7, 2, 5, 12, 
    3, 6, 10, 4, 9, 16, 5, 12, 22
};
vector<int> elem_seq(elem_vals, elem_vals + seq_size);

for (int ix = 0; ix < elem_seq.size(); ++ix)
    cout << pell_seq[ix] << ' ';
```

## 1.6 指针带来弹性

```cpp
int ival = 2014;
int *pi;

int *pi = &ival; // &取地址

if (*pi != 1024) // *取得位于该指针所指内存地址上的对象
    *pi = 1024;

// pi; pi所含有的内存地址
// *pi; ival的值

// null指针，其内含地址为0，不指向任何对象
int *pi = 0;
double *pd = 0;
string *ps = 0;

// 对null指针进行提领会出错，需要检验
if (pi && *pi != 1024)
    *pi = 1024
if (pi && ...)
if (!pi)


vector<int> fibonacci, lucas, pell, triangular, square, pentagonal;
vector<int> *pv = 0;

pv = & fibonacci;
// ...
pc = &lucas;

const int seq_cnt = 6;
vector<int> *seq_addrs[seq_cnt] = {
    &fibonacci, &lucas, &pell,
    &triangular, &square, &pentagonal
};

vector<int> *current_vec = 0;
// ...
for (int ix = 0; ix < seq_cnt; ++ix)
{
    current_vec = seq_addrs[ix];
}

#include <cstdlib>
srand(seq_cnt); // srand的参数是随机数产生器种子，限制rand()最大值
seq_index = rand() % seq_cnt; // rand()返回一个介于0和int所能表示最大整数间的一个整数
current_vec = seq_addrs[seq_index];

// class object指针
if (!fibonacci.empty() && (fibonacci[1] == 1)) // dot member selection operator
if (pv && !pv->empty() && ((*pv)[1] == 1)) // arrow member selection operator
```

## 1.7 文件的读写

```cpp
#include <fstream>

// Write
ofstream outfile("seq_data.txt"); // 以输出模式开启seq_data.txt
ofstream outfile("seq_data.txt", ios_base::app) // 以append mode开启seq_data.txt

if (!outfile) // 检验文件是否开启成功
    cerr << "Oops! Unable to save session data!\n";
else
    outfile << usr_name << ' '
            << num_tries << ' '
            << num_right << endl;

// Read
ifstream infile("seq_data.txt");

int num_tries = 0;
int num_cor = 0;

if (!infile)
{
    // 文件无法开启
}
else
{
    string name;
    int nt;
    int nc;

    while (infile >> name)
    {
        infile >> nt >> nc;
        if (name == usr_name)
        {
            cout << "Welcome back, " << usr_name
                 << "\nYour current score is " << nc
                 << " out of " << nt << "\nGood Luck!\n";
            
            num_tries = nt;
            num_cor = nc;
        }
    }
}

// Write and read same file
fstream iofile("seq_data.txt", ios_base::in|ios_base::app)

if (!iofile)
    // 文件无法开启
else
{
    iofile.seekg(0); // 开始读取之前将文件重新定位至起始位置
    // ...
}
```
