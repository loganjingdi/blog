## WIFI钓鱼

wifi钓鱼项目目前进展：

1. setup AP 这一部分是ok的（目前持续时间不太长，多人使用的时候会掉线，这块不知道是硬件问题还是设置问题）。主要参考步骤是这一篇博客：

>https://www.quora.com/How-do-I-create-a-Wi-Fi-hotspot-on-my-Kali-Linux-machine

2. netfilter这一块，目前编译好了模块，也通过flask建立了一个测试网站（这一块和上面的耦合度很低）。需要的东西：

   1. kali 内核 （uname -r)
   2. kali 内核头文件 (uname -r)

   两个东西相对应就可以了，内核头文件和内核版本要一致。

   博客在这里：

   >https://blog.csdn.net/qq_33386311/article/details/89388967?spm=1001.2101.3001.6650.5&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-5.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-5.no_search_link

   代码在这里：

   >https://github.com/chzhyang/tcpip-security/tree/master/netfilter
   >
   >https://github.com/greyli/helloflask/tree/master/demos

   这一块大家可以都玩玩。

   kali的一些命令:

   ```bash
   apt-cache search linux-header # 查找对应的头文件，编译module需要使用
   apt-cache search linux-image  # 查找对应的镜像，老师给的 i7的版本没有对应的header，我使用的是 Linux kali 5.14.0-kali4-amd64 #1 SMP Debian 5.14.16-1kali1 (2021-11-05) x86_64 GNU/Linux
   
   ```

   

---

## LKM编程

lkm是为了向内核