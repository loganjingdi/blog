# 数据的设计目标

很简单：就是让数据库有能力获取超过内存容量的数据

注意：IO是很耗时的，所以需要更智能的管理数据以避免性能下降

## buffer_pool && Disk

内存中先开辟一个buffer pool 供后续的页面置换使用

![pufferpoll_disk](.\pictures\pufferpool_disk.png)

通过定位到具体的文件，加载directory信息和具体的页面到buffer pool里面。

![bufferpool_disk_2](.\pictures\bufferpool_disk_2.png)



## 不使用mmap

为什么不使用mmap，在我们使用多个线程对mmap文件进行访问的时候，如果涉及到多写，会非常复杂，对于多读，因为可以在内存中找到所要的页面，所以是ok的。

> mmap的相关知识可以在这个链接找到 
>
> [mmap](../unix/mmap.md)

这里再扯一点哲学的东西：

- **DBMS always wants to control things itself **

包括prefetching，页面置换这种。

**解决方法：**

- madvise : [madvise 预读](https://www.cnblogs.com/wlzy/p/10665472.html)
- mlock : [mlock 锁住内存不被置换出去](https://blog.csdn.net/fjt19900921/article/details/8074541)
- msync : [msync 强制映射空间转换写回磁盘](https://blog.51cto.com/changer/1285696)



## 数据在磁盘的组织形式

数据库在DBMS系统中就是一个或者一系列的文件。

### 文件

组成了一个数据库。其中 **文件管理器** 控制着文件的读写，并且管理着一系列的页面pages:

- 管理 "pages" 的读写
- 管理着可用空间

**页面**

页面是一段大小固定的数据段。

一个页面可以存储一个特定类型的数据，一般一个页面不会同时存储两个类型的数据（索引，行）这种。

每一个页面 都有一个特定的标识符。数据库**管理** 着 page ids 到 物理位置 的映射。

数据库中的页面大小：oracle-- 4kb, postgres-- 8kb, mysql--16kb。

操作系统中的页面大小：4kb。

**数据库页面管理**

数据库页面一般都是存储在一个 **heap file** 的数据结构（开始吓我一跳，还以为是堆，后来一查才发现只是一个抽象，需要两个数据结构共同配合）。

- 链表的实现（记录可用的page，和空闲的page）
- page directory的实现（类似于map，把数据库页面映射到物理页面）

![heap_file_linkedlist](F:\blog\好好学15445\pictures\heap_file_linkedlist.png)

![heap_file_pagedirectory](F:\blog\好好学15445\pictures\heap_file_pagedirectory.png)

---

### 页面布局

每一个页面都有一系列的header来记录本页面的一些基本信息：

两种实现方式：

1. **存储每一行的数据**

为了方便在合适的位置，找到插入的数据，我们为每一个tuple建立了一个id，在页面的slot array数组里面，

![slot_pages](.\pictures\slot_pages.png)

2.  **记录日志**

这种方式是直接记录日志在文件里面，读取的时候，从后往前读，生成tuple。

> 基于日志记录的可以看一下, [WAL 和 LSM ](../unix/WAL&&LSM.md)

![log_structured_file](F:\blog\好好学15445\pictures\log_structured_file.png)

