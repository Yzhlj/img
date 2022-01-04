# 												JS数据结构:树

## 一、是什么

在计算机领域，树形数据结构是一类重要的非线性数据结构，可以表示数据之间一对多的关系。以树与二叉树最为常用，直观看来，树是以分支关系定义的层次结构

树的定义：树(Tree)是n(n 大于等于0)个节点的有限集合T，当n=0时称为空树，否则，称为非空树

### 树的基本术语：

节点：就是指树中的元素

子节点： 树中一个节点的 直属下级节点

父节点： 当前节点的前趋节点

祖先节点：当前节点的前趋节点(非父节点)

子孙节点：当前结点的下属节点(非直接下属节点)

叶子节点：没有子节点的节点元素

节点的层次：根节点是第一层节点，表示节点的所属的层数

深度：树的层数，表示树的深度

森林： n棵互不相交的树的集合

## 二叉树

二叉树是另一种特殊的树形结构，它的特定是每个至多有两棵树，二叉树的子节点有左右之分，其次序不能任意颠倒。二叉树的结构简单，存储效率较高，运算的算法也相对简单。并且任何树或森林与二叉树之间可以通过简单的操作规则相互转换，在树的应用中，它起着举足轻重的作用。

### 二叉树特点

二叉树具有以下特点。

- 最大度数为 2，就是说子节点数只能是 `0`、`1`、`2` 这三种情况之一
- 为有序树
- 比父节点小的放左边，小的放右边（如果相等的呢？好像是放右边）
- 即使只有一个节点，也区分左右

如下图，左侧的为二叉树，而右侧的因为头结点的子结点超过2，因此不属于二叉树：

<img src="https://cdn.jsdelivr.net/gh/xuzhangZ/newcdn/img/image-20211010161402671.png" alt="image-20211010161402671" style="zoom:50%;" />

同时，二叉树可以继续进行分类，分成了满二叉树和完全二叉树：

- 满二叉树：如果二叉树中除了叶子结点，每个结点的度都为 2

<img src="https://cdn.jsdelivr.net/gh/xuzhangZ/newcdn/img/image-202110101614321.png" alt="image-202110101614321" style="zoom:50%;" />

- 完全二叉树：如果二叉树中除去最后一层节点为满二叉树，且最后一层的结点依次从左到右分布

<img src="https://cdn.jsdelivr.net/gh/xuzhangZ/newcdn/img/image-20211010220513510.png" alt="image-20211010220513510" style="zoom:50%;" />

## 二、操作

首先要创建一个节点

```js
class Tree {
    value: number;
    left?: Tree;
    right?: Tree;
    constructor(value: number) {
        this.value = value;
        this.left = null;
        this.right = null
    }
}
```



​	创建一个树

