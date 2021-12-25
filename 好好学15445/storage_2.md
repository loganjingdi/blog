## Slotted pages VS LSF

我们在前面讲了page的构造，这里明确一个概念，每一个slot对应的是一个tuple（一般是固定长度)

![slot_pages](.\pictures\slot_pages.png)

像这样，一般我们的tuple是不会超过一个页面的（4kb)，但是有一些例外，比如大文件什么的，这个时候一个page不够存，我们就需要用TOAST（一般大于1/2页面大小就用toast存储）这种来存储。

TOAST这种就是，利用其他连续的pages或者是外部文件（oracle之类的使用）

![toast](.\pictures\toast.png)

### 行存或者列存

**行存** （NSM : N-array Storage Model)

就是把一个tuple当成一个整体，然后放在slot里面，适合场景为：

- 只查询几个特殊的行
- 插入行

只满足几个特殊的行的情况下，主要的cost在查询，一旦找到对应的页面，就很好处理了。

插入就更好理解了，因为是基于lsm的插入，天然对写友好。

**列存**  (DSM: Decomposition Storage Model)

就是按照一个列来将数据存放到页面中，要得到一个完整的tuple需要自己组装（利用offset 或者 利用行标识）这种的好处是：

- 查询某一个列的情况下，速度特别快

行存的优点就是列存的缺点。。。



