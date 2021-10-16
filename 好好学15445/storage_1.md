# 数据的设计目标

很简单：就是让数据库有能力获取超过内存容量的数据

注意：IO是很耗时的，所以需要更智能的管理数据以避免性能下降

## buffer_pool && Disk

内存中先开辟一个buffer pool 供后续的页面置换使用

![pufferpoll_disk](.\pictures\pufferpool_disk.png)

通过定位到具体的文件，加载directory信息和具体的页面到buffer pool里面。

![bufferpool_disk_2](.\pictures\bufferpool_disk_2.png)



## 不使用mmap

为什么不使用mmap，在我们使用多个线程对mmap文件进行访问的时候，如果涉及到多写，会非常复杂，对于多读，是ok的。

> mmap的相关知识可以在这个链接找到
>
> <a> mmap ()</a>

这里再扯一点哲学的东西：

- **DBMS always wants to control things itself **

包括prefetching，页面置换这种。



## 数据在磁盘的组织形式

