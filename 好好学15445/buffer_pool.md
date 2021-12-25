## Buffer Pool

### ETL

ETL是一个常见的名称，它的全称是 Extract Transform Load，一般是用来在oltp和olap两个数据库之间进行切换的。

### Buffer Pool 作用

在我们做database storage的时候，我们需要关心的是，怎么把页面中的东西取出来到内存使用。

所以，我们的buffer pool其实是对应数据库中的页面。一个frame --> 一个page

### page_table

除了在buffer pool中拿到我们需要的page，我们还需要一个特别的数据结构来标识这些页面。

![page_table](F:\blog\好好学15445\pictures\page_table.png)

