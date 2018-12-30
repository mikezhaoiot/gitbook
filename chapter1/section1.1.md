
##### Docker的安装
---------

- 系统版本为Ubuntu 164.2 LTS,通过`cat /etc/issue`查看，卸载之前的Docker
```C
$ sudo apt-get remove docker docker-engine docker.io
```
- 安装Docker CE需要的软件包
```C
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```

- 添加Docker官方GPG key
```C
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
//执行之后，设备一直无法获取，可能需要翻墙，这个时候需要拆分步骤
通过浏览器下载https://download.docker.com/linux/ubuntu/gpg
安装官方GPG key： sudo apt-key add /xxx/gpg
```
- 安装
```C
sudo apt-get update
sudo apt-get install docker-ce
```

- 安装docker-ce提示,参考[^1]解决

```C
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Package docker-ce is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'docker-ce' has no installation candidate
```
 - 安装完成之后，运行下面的命令，验证是否安装成功

```shell 
$ docker version
# 或者
$ docker info
```
Docker 需要用户具有 sudo 权限，为了避免每次命令都输入sudo，可以把用户加入 Docker 用户组[^2]

```shell
# Create the docker group.
$ sudo groupadd docker

# Add your user to the docker group.
$ sudo usermod -aG docker $USER
```
罗列出本机安装的镜像

```shell
# 列出本机的所有 image 文件。
$ docker image ls

# 删除 image 文件
$ docker image rm [imageName]
```


##### 获取官方Ubuntu镜像

--------------
删除容器
```shell
$ sudo docker rm daemon_ubuntu
```
删除镜像
```shell 
$ sudo docker rmi ubuntu 
```

##### 从镜像中启动容器


docker container run -it ubuntu:16.04  bash 

-it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端

terminal interactive 这个参数可以让我们进入容器的交互式终端


对于那些不会自动终止的容器，必须使用docker container kill 命令手动终止

```shell
docker container kill [containID]
```

启动已终止容器

```shell
docker container start
```

重新进入容器

```shell
docker exec -it id bash 
```


ssh登录

```shell
ssh sugr@127.0.0.1 -p 10022
```


#### 参考
- [Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
- [Docker doc](https://docs.docker.com/install/linux/docker-ce/ubuntu/#set-up-the-repository)
- [在Ubuntu中安装Docker，添加Docker官方GPG key失败，解决方案](https://blog.csdn.net/sunchaoyia/article/details/81231000)
- [关于Docker目录挂载的总结](https://www.cnblogs.com/ivictor/p/4834864.html)
- [Linux主机如何用ssh去登录docker容器的步骤](https://blog.csdn.net/ypbsyy/article/details/80529101)

- [ssh 端口转发实现外网 80 端口映射到内网 80 端口](https://www.cnblogs.com/lesliefang/p/4634408.html)
- [Linux 下非root用户使用docker](https://www.cnblogs.com/mingbai/p/UnrootUseDocker.html)



[^1]: [ubuntu x64 安装docker-ce](https://segmentfault.com/a/1190000015127278)

[^2]: [官方文档](https://docs.docker.com/install/linux/linux-postinstall/)


