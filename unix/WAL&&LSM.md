# WAL

wal 是 数据库的预写日志（write-ahead logging)，是关系数据库中提供原子性和持久性的技术。

在使用WAL的系统中，所有的**修改** 在 **提交** 之前都要先写入log文件中。我们要想要研究透彻wal的格式，我们就做一个实验。

>实验思路来自 https://www.modb.pro/db/48191

1. 疑惑：这里的changes是只指update么？还是insert也是可以的？







