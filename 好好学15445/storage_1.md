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

**文件**

组成了一个数据库。
