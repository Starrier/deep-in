---
description: >-
  R-B Tree，全称是Red-Black
  Tree，又称为“红黑树”，它一种特殊的二叉查找树。红黑树的每个节点上都有存储位表示节点的颜色，可以是红(Red)或黑(Black)。
---

# RedBlackTree

 **红黑树的特性**:

1. 每个节点或者是黑色，或者是红色。
2. 根节点是黑色。
3. 每个叶子节点是黑色（这里的叶节点是指为空的叶子节点）
4. 如果一个节点是红色，则它的子节点必须是黑色的
5. 从一个节点到该节点的子孙节点的所有路径包含相同数目的黑色节点。

{% hint style="warning" %}
 3 中的叶子节点，是只为空（NIL 或 Null）的节点。

5 中要确保没有一条路径会比其他路径长出两倍。因而，  
\(01\) 特性\(3\)中的叶子节点，是只为空\(NIL或null\)的节点。  
\(02\) 特性\(5\)，确保没有一条路径会比其他路径长出俩倍。因而，红黑树是相对是接近平衡的二叉树。
{% endhint %}

![RedBlackTree](../.gitbook/assets/redblacktree.png)

**红黑树的时间复杂度和相关证明**

定理：**一棵含有n个节点的红黑树的高度至多为2log\(n+1\)**.

证明：  
    "一棵含有n个节点的红黑树的高度至多为2log\(n+1\)" 的**逆否命题**是 "高度为h的红黑树，它的包含的内节点个数至少为 2h/2-1个"。  
    我们只需要证明逆否命题，即可证明原命题为真；即只需证明 "高度为h的红黑树，它的包含的内节点个数至少为 2h/2-1个"。

    从某个节点x出发（不包括该节点）到达一个叶节点的任意一条路径上，黑色节点的个数称为该节点的黑高度\(x's black height\)，记为**bh\(x\)**。关于bh\(x\)有两点需要说明：   
    第1点：根据红黑树的"**特性\(5\)** ，即_从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点_"可知，从节点x出发到达的所有的叶节点具有相同数目的黑节点。**这也就意味着，bh\(x\)的值是唯一的**！  
    第2点：根据红黑色的"特性\(4\)，即_如果一个节点是红色的，则它的子节点必须是黑色的_"可知，从节点x出发达到叶节点"所经历的黑节点数目"&gt;= "所经历的红节点的数目"。假设x是根节点，则可以得出结论"**bh\(x\) &gt;= h/2**"。进而，我们只需证明 "高度为h的红黑树，它的包含的黑节点个数至少为 2bh\(x\)-1个"即可。

    到这里，我们将需要证明的定理已经由  
**"一棵含有n个节点的红黑树的高度至多为2log\(n+1\)"**   
    转变成只需要证明  
**"高度为h的红黑树，它的包含的内节点个数至少为 2bh\(x\)-1个"。**

  
下面通过"数学归纳法"开始论证高度为h的红黑树，它的包含的内节点个数至少为 2bh\(x\)-1个"。

\(01\) 当树的高度h=0时，  
    内节点个数是0，bh\(x\) 为0，2bh\(x\)-1 也为 0。显然，原命题成立。

\(02\) 当h&gt;0，且树的高度为 h-1 时，它包含的节点个数至少为 2bh\(x\)-1-1。这个是根据\(01\)推断出来的！

    下面，由树的高度为 h-1 的已知条件推出“树的高度为 h 时，它所包含的节点树为 2bh\(x\)-1”。

    当树的高度为 h 时，  
    对于节点x\(x为根节点\)，其黑高度为bh\(x\)。  
    对于节点x的左右子树，它们黑高度为 bh\(x\) 或者 bh\(x\)-1。  
    根据\(02\)的已知条件，我们已知 "x的左右子树，即高度为 h-1 的节点，它包含的节点至少为 2bh\(x\)-1-1 个"；

    所以，节点x所包含的节点至少为 \( 2bh\(x\)-1-1 \) + \( 2bh\(x\)-1-1 \) + 1 = 2^bh\(x\)-1。即节点x所包含的节点至少为 2bh\(x\)-1。  
    因此，原命题成立。

    由\(01\)、\(02\)得出，"高度为h的红黑树，它的包含的内节点个数至少为 2^bh\(x\)-1个"。  
    因此，“一棵含有n个节点的红黑树的高度至多为2log\(n+1\)”。
