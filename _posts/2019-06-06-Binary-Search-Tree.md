---
layout: post
title: "Binary Search Tree"
date: 2019-06-06 11:11:20 +0800
comments: true
categories: bst
---


### Binary Search Tree

#### 定义

> 二叉查找树（英文名 Binary Search Tree ）又名二叉搜索树、有序二叉树。是一种树形数据结构，同时又能保证数据的有序，方便搜索、插入、删除操作。它有满足如下条件：



    1. 如果存在左子树，那么左边子节点树下的所有节点值都比当前值小。
    2. 如果存在右子树，那么右边子节点树下的所有节点值都比当值值大。
    3. 左右子树都满足二叉搜索树的特征。
    4. 不存在键值相对的节点。


>  二叉查找树方便插入和查找数据，平均时间复杂度是O(logN).


#### 二叉树的应用

#### 查找

例如在二叉树中、查找Key的步骤如下

1. 如果当前树是空的、则直接返回NULL，没有找到。
2. 如果当前遍历的节点的值等于查找的Key，则找到返回。
3. 若当前遍历节点的值大于查找的Key，则遍历左子树。
4. 反之，则遍历右子树。



```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    public TreeNode() {}
    public TreeNode(int val) {this.val = val;}
}

public class BinarySearchTree {

    int TreeNode root;

    public BinarySearchTree() {}

    public TreeNode search(TreeNode root, int key) {

            if (root == null || root.val == key) return root;

            if (root.val > key) return search(root.right, key);

            return search(root.left, key);
    }
}

```


#### 插入

例如在二叉树中、插入Key的元素。

1. 如果当前树是空的、则插入的元素为树的Root节点、则完成。
2. 遍历整个树节点、找到需要插入key的元素所在的树的位置。
3. 如果插入的key大于遍历的当前节点，插入到右子树。
4. 反之，则插入左子树、直到达叶节点。插入的元素总是当做叶节点插入到整个树。


```java
    public void insert(int key) {
        root = insertHelper(root, key);
    }

    private TreeNode insertHelper(TreeNode root, int key) {
        if (root == null) {
            TreeNode node = new TreeNode(key);
            return node;
        }

        if (root.val > key) {
            root.left = insertHelper(root.left, key);
        } else {
            root.right = insertHelper(root.right, key);
        }


        return root;
    }

```


#### 删除

例如在二叉树中、删除制定key的元素。分为3中情况需要讨论。

1. 若待删除的节点下面同时没有左子树和右子树。则直接删掉当前节点即可。
2. 若待删除的节点只有左子树或右子树，则把对应的左子树或右子树直接代替待删除的元素即可。这样还是能保留的树的元素的顺序。
3. 若待删除节点的左子树和右子树都存在。则需要找到以当前节点的右节点为Root的子树中找到最小值的节点（也就是最左边的节点）。然后用来和待删除节点替换。同时把最小左节点删掉即可。

```java
    public void delete(int key) {
        root = deleteHelper(root, key);
    }


    private TreeNode deleteHelper(TreeNode root, int key) {
        if (root == null) return null;

        if (root.val < key) {
            root.right = deleteHelper(root.right, key);
            return root;
        } else if (root.val > key) {
            root.left = deleteHelper(root.left, key);
            return root;
        } else {


            if (root.left == null) {
                return root.right;
            } else if (root.right == null) {
                return root.left;
            } else {

                root.val = findMinValue(root.right); // 找到以当前节点的右子节点为父节点的最左边的一个值。

                root.right = deleteHelper(root.right, root.val); // 删除掉右边节点中子节点中做左边的那个点

                return root;

            }


        }

    }

    private int findMinValue(TreeNode node) {
        int minValue = node.val;

        while(node.left != null) {
            minValue = node.left.val;
            node = node.left;
        }

        return minValue;
    }


```
