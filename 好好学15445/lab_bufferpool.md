## lab1

首先是实验环境搭建，个人使用如下配置，很省心。(试了win10, ubuntu 16, 都很麻烦)

```c++
1. clion， 可以远程同步文件夹，很方便。
2. ubuntu 20.04，高版本的gcc版本高点，后面省心很多。
```

### 1. 下载代码

按照实验说明的，git clone就好了。

```bash
git clone https://github.com/cmu-db/bustub.git
```

然后我们编译

```bash
mkdir build
cd build
cmake ..
make 
```

具体到我们第一步的clock_replacer_test

```bash
cd build 
make clock_replacer_test
./test/clock_replacer_test
```

### 实现clock_replacer_test

**1. clock_replacer.h**

这一部分相对简单，只需要关注两个文件就好。一个是 **clock_replace.h** 。

我们来看文档的描述

>The `ClockReplacer` is initialized to have no frame in it. Then, only the newly unpinned ones will be considered in the `ClockReplacer`. Adding a frame to or removing a frame from a replacer is implemented by changing a reference bit of a frame. The clock hand initially points to the placeholder of frame 0. For each frame, you need to track two things: 1. Is this frame currently in the `ClockReplacer`? 2. Has this frame recently been unpinned (ref flag)?

需要关注两个东西，所以我们用两个数组来标识，为了方便计数，加了一个frame_count变量。更改的地方是 ：

```c++
 private:
  // TODO(student): implement me!
  std::vector<frame_id_t> vt; // 使用一个数组frame_id_t来标识哪一个frame_id_t在clock_replacer里面
  std::vector<bool> refs;  // 用来标识ref标志位
  size_t frame_count = 0;  // 为了不遍历vt，使用一个变量来记录数组中的元素
```

**2. clock_replacer.cpp **

这里就是实现了，同样的我们先看文档：

>You will need to implement the *clock* policy discussed in the class. You will need to implement the following methods:
>
>- `Victim(T*)` : Starting from the current position of clock hand, find the first frame that is both in the `ClockReplacer` and with its ref flag set to `false`. If a frame is in the `ClockReplacer`, but its ref flag is set to `true`, change it to `false` instead. This should be the only method that updates the clock hand.
>- `Pin(T)` : This method should be called after a page is pinned to a frame in the `BufferPoolManager`. It should remove the frame containing the pinned page from the `ClockReplacer`.
>- `Unpin(T)` : This method should be called when the `pin_count` of a page becomes 0. This method should add the frame containing the unpinned page to the `ClockReplacer`.
>- `Size()` : This method returns the number of frames that are currently in the `ClockReplacer`.

这里格外要注意的是 **Unpin(T)** 的触发条件，**pin_count**为零的时候直接调用，这个意思就是不用考虑ref_flag。关于ref_flag参数，只有两个函数会使用到。

这里我们首先要明白我们实现的算法是什么，我们实现的是clock置换，任何页面都有被换出去的可能性，lecture上举例子就是以，两个扫描为例子，unpin的操作做两件事：

- 将ref_flag置为1，
- 将frame_id加入到数组里面。

为什么要讲ref_flag设置为1?

哈哈，lecture讲了，我们这是一个lru的近似算法，近似就是思想差不多，但是没那么严谨。我们用ref_flag来指，它存在内存中，对于replacer来说，就是有**buffer_pool_manager**指向它。

