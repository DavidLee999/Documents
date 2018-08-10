<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Trees](#trees)
  - [Preliminaries](#preliminaries)
    - [Implmentation of Trees](#implmentation-of-trees)
    - [Tree Traversals](#tree-traversals)
  - [Binary Trees](#binary-trees)
    - [Implementation](#implementation)
  - [Binary Search Trees](#binary-search-trees)
    - [Consturctor](#consturctor)
    - [`contains` function](#contains-function)
    - [`insert` function](#insert-function)
    - [`findMin` and `findMax`](#findmin-and-findmax)
    - [`remove` function](#remove-function)
    - [Desturctor](#desturctor)
    - [Remark](#remark)
    - [Average-Case Analysis](#average-case-analysis)
  - [AVL Trees](#avl-trees)
    - [Single Rotation](#single-rotation)
    - [Double Rotation](#double-rotation)
    - [Summary](#summary)
    - [Implementation](#implementation-1)
      - [Node](#node)
      - [`insert` method](#insert-method)
      - [`balance` method](#balance-method)
      - [`remove` method](#remove-method)
  - [Splay Trees](#splay-trees)
    - [A (not working) simple idea](#a-not-working-simple-idea)
    - [Splaying](#splaying)
    - [Deletion:](#deletion)
  - [B-Trees](#b-trees)
    - [Operations:](#operations)
      - [Insertion:](#insertion)
      - [Remove：](#remove)
  - [树的遍历](#%E6%A0%91%E7%9A%84%E9%81%8D%E5%8E%86)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Trees

The average running time of most operations is O(logN).

The *binary search tree* is the basis for the implementation of two library collections classes, `set` and `map`.

## Preliminaries

**Definition** (recursive): A tree is **a collection of nodes**. The collection can be empty; otherwise, a tree consists of a distinguished node, *r*, called the **root**, and zero or more nonempty (sub)trees *T~1~ , T~2~ , ..., T~k~*, each of whose roots are connected by a **directed edge** from *r*.

The root of each subtree is said to be a **child** of *r*, and *r* is the **parent** of each subtree root.

From the recursive deﬁnition, we ﬁnd that a tree is a collection of N nodes, one of which is the root, and N − 1 edges.

Nodes with no children are known as **leaves**; Nodes with the same parent are **siblings**;

A **path** from node n~1~ to n~k~ is deﬁned as **a sequence of nodes** n~1~ , n~2~ , ..., n~k~ such that n~i~ is the parent of n~i+1~ for 1 ≤ i < k. The **length** of this path is **the number of edges** on the path, namely, k − 1. There is a path of length zero from every node to itself.

For any node n~i~ , the **depth** of n~i~ is **the length of the unique path from the root to n~i~** . The root is at depth 0. The **height** of n~i~ is **the length of the longest path** from n~i~ to a leaf. All leaves are at height 0. The **height of a tree** is equal to **the height of the root**.

The **depth of a tree** is equal to **the depth of the deepest leaf** and is **equal to the height of the tree**.

If there is a path from n~1~ to n~2~, then n~1~ is an **ancestor** of n~2~ and n~2~ is a **descendant** of n~1~ . If n~1~ != n~2~ , then n~1~ is a **proper ancestor** of n~2~ and n~2~ is a** proper descendant** of n~1~.

### Implmentation of Trees

```c++
struct TreeNode
{
  Object element;
  TreeNode *firstChild;
  TreeNode *nextSibling; 
};
```

![屏幕快照 2018-01-06 22.26.08](屏幕快照 2018-01-06 22.26.08.png)

### Tree Traversals

- **Preorder traversal**: In a preorder traversal, work at a node is performed **before (pre) its children** are processed.
- **Postorder traversal**: In a postorder traversal, the work at a node is performed **after (post) its children** are evaluated.
- **Inorder traversal**: In a inorder traversal, the work at a node is performed **after its left-children and before its right-child** are evaluated.

## Binary Trees

**DEFINITION**:  A binary tree is a tree in which no node can have more than two children.

**Properities**:

- The depth of an average binary tree is considerably smaller than N (largest N - 1). The **average depth** is $O(\sqrt{N})$. The**average depth of *binary search tree*** is $O(logN)$.
- 二叉树第 i 层上的结点数目最多为2^i-1^.
- 深度为 k 的二叉树至多有2^k^-1个结点。
- 一个节点含有的子树的个数称为该节点的度。度为i 的节点的个数设为n~i~。一棵二叉树的总节点为n~0~ + n~1~ + n~2~ = n~1~ + 2n~2~ + 1.
- 由上一条可知：n~0~=n~2~ + 1.
- 一棵树的总结点数为N，其总度数为N - 1.

### Implementation

```c++
struct BinaryNode
{
  Object element; // The data in the node
  BinaryNode *left; // Left child
  BinaryNode *right; // Right child
};
```

## Binary Search Trees

**DEFINITION**: The property that makes a binary tree into a binary search tree is that for every node, X, in the tree, the values of all the items in its left subtree are smaller than the item in X, and the values of all the items in its right subtree are larger than the item in X.

**Note**: Because of the **recursive deﬁnition** of trees, it is common to write these routines **recursively**.

An implementation of binary search tree can be found in *[BinarySearchTree](https://github.com/DavidLee999/Data_Structures_and_Algorithm_Analysis_Cpp/blob/master/Ch4/BinarySearchTree.cpp)*. In this implementation:

- the dtat member is merely **a pointer to the root node**. 
- The public member functions use the general technique of calling **private recursive functions**.
- The private member function uses **reference to a pointer** (`*&`) as the parameter (形参) to ensure that the function can change the pointer to the root.

### Consturctor

The copy constructor can be done by simply calling the `clone` function which do all the dirty work.

```c++
BinaryNode * clone( BinaryNode *t ) const
{
  if( t == nullptr )
    return nullptr;
  else
    return new BinaryNode{ t->element, clone( t->left ), clone( t->right ) };
}
```

### `contains` function

Make use the recursive definition of the tree, we **make a recursive call** on a subtree of T, either left or right, depending on the relationship of item X and the item of T.

```c++
bool contains( const Comparable & x, BinaryNode *t ) const 
{ 
  if( t == nullptr ) 
    return false; 
  else if( x < t->element ) 
    return contains( x, t->left ); 
  else if( t->element < x ) 
    return contains( x, t->right ); 
  else 
    return true; // Match
}
```

### `insert` function

To insert X into tree T, proceed down the tree as you would with a`contains`. If X is found, do nothing. Otherwise, insert X at the last spot on the path traversed.

```c++
if( t == nullptr )
  t = new BinaryNode{ x, nullptr, nullptr };
```

**Duplicates** can be handled by keeping all of the structures that have the same key in an **auxiliary data structure**, such as a `list` or another search tree.

In the recursive routine, **the only time that `t` changes** is when a **new leaf** is created. When this happens, it means that the recursive routine has been called from some other node, `p`, which is to be the leaf’s parent.

### `findMin` and `findMax`

To perform a `findMin`, start at the root and **go left** as long as there is a left child. The stopping point is the smallest element. 

The `findMax` routine is the same, except that branching is to the **right child**.

### `remove` function

Three cases:

- If the node is a **leaf**, it can be **deleted immediately**;
- If the node has **one child**, the node can be deleted after its parent **adjusts a link to bypass the node**;
- For a node with **two children**, the general strategy is to **replace the data of this node with the smallest data of the right subtree** (which is easily found) and recursively **delete that node** (which is now empty).

```c++
void remove( const Comparable & x, BinaryNode * & t )
{ 
  if( t == nullptr ) 
    return; // Item not found; do nothing 
  if( x < t->element ) 
    remove( x, t->left ); 
  else if( t->element < x ) 
    remove( x, t->right ); 
  else if( t->left != nullptr && t->right != nullptr ) // Two children
  {
    t->element = findMin( t->right )->element; 
    remove( t->element, t->right ); 
  }
  else 
  {
    BinaryNode *oldNode = t; 
    t = ( t->left != nullptr ) ? t->left : t->right; 
    delete oldNode;
  }
}
```

### Desturctor

The destructor calls `makeEmpty`. The public `makeEmpty` simply calls the private recursive version. After **recursively processing** `t`’s children, a call to delete is made for `t`. Thus all nodes are recursively reclaimed.

### Remark

综上，对于二叉搜索树的实现，只需要牢牢把握住其定义的递归性。利用定义上的递归性来用递归函数来完成其实现。

### Average-Case Analysis

The running time of all operations (except `makeEmpty` and copying) is O(d), where d is the depth of the node containing the accessed item. The **average depth** over all nodes in a tree is O(log N) on the **assumption that all insertion sequenes are equally likely**.

**internal path length**: The sum of the depths of all nodes in a tree.

The average running time of all the operations is O(log N), but this is not entirely true. The reason for this is that **because of deletions**, it is not clear that all binary search trees are equally likely. In particular, the deletion algorithm described above favors **making the left subtrees deeper than the right**.

**Problem**:

- If we alternate insertions and deletions $\Theta(N^2)$ times, then the trees will have an expected depth of $\Theta(\sqrt{N})$.
- If the input comes into a tree **presorted**, then a series of inserts will take quadratic time and the tree will consist only of nodes with no left children.

**Solutions**:

1. One solution to the problem is to insist on an extra structural condition called **balance**: No node is allowed to get too deep.
2. A second method is to allow the tree to be arbitrarily deep, but after every operation, a restructuring rule is applied that tends to make future operations efﬁcient which is called **self-adjusting**.

## AVL Trees

An *AVL tree* is a binary search tree with a **balance condition**. The balance condition ensures that the depth of the tree is O(log N).

**DEFINITION**: An AVL tree is identical to a binary search tree, except that for every node in the tree, **the height of the left and right subtrees can differ by at most 1**. (The height of an empty tree is deﬁned to be −1.) 

Height information is kept for **each node** (in the node structure).

**PROPERTY**: The minimum number of nodes, S(h), in an AVL tree of height *h* is given by S(h) = S(h − 1) + S(h − 2) + 1. For h = 0, S(h) = 1. For h = 1, S(h) = 2.

Thus, all the tree operations can be performed in O(log N) time, except possibly insertion and deletion. When we do an **insertion**, we need to **1) update all the balancing information** for the nodes on the path back to the root, but the insertion could **2) violate the balance**. If this is the case, then the property has to be **restored before the insertion** step is considered over and the operation is called **rotate**.

After an insertion, only **nodes that are on the path from the insertion point to the root** might have their balance altered because only those nodes have their subtrees altered. 

Let us call the node tht must be balanced $$\alpha$$. A height imbalance requires that $\alpha$’s **two subtrees’ heights differ by two**, it is easy to see that a violation might occur in **four cases**:

1. $\alpha \to left \to h > \alpha \to right \to h$
   1. An insertion into the left subtree of the left child of $\alpha$. ($\alpha \to left \to left \to h  \ge \alpha \to  left \to right \to h$);
   2. An insertion into the right subtree of the left child of $\alpha$. ($\alpha \to left \to left \to h < \alpha \to  left \to right \to h$);
2. $\alpha \to right \to h > \alpha \to left \to h$
   1. An insertion into the left subtree of the right child of $\alpha$. ($\alpha \to right \to right \to h < \alpha \to right \to left \to h$)
   2. An insertion into the right subtree of the right child of $\alpha$. ($\alpha \to right \to right \to h  \ge\alpha \to  right \to left \to h$)

Case 1.1 and 2.2 are mirror image symmetries w.r.t. $\alpha$, as are case 1.2 and 2.1. The first situation, in which the insertion occurs on the "**outside**", is fixed by a **single rotation** of the tree. The second case, in which the insertion occurs on the "**inside**", is handle by the **double rotation**.

### Single Rotation

Following figure shows the *single rotation* that fixes case 1.1.

![屏幕快照 2017-12-20 17.02.28](屏幕快照 2017-12-20 17.02.28.png)

**OBJECT**: To ideally rebalance the tree, we would like to move X up a level and Z down a level.

The **result** is that k~1~ will be the new root and k~2~ becomes the right child of k~1~ in the new tree. X and Z remain as the left child of k~1~ and right child of k~2~ , respectively. Subtree Y, which holds **items that are between k~1~ and k~2~** in the original tree, can be placed as k~2~ ’s left child in the new tree and satisfy all the ordering requirements.

**PROCEDURE**: This result only requires a few **pointer changes**. k~2~ and k~1~ not only satisfy the AVL requirements, but they also have subtrees that are exactly **the same height**. Furthermore, the new height of the entire subtree is **exactly the same** as the height of the original subtree **prior** to the insertion that caused X to grow. Only the heights of k~1~ and k~2~ need to be changed. No further updating of heights on the path to the root is needed, and consequently no further rotations are needed.

Case 2.2 is a symmetric case as shown in follows.

![屏幕快照 2017-12-20 17.30.25](屏幕快照 2017-12-20 17.30.25.png)

**NOTE**: Besides the local change caused by the rotation, it should be remembered that the rest of the tree has to be informed of this change.

### Double Rotation

The algorithm described above cannot solve the case 1.2 and 2.1. The following figure shows the *left-right double rotation* to fix case 1.2. For case 1.2, the tree may be viewed as four subtrees connected by three nodes. As the diagram suggests, exactly one of tree B or C is two levels deeper than D (unless all are empty), but we cannot be sure which one.

![屏幕快照 2017-12-20 17.40.32](屏幕快照 2017-12-20 17.40.32.png)

We need to place k~2~ as the new root. This forces k~1~ to be k~2~ ’s left child and k~3~ to be its right child, and it also completely determines the resulting locations of the four subtrees.

Following figure shows the *right-left double rotation* to fix case 2.1.

![屏幕快照 2017-12-20 17.59.15](屏幕快照 2017-12-20 17.59.15.png)

### Summary

平衡AVL树，我们可以利用一下步骤：

1. Which case -> which strategy;
2. 确定k~1~, k~2~, k~3~;
3. 确定A, B, C, D;
4. Rotation。

### Implementation

Description: To insert a new node with item X into an AVL tree T, we recursively insert X into the appropriate subtree of T (let us call this T~LR~ ). 

- If the height of T~LR~ does not change, then we are done.
- If a height imbalance appears in T, we do the appropriate single or double rotation depending on X and the items in T and T~LR~ , updating the heights and making the connection from the rest of the tree above.

A full relization of AVL tree is in [AVLTree](https://github.com/DavidLee999/Data_Structures_and_Algorithm_Analysis_Cpp/blob/master/Ch4/AVLTree.cpp).

#### Node

```C++
struct AvlNode 
{
  Comparable element; 
  AvlNode *left; 
  AvlNode *right; 
  int height;
};
```

#### `insert` method

```c++
void insert( const Comparable & x, AvlNode * & t ) 
{
  if( t == nullptr )
    t = new AvlNode{ x, nullptr, nullptr };
  else if( x < t->element )
    insert( x, t->left );
  else if( t->element < x )
    insert( x, t->right );
  
  balance( t );
}
```

#### `balance` method

```c++
void balance( AvlNode * & t ) 
{
  if( t == nullptr ) 
    return; 
  if( height( t->left ) - height( t->right ) > ALLOWED_IMBALANCE ) // case 1 
    if( height( t->left->left ) >= height( t->left->right ) ) // case 1.1
      rotateWithLeftChild( t ); 
  	else // case 1.2
      doubleWithLeftChild( t ); 
  else if( height( t->right ) - height( t->left ) > ALLOWED_IMBALANCE )  // case 2
    if( height( t->right->right ) >= height( t->right->left ) ) // case 2.2
      rotateWithRightChild( t ); 
  	else // case 2.1
      doubleWithRightChild( t ); 
  
  t->height = max( height( t->left ), height( t->right ) ) + 1;
}
```

```C++
void rotateWithLeftChild( AvlNode * & k2 ) 
{
  AvlNode *k1 = k2->left;
  k2->left = k1->right;
  k1->right = k2;
  k2->height = max( height( k2->left ), height( k2->right ) ) + 1;
  k1->height = max( height( k1->left ), k2->height ) + 1;
  k2 = k1;
}
```

```C++
void doubleWithLeftChild( AvlNode * & k3 )
{
  rotateWithRightChild( k3->left );
  rotateWithLeftChild( k3 ); 
}
```

#### `remove` method

```C++
void remove( const Comparable & x, AvlNode * & t )
{
  if( t == nullptr )
    return; // Item not found; do nothing
  
  if( x < t->element ) 
    remove( x, t->left ); 
  else if( t->element < x )
    remove( x, t->right ); 
  else if( t->left != nullptr && t->right != nullptr ) // two children
  {
    t->element = findMin( t->right )->element;
    remove( t->element, t->right );
  } 
  else 
  {
    AvlNode *oldNode = t;
    t = ( t->left != nullptr ) ? t->left : t->right;
    delete oldNode; 
  }
  
  balance( t );
}
```

## Splay Trees

**Property1**: any *M* **consecutive** tree operations starting from an empty tree take at most *O(M logN)* time. So there are no bad input sequences.

( **DEFINITION**: when a sequence of *M* operations has **total worst-case running time** of *O(M f(N))*, we say that the **amortized running time** is *O(f(N))*. Thus, a splay tree has an *O(log N)* amortized cost per operation.)

**Basic idea**: after a node is accessed, it is **pushed to the root** by a series of AVL tree rotations (because if any particular operation is allowed to have an *O(N)* worst-case time bound, and we still want an *O(log N)* amortized time bound, then whenever a node is accessed, it must be moved) and if the node is unduly deep, we want this restructuring to have the side effect of **balancing the tree**.

### A (not working) simple idea

One way of performing the restructuring is to perform **single rotations**, bottom up. This means that we **rotate every node on the access path with its parent**.

Assuming we accessed *k~1~* node through the dashed line in following figure.

![屏幕快照 2018-01-03 19.44.44](屏幕快照 2018-01-03 19.44.44.png)

Then we firstly performed a single rotation between *k~1~* and *k~2~* as shown below and then between *k~1~* and *k~3~* and so on. 

![屏幕快照 2018-01-03 19.46.53](屏幕快照 2018-01-03 19.46.53.png)

And finally we have

![屏幕快照 2018-01-03 19.47.18](屏幕快照 2018-01-03 19.47.18.png)

**Drawbacks**: These rotations have the effect of pushing *k~1~* all the way to the root. However, it has pushed another node (*k~3~*) almost as deep as *k~1~* used to be. An access on that node will then push another node deep, and so on. **So this idea is not quite good enough**.

### Splaying

The splaying strategy is similar to the rotation idea above, except that we are more selective about how rotations are performed.

####Procedure:

Let *X* be a (non-root) node on the access path at which we are rotating. 

If the parent of *X* is the root of the tree, we merely rotate *X* and the root. This is the last rotation along the access path. 

Otherwise, X has both a parent (*P*) and a grandparent (*G*), and there are **two cases** (plus symmetries) to consider. 

1. **The zig-zag case**（之字型）: Here *X* is a right child and *P* is a left child (or vice versa). We perform a **double rotation,** exactly like an AVL double rotation.

   ![屏幕快照 2018-01-03 20.05.22](屏幕快照 2018-01-03 20.05.22.png)

2. **The zig-zig case**（一字型）: X and P are both left children (or, in the symmetric case, both right children). In that case, we transform the tree on the left to the tree on the right.

   ![屏幕快照 2018-01-03 20.07.23](屏幕快照 2018-01-03 20.07.23.png)

**Property**: 

- Splaying not only **moves the accessed node to the root** but also roughly **halves the depth** of most nodes on the access path (some shallow nodes are pushed down at most two levels).
- When access paths are long, thus leading to a longer-than-normal search time, the rotations tend to be good for future operations. When accesses are cheap, the rotations are not as good and can be bad.

### Deletion:

We perform deletion by accessing the node to be deleted. This puts the node at the root. If it is deleted, we get **two subtrees** *T~L~* and *T~R~* (left and right). If we ﬁnd the **largest element** in *T~L~* (which is easy), then this element is **rotated to the root** of *T~L~* , and *T~L~* will now have **a root with no right child**. We can ﬁnish the deletion by making *T~R~* the right child.

## B-Trees

Assume we have more data than can ﬁt in main memory and must have the data structure reside on disk. In this cases, it is **the number of disk accesses** that will dominate the running time.

We want to **reduce the number of disk accesses** to a very small constant, such as three or four. It should probably be clear that **a binary search tree will not work**. We cannot go below *logN* using a binary search tree. The **solution** is intuitively simple: If we have **more branching**, we have **less height**.

An *M-ary search tree* allows M-way branching. Whereas a complete binary tree has height that is roughly *log~2~N*, a complete M-ary tree has height that is roughly *log~M~N*.

In an M-ary search tree, we need ***M − 1* keys** to decide which branch to take. To make this scheme efﬁcient in the worst case, we need to ensure that the M-ary search tree is balanced in some way.

###Definition:

A B-tree of **order *M*** is an M-ary tree with the following **properties**: 

1. The data items are stored at leaves.
2. The non-leaf nodes store up to *M − 1* keys to guide the searching; key *i* represents the **smallest key** in subtree *i + 1*.
3. The **root** is either a leaf or has between **2 and *M* children**.
4. All **non-leaf nodes** (except the root) have between ***M/2* and *M* children**.
5. All leaves are at the same depth and have between ***L/2* and *L* data items**, for some L (the determination of L is described shortly).

![屏幕快照 2018-01-03 20.41.28](屏幕快照 2018-01-03 20.41.28.png)

Above is a B-tree of order 5 with L = 5 as well. Requiring nodes to be half full guarantees that the B-tree does not degenerate into a simple binary tree. Each node represents a disk block, so we choose *M* and *L* on the basis of **the size of the items** that are being stored.

( **Example**: suppose one block holds 8,192 bytes. In the Florida example, each key uses 32 bytes. In a B-tree of order *M*, we would have *M−1* keys, for a total of *32M − 32* bytes, plus *M* branches. Since each branch is essentially a number of another disk block, we can assume that a branch is 4 bytes. Thus the branches use *4M* bytes. The total memory requirement for a non-leaf node is thus *36M−32*. The largest value of *M* for which this is no more than 8,192 is 228. Thus we would choose *M = 228*. Since each data record is 256 bytes, we would be able to ﬁt 32 records in a block. Thus we would choose *L = 32*. We are guaranteed that each leaf has between 16 and 32 data records and that each internal node (except the root) branches in at least 114 ways. Since there are 10,000,000 (N) records, there are, at most, (N / 16 = ) 625,000 leaves. Consequently, in the worst case, leaves would be on level (*log~114~ N* = ) 4. In more concrete terms, the worst-case number of accesses is given by approximately *log~M/2~ N*, give or take 1. (For example, the root and the next level could be cached in main memory, so that over the long run, disk accesses would be needed only for level 3 and deeper.))

### Operations:

#### Insertion:

对于普通插入，例如往上图到B-tree插入57，直接将之插入到对应的叶子结点中，需要一次写操作。

![屏幕快照 2018-01-06 20.49.22](屏幕快照 2018-01-06 20.49.22.png)

假如我们现在又要插入数据55，此时对应的叶子结点已满，此时，我们需要**分裂**该节点，每个节点则包含 L/2 或 (L+1) / 2个节点，满足之前所述的要求。此时需要两次写操作和一次对其父节点进行更改的写操作，共有3次磁盘读写。

![屏幕快照 2018-01-06 20.50.58](屏幕快照 2018-01-06 20.50.58.png)

尽管分裂叶子节点十分费时，但每次分裂都会带了 L / 2 的额外空间。

假设此时我们要继续插入数据40. 可见此时对应叶子结点的父节点也已经满了。此时，我们需要对该父节点进行分裂，并对父节点中的键和根结点中的键进行更新。

1. 包含数据35的叶子结点需要分裂成连个节点，将其中值38选为新的键值；
2. 此时我们有键8、18、26、35、38. 中值26被选为新键值上传到根结点，以保证新产生的两个结点有足够数目（2 or 3）的孩子；
3. 原结点包含有键8和18且有3个孩子（不变）。新产生的结点则包含有键35和38，一个原叶子结点和两个分裂产生的新节点。

![屏幕快照 2018-01-06 21.11.58](屏幕快照 2018-01-06 21.11.58.png)

我们一共需要更新3个节点的信息，两次向磁盘写入数据。因此一共有5次磁盘读写操作。

此时根结点已满，假设我们联系不断插入数据使得根节点需要分裂，此时我们就有了两个根结点。此时，我们自然会创建一个新的根结点并将两个分裂得到的根结点作为其孩子。这也是为什么根节点被允许有2个孩子。而且这也是B-tree 长高的唯一途径。

**另一种**解决节点数据溢出的方法是将溢出节点的某个孩子抛出让其兄弟节点**收养**。假设我们要插入数据29。显然对应叶子结点已满，我们可以抛出数据32让下一个叶子结点收养并更改对应的键值。因此涉及两次磁盘读写。

#### Remove：

执行删除操作时，我们只需简单删除点对应数据即可。若此时该操作使得对应的叶子结点的数据数目少于要求，则该叶子结点要求收养其兄弟节点的一个数据并更改对应键值。如果其兄弟节点已到了数据数量允许的最小值，则我们合并这两个节点使之成为一个新的满节点。这就意味着其父节点会少掉一个孩子。如果这让该父节点不满足B-tree的性质，则我们让其收养兄弟节点的一个孩子并更改相关键值或将之与其兄弟节点合并。

该策略不断运用直至停止或到达根节点。若因此，根节点只剩下一个孩子，则根节点被删除且其孩子成为新的根节点。

假设我们要从上个B-tree中删除数据99. 显然此时该叶子节点只剩下两个数据。其兄弟节点也只有3个数据，所以将之与其兄弟节点合并。此时其父节点只有两个孩子，不满足要求。而父节点的兄弟节点有4个孩子，此时，要求收养其最后一个孩子。首先，将键值83上传给根节点作为其新键值。根节点的第四个孩子中最后一个键被删除，最后一个孩子被移到最后一个节点中。在叶子结点 (83, 84, 85) 和 (87, 89, 90) 中选取中间值87作为新键值。

![屏幕快照 2018-01-06 21.50.24](屏幕快照 2018-01-06 21.50.24.png)

## 树的遍历

依据根节点的处理次序，树的遍历可以分为*前序遍历*（根节点 - 左孩子 - 右孩子），*中序遍历*（左孩子 - 根节点 - 右孩子）和*后序遍历*（左孩子 - 右孩子 - 根节点）。每种遍历的时间复杂度都为 *O(N)* 且都可以使用递归的方式实现。下面展示了中序遍历的代码：

```C++
void printTree( ostream & out = cout ) const
{
  if( isEmpty( ) ) // check for empty first
    out << "Empty tree" << endl;
  else
    printTree( root, out ); 
}

void printTree( BinaryNode *t, ostream & out ) const 
{ 
  if( t != nullptr ) 
  { 
    printTree( t->left, out ); // left
    out << t->element << endl; // root
    printTree( t->right, out ); // right
  }
}
```

可以看到，其仍然是利用树的递归定义通过递归函数来实现。

此外，还有一种 **level-order traversal**. 在 level-order traversal 中，所有位于深度 *d* 的结点先被处理，然后再处理深度为 *d+1* 的节点。该遍历无法用递归实现，而需要用到队列。

> Enqueue the root;
>
> Dequeue the node;
>
> Process;
>
> Enqueue its children; 
>
> Continue with step 2.

