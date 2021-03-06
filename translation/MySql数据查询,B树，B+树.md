# MySql数据查询

## 查找方法对比

二分法查找需要先排序，不符合现实情况。二叉树查找不需要排序，但是由于二叉树深度太大，查询次数太多，每一层的数据查询代表一次硬盘IO，所以使用B树、B+树，一般来讲，实际中B+树的深度只有3。

## B树/B+树

走进搜索引擎的作者梁斌老师针对B树、B+树给出了他的意见（为了真实性，特引用其原话，未作任何改动）： “B+树还有一个最大的好处，方便扫库，B树必须用中序遍历的方法按序扫库，而B+树直接从叶子结点挨个扫一遍就完了，B+树支持range-query非常方便，而B树不支持。这是数据库选用B+树的最主要原因。

    比如要查 5-10之间的，B+树一把到5这个标记，再一把到10，然后串起来就行了，B树就非常麻烦。B树的好处，就是成功查询特别有利，因为树的高度总体要比B+树矮。不成功的情况下，B树也比B+树稍稍占一点点便宜。

    B树比如你的例子中查，17的话，一把就得到结果了，
有很多基于频率的搜索是选用B树，越频繁query的结点越往根上走，前提是需要对query做统计，而且要对key做一些变化。

    另外B树也好B+树也好，根或者上面几层因为被反复query，所以这几块基本都在内存中，不会出现读磁盘IO，一般已启动的时候，就会主动换入内存。”非常感谢。

    Bucket Li："mysql 底层存储是用B+树实现的，知道为什么么。内存中B+树是没有优势的，但是一到磁盘，B+树的威力就出来了"。

## 参考文章

[从B树、B+树、B*树谈到R 树](https://blog.csdn.net/v_JULY_v/article/details/6530142)

