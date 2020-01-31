# Introduction to C++ Multithread

Ref: https://www.jyt0532.com/2016/12/23/c++-multi-thread-p1/

## Environment 

C++11 compiler

```bash
g++ -std=c++11 filename.cpp
```

## New a thread

```cpp
#include <thread>
#include <iostream>

using namespace std;

void thread_function() 
{
    cout << "I am a new thread" << endl;
}

int main()
{
    thread t1(thread_function);
    t1.join();
    return 0;
}
```

Master thread会在`t1.join()`那行等t1跑完才会继续往下跑。可以call detach function使得master可以放生它自己结束程序。

```cpp
#include <thread>
#include <iostream>
#include <unistd.h>

using namespace std;

void thread_function()
{
    cout << "I am a new thread" << endl;
    usleep(2000);
    cout << "This message is unlikely to show" << endl;
}

int main()
{
    thread t1(thread_function);
    t1.detach();

    cout << "I am master thread and I am about to finish" << endl;

    return 0;
}
```

## Pass arguments to thread

```cpp
#include <thread>
#include <iostream>
#include <string>
#include <vector>

using namespace std;

void func(int i, string s)
{
    cout << i << ", " << s << endl;
}

int main()
{
    vector<thread> threads;
    for (int i = 0; i < 10; i++)
    {
        threads.push_back(thread(func, i, "test"));
    }
    for (int i = 0; i < threads.size(); i++)
    {
        threads[i].join();
    }
    return 0;
}
```

给thread的第一个参数就是function，之后就是function的arguments。每个thread有自己的id，可以call `this_thread::get_id()`取得自己id，或是call `get_id()`来得到相对应thread的id。

## lock

What is the difference between a process and a thread?

> Both processes and threads are independent sequences of execution. The typical difference is that threads (of the same process) run in a shared memory space, while processes run in separate memory spaces.

Be careful with shared data when you new many threads.

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

void func(int &s)
{
    s++;
}

int main()
{
    vector<thread> threads;
    int sum = 0;
    for (int i = 0; i < 1000; i++)
    {
        threads.push_back(thread(func, ref(sum)));
    }
    for (int i = 0; i < threads.size(); i++)
    {
        threads[i].join();
    }
    cout << sum << endl;
    return 0;
}
```

上面的程序每次执行的结果都会不一样，偶尔刚好1000偶尔小于1000。处理multi-thread的问题一定要处理好shared data的access。如果多个thread同时改变同样的data就会有race condition的问题。这时候就需要mutex来manage控制权。

```cpp
#include <thread>
#include <iostream>
#include <string>
#include <vector>
#include <mutex>

using namespace std;
struct Sum 
{
    int sum = 0;
    mutex mu;
    void incre() 
    {
        mu.lock();
        sum++;
        mu.unlock();
    }
}

void func(Sum &s)
{
    s.incre();
}

int main()
{
    vector<thread> threads;
    Sum s;
    for (int i = 0; i < 10000; i++)
    {
        threads.push_back(thread(func, ref(s)));
    }
    for (int i = 0; i < threads.size(); i++)
    {
        threads[i].join();
    }
    cout << s.sum << endl;
}
```

Best practice是把sum这个variable跟mutex包在一起，避免将mutex用作global variable。这样一个thread要改sum variable之前先要去lock，如果有人在用，我就一直等到锁被release。如果没有人在用锁，我就拿这个锁，改动数据之后再把锁release。看似完美，在shared data的前后加lock。

## problem with lock/unlock

如果将sum++改成一个function的话，并且mutex之间的operation throw了exception。

```cpp
void incre()
{
    mu.lock();
    funA(); // if funA throw unexpected exception, the lock will never release
    mu.unlock();
}
```

那throw exception的那个指令之后的指令都不会被执行。它会直接看它是不是在try block里，是的话就会找到对应的catch block执行catch block内的指令；找不到的话就会直接return，再看它的parent function有没有预期到这个exception，没有的话就一路return出去，直到找到对应的exception handler再去执行对应的catch。这就引出了[stack unwinding](https://stackoverflow.com/questions/2331316/what-is-stack-unwinding)。

如果throw exception那么锁永远不会release，其它thread就等你等到死。可以用try/catch包住可能throw exception的所有地方。

```cpp
void incre()
{
    mu.lock()
    try
    {
        funA();
    }
    catch (std::exception &cException)
    {
        mu.unlock();
        throw cException;
    }
    mu.unlock();
}
```

但是对于大型程序这样的写法很难maintain。那如何让一个function在结束之前保证release lock呢？

## RAII

Resource acquisition is initialization

> In RAII, holding a resource is tied to object lifetime: resource allocation is done during object creation by the constructor, while resource deallocation is done during object destruction by the destructor. Thus the resource is guaranteed to be held between when initialization finished and finalization starts and to be held only when the object is alive.

那C++怎么implement这件事情呢？

```cpp
void func(int x)
{
    char* pleak = new char[1024]; // might be lost => memory leak
    std::string s("hello world"); // will be properly destructed
    
    if (x) throw std::runtime_error("boom");

    delete [] pleak; // will only get here if x == 0. if x != 0, throw exception
}
```

C++的compiler会在一个scope要结束的时候（about to go out of scope or exception thrown）去执行所有变量的destructor in reverse order。相反顺序是因为后面声明的变量可能用到之前声明的变量当做constructor的参数，先destruct后面的比较安全。

所以在multi-thread的世界里C++为了支持RAII发明了以下两个工具：

## lock_guard, unique_lock

```cpp
void incre()
{
    lock_guard<std::mutex> lockGuard(mu);
    funA();
}
```

要initialize lock_guard只需要给一个mutex variable，剩下的他帮你搞定。lock_guard在constructor里lock你给他的mutex，在destructor里release同一个mutex，保证destructor会被call到。

unique_lock用途更广，除了以上之外unique_lock还可以当做function的return type，更重要的是unique_lock支持各种不同的lock。

1. Deferred lock：先不要acquire，等晚点再acquire
2. Time-constrained lock：试着要lock，但过了一段时间都要不到就放弃
3. Recursive lock：如果一个function会recursively call自己，如果你用的是一般的mutex，那就会deadlock，因为你的parent正在占用同样的锁。但如果是recursive mutex，那就可以同一个thread一直acquire同样的lock，其它thread必须等到这个thread release，所有的recursive lock之后才可以acquire
4. Condition variable都需要搭配unique_lock使用

## condition variable是什么

 用condition variable我的理解是把它想成一个thread queue。一个thread要做事之前呢，看看有没有符合可以做事的condition，不符合的话就乖乖push进queue里面睡觉，等人家叫你醒来。如果有人叫你醒来，你就确认一下有没有符合可以做事的condition，符合的话就可以做事，不符合就继续进queue睡觉。

### producer/consumer

```cpp
#include <thread>
#include <iostream>
#include <vector>
#include <mutex>
#include <deque>

