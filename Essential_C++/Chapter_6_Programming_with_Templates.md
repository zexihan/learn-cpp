# Chapter 6 Programming with Template

## 6.1 被参数化的型别（Parameterized Types）

```cpp
// forward declaration
template <typename valType>
class BTnode;

template <typename Type>
class BinaryTree;

template <typename valType>
class BTnode {
public:
    friend class BinarTree<calType>;
    // ...
private:
    valType _val;
    int _cnt;
    BTnode *_lchild;
    BTnode *_rchild;
}
```

```cpp
BTnode<int> bti;
BTnode<string> bts;

BinaryTree<string> st;
BinaryTree<int> it;
```

## 6.2 Class Template的定义

```cpp
template <typename elemType>
class BinaryTree {
public:
    BinaryTree();
    BinaryTree(const BinaryTree&);
    ~BinaryTree();
    BinaryTree& operator=(const BinaryTree&);

    bool empty() ( return _root == 0; )
    void clear();
private:
    BTnode<elemType> *_root;

    void copy(BTnode<elemType>*tar, BTnode<elemType>*src);
};
```

## 6.3 Template型别参数（type parameters）的处理 

skip
