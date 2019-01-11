#### zlog 简介


#### 安装

- 编译的时候已经指定库和头文件路径，还是提示
```
error while loading shared libraries: libzlog.so.1.1: cannot open shared object file: No such file or directory
```
最终通过运行ldconfig解决[^1]


[^1]:[error while loading shared libraries: xxx.so.x" 错误的原因和解决办法](https://www.cnblogs.com/Anker/p/3209876.html)