using namespace std;

struct QueueBuffer
{
    deque<int> deq;
    int capacity;
    mutex lock;
    condition_variable not_full;
    condition_variable not_empty;
    QueueBuffer(int capacity) : capacity(capacity){}
    void deposit(int data)
    {
        unique_lock<mutex> lk(lock);
        while(deq.size() == capacity)
        {
            not_full.wait(lk);
        }
        deq.push_back(data);
        lk.unlock();
        not_empty.notify_one();
    }
    int fetch()
    {
        unique_lock<mutex> lk(lock);
        while(deq.size() == 0)
        {
            not_empty.wait(lk);
        }
        int ret = deq.front();
        deq.pop_front();
        lk.unlock();
        not_full.notify_one();
        return ret;
    }
}

void consumer(int id, QueueBuffer& buffer)
{
    for (int i = 0; i < 20; ++i)
    {
        int value = buffer.fetch();
        cout << "Consumer " << id << " fetched " << value << endl;
        this_thread::sleep_for(chrono::milliseconds(200));
    }
}

void producer(int id, QueueBuffer& buffer)
{
    for (int i = 0; i < 30; ++i)
    {
        buffer.deposit(i);
        cout << "Produced " << id << " produced " << i << endl;
        this_thread::sleep_for(chrono::milliseconds(100));
    }
}

int main() 
{
    QueueBuffer buffer(4);

    thread c1(consumer, 0, ref(buffer));
    thread c2(consumer, 1, ref(buffer));
    thread c3(consumer, 2, ref(buffer));

    thread p1(producer, 0, ref(buffer));
    thread p2(producer, 1, ref(buffer));

    c1.join();
    c2.join();
    c3.join();
    p1.join();
    p2.join();

    return 0;
}
```

各个击破。先high level看main，两个producer三个consumer共用一个pool，这个pool最多可以放4个东西，producer制造东西，丢进pool consumer从pool拿东西。

再来看consumer跟producer function，一个call pool的deposit，一个call pool的fetch。

再来看怎么implement这个pool。

## producer/consumer Deep dive

QueueBuffer里面，除了基本的deque放东西跟capacity以外，需要一个所有thread共用的mutex，跟两个condition variable (CV)。这两个CV就是thread要被丢进去谁教的地方，not_full放producer thread，not_empty放consumer thread。

来看deposit，一个producer thread先拿到lock之后，**看一下现在的condition，确认一下该做事还是该睡觉**，while里面的就是等待条件，在这里，就是如果pool的size满了就睡觉，not_full.wait(lk)解读成把自己push进not_full这个thread queue里面。

**在CV里睡觉之前release手中的lock**

**在CV里睡觉之前release手中的lock**

**在CV里睡觉之前release手中的lock**

这就是CV最神奇的地方，也是这篇文章最重要的一个概念。

而且它并不是一直在while里面确认condition，他是一直在CV里面睡觉知道有人叫它起床，起床之后做几件事：1. acquire lock（别怀疑，就是楼上那个unique_lock，我们睡觉前有release它） 2. 确认while condition符不符合 3. 是个可以做事的condition的话，跳出while loop做事 4. condition还是不对的话，一样release刚刚拿的lock，进CV睡觉。

好如果现在可以做事的话，我就可以把我的data push进pool，然后release lock（我们在改动shared data的时候手上一定要有锁），最后叫consumer thread queue(not_empty)的其中一个thread起床（notify），因为你不知道有没有consumer thread在等你叫它起床，如果没有人在not_empty里面也没关系，你再放东西进去pool后就要负责叫not_empty这个CV里面的thread起床。

fetch里面也大同小异，拿锁，确认条件，有东西可以拿的话就拿，拿完后release lock，叫producer thread queue的人起床。

### Read/Write Lock

https://www.jyt0532.com/2017/01/02/c++-multi-thread-p4-2/