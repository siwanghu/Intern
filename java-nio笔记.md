# Java NIO（siwang.hu&nbsp;&nbsp;V1.0）  
> + Java NIO(New IO)是一个可以替代标准Java IO API（从Java 1.4开始)，提供了与标准IO不同的IO工作方式  
>  
> + 标准的IO基于字节流和字符流进行操作的，而NIO是基于通道（Channel）和缓冲区（Buffer）进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中  
>  
> + Java NIO可以让你非阻塞的使用IO  
>  
> + Java NIO引入了选择器的概念，选择器用于监听多个通道的事件（比如：连接打开，数据到达）。因此，单个的线程可以监听多个数据通道  
>  
> ## **核心部分**  
> + channels（通道）  
> + Buffers（缓冲区）  
> + Selectors（选择器）  
>  