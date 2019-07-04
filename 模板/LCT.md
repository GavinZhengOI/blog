# [算法模版]Link-Cut-Tree

博主懒本博客只对现有博客进行补充，先直接放隔壁链接。

[FlashHu-LCT总结](https://www.cnblogs.com/flashhu/p/8324551.html)

[Menci-LCT学习笔记](https://oi.men.ci/link-cut-tree-notes/)

## make-root操作

make-root操作用于把任何一个点反转到当前树的根节点。

做法是先把要进行操作的节点x进行access，将root和x进行连通。然后进行splay(x)操作，把x变成splay的根。（请注意，这时候x在主树的深度仍然没有改变）。

随后将x的子树全部进行反转操作。也就是改变了这个splay的深度。虽然splay和splay之间的连接需要依赖深度关系（一棵splay的虚边连接向当前splay中序遍历序列的首位在原树上的父亲）。但是因为相对关系不变，所以不影响。

看图（图来自 动态仙人掌系列题解之四）：

![LCT-1](/Users/gavinzheng/Documents/GitHub/blog/模板/pic/LCT-1.png)

可以把连接老根和新根的splay看作一个无法弯曲的杆子，其他splay都是连接在杆子上的块。旋转操作虽然会改变杆子上每一点的深度，但是却不会改变块和杆子上连接点的相对深度关系。所以不会这样变换老根和新根不会对树的结构造成破坏。