```js
class BinaryTree {
    //根节点
    root: Tree | null;
    constructor() {
        this.root = null
    }
    //插入节点 
    //如果根节点为空，则插入到根节点。如果不是，插入到根节点下面
    insert(value: number) {
        let newNode = new Tree(value)
        if (!this.root) {
            this.root = newNode;
            return;
        }

        this.insertNode(this.root, newNode);
    }
    //实际的插入方法。这是一个递归的方法。
    insertNode(node: Tree, newNode: Tree) {
        if (node.value > newNode.value) { // 比父节点小，插入在左边
            if (node.left) {
                this.insertNode(node.left, newNode); // 如果非空，则要再一次判断，插入到子节点下面
                return;
            }

            node.left = newNode;
            return;
        } else {// 比父节点大，插入在右边
            if (node.right) {
                this.insertNode(node.right, newNode);// 如果非空，则要再一次判断，插入到子节点下面
                return;
            }

            node.right = newNode;
            return;
        }
    }
//查找结点
    search(value: number) {
        return this.searchNode(this.root, value)
    }
    searchNode(node: Tree, value: number) {
        if (node.value === value) {
            return true
        }
        if (node.value > value) {
            if (node.left) {
                return this.searchNode(node.left, value)
            }
            return false
        } else {
            if (node.right) {
                return this.searchNode(node.right, value)
            }
            return false
        }
    }
    //由于大的在右边，只需要一直寻找最右边的就行了。寻找最小的也同理。
    findMax(): number {
        return this.root ? this.findMaxNode(this.root).value : 0;
    }

    findMaxNode(node: Tree): Tree {
        if (node && node.right)
            return this.findMaxNode(node.right);

        return node;
    }

    findMin(): number {
        return this.root ? this.findMinNode(this.root).value : 0;
    }

    findMinNode(node: Tree): Tree {
        if (node && node.left)
            return this.findMinNode(node.left);

        return node;
    }
    //删除节点
    delete(key: number) {
        this.root = this.deleteNode(this.root, key);
    }
    deleteNode(node: Tree | null, key: number): Tree | null {
        if (!node)
            return undefined; // 如果是空就直接返回空

        if (key < node.value) {
            node.left = this.deleteNode(node.left, key);
            return node;
        } else if (key > node.value) {
            node.right = this.deleteNode(node.right, key);
            return node;
        }

        if (!node.left && !node.right) {
            node = undefined;
            return node;
        }

        if (!node.left) {
            node = node.right;
            return node;
        }

        if (!node.right) {
            node = node.left;
            return node;
        }

        const aux = this.findMinNode(node.right);
        node.value = aux.value;
        node.right = this.deleteNode(node.right, aux.value);

        return node;
    }
    // 获取二叉树节点个数
    size(): number {
        return this.root ? this._size(this.root) : 0;
    }
    _size(subTree: Tree | null): number {
        if (!subTree)
            return 0;
        else
            return 1 + this._size(subTree.left) + this._size(subTree.right);
    }
}
const tree = new BinaryTree()
tree.insert(2);//插入节点
tree.insert(1);
tree.insert(3);
tree.insert(5);
tree.insert(0);
tree.insert(6);
tree.delete(0);//删除节点
console.log(tree);
console.log(`后序遍历------` + tree.postorderTraversal());
console.log(`查找节点------` + tree.search(11));
console.log(`查找最大值------` + tree.findMax());
console.log(`查找最小值------` + tree.findMin());
console.log(`获取节点个数------` + tree.size());
```

关于二叉树的遍历，常见的有：

- 前序遍历

<img src="https://cdn.jsdelivr.net/gh/xuzhangZ/newcdn/img/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20211221200233.png" alt="微信图片_20211221200233" style="zoom:50%;" />

前序遍历的实现思想是：

- 访问根节点
- 访问当前节点的左子树
- 若当前节点无左子树，则访问当前节点的右子

```js
//前序遍历
    private traversal: number[] = [];
    preorderTraversal(): number[] {
        this.traversal = [];

        if (this.root)
            this.preorderTraversalNode(this.root);

        return this.traversal;
    }
    preorderTraversalNode(node: Tree) {
        this.traversal.push(node.value);

        if (node.left)
            this.preorderTraversalNode(node.left);

        if (node.right)
            this.preorderTraversalNode(node.right);
    }
```

- 中序遍历

<img src="https://cdn.jsdelivr.net/gh/xuzhangZ/newcdn/img/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20211221200308.png" alt="微信图片_20211221200308" style="zoom:50%;" />

中序遍历的实现思想是：

- 访问当前节点的左子树
- 访问根节点
- 访问当前节点的右子

```js
//中序遍历
    inorderTraversal(): number[] {
        this.traversal = [];

        if (this.root)
            this.inorderTraversalNode(this.root);

        return this.traversal;
    }

    inorderTraversalNode(node: Tree) {
        if (node.left)
            this.inorderTraversalNode(node.left);

        this.traversal.push(node.value);

        if (node.right)
            this.inorderTraversalNode(node.right);
    }
```

- 后序遍历

<img src="https://cdn.jsdelivr.net/gh/xuzhangZ/newcdn/img/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20211221200341.png" alt="微信图片_20211221200341" style="zoom:50%;" />

后序遍历的实现思想是：

- 访问当前节点的左子树
- 访问当前节点的右子
- 访问根节点

```js
//后序遍历
    postorderTraversal(): number[] {
        this.traversal = [];
        if (this.root)
            this.postorderTraversalNode(this.root);

        return this.traversal;
    }
    private postorderTraversalNode(node: Tree) {
        if (node.left)
            this.postorderTraversalNode(node.left);

        if (node.right)
            this.postorderTraversalNode(node.right);

        this.traversal.push(node.value);
    }
```



## 三、总结

树是一个非常重要的非线性结构，其中二叉树以二叉树最常见，二叉树的遍历方式可以分成前序遍历、中序遍历、后序遍历

同时，二叉树又分成了完成二叉树和满二叉树

