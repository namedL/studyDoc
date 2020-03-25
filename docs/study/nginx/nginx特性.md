### nginx的优点的具体体现

1. IO多路复用epoll 【 [什么是IO多路复用](https://www.cnblogs.com/cainingning/p/9556642.html)】

   a. 多个描述符的I/O操作都能在一个线程内并发交替的顺序完成，这就叫I/O多路复用

   这里`复用`指的是复用同一个线程

   b. IO多路复用的实现方式：select、poll、epoll

   ​	select模型缺点：能够监视文件扫描符的数量存在最大限制（1024个）

   ​				          线性扫描效率低下

     epoll模型：每当FD就绪，采用系统的回调函数直接将fd放入，效率更高

   ​			           最大连接无限制

2. 轻量级

   1. 功能模块少

   2. 代码模块化

   3. CPU亲和

      一种把CPU核心和nginx工作进程绑定的方式，把每个worker进程固定在一个cpu上执行，减少切换cpu的chche miss，获得更好的性能

   4. sendfile：处理静态文件的机制，直接通过内核处理文件