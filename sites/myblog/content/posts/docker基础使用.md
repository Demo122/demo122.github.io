---
title: "docker基础"
date: 2022-06-08T13:52:20+08:00
lastmod: 2022-06-09T13:52:20+08:00
draft: false
categories: ["docker","开发工具"]
tags: ["docker"]
---
## 1. docker安装
### ubuntu中安装docker

**1. 卸载旧版本**

``` bash
 sudo apt-get remove docker docker-engine docker.io containerd runc
```

**2. 安装依赖**

```bash
sudo apt-get update
sudo apt-get install \
ca-certificates \
curl \
gnupg \
lsb-release
```

**3. 添加docker官方gpg密钥，这里改成中科大镜像的**

```shell
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

**4. 设置稳定版本库，也换成中科大的**

```shell
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/ \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**5. 安装docker,默认是安装最新版**

```shell
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
​	***你可以使用如下命令查看当前存储库中可用的版本，自己指定版本号***
​	**查看命令为：**

```bash
apt-cache madison docker-ce
```
   **安装，<VERSION_STRING>替换成你的版本号**

```bash
sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io docker-compose-plugin

```

**6. 测试是否安装成功**

```bash
 sudo docker run hello-world
```
**如下，则成功**

```bash
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:10d7d58d5ebd2a652f4d93fdd86da8f265f5318c6a73cc5b6a9798ff6d2b2e67
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
1. The Docker client contacted the Docker daemon.
2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
$ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
https://hub.docker.com/

```

**7. 卸载**

```bash
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin

sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

### 常用docker命令

```shell
docker ps 查看当前运行中的容器
docker images 查看镜像列表
docker rm container-id 删除指定 id 的容器
docker stop/start container-id 停止/启动指定 id 的容器
docker rmi image-id 删除指定 id 的镜像
docker volume ls 查看 volume 列表
docker network ls 查看网络列表
```



## 2. 编写dockerfile制作深度学习镜像

### dockerfile指令

```dockerfile
FROM   		#基础镜像  一切从这里开始构建
MAINTAINER  #镜像是谁写的  名字+邮箱
RUN  		#镜像构建的时候需要运行的命令
ADD			#步骤，tomcat镜像，加一个tomcat压缩包！添加内容
WORKDIR 	#镜像的工作目录
VOLUME		#挂载的目录
EXPOSE		#指定暴露端口
CMD			#指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT	#指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD		#当构建一个被继承DockerFile，这个时候就会运行ONBUILD的指令
COPY		#类似ADD，将我们的文件拷贝到镜像中
ENV			#构建的时候设置环境变量
```

更多参考 [dockerfile文档](https://docs.docker.com/engine/reference/commandline/run/)  [菜鸟dockerfile文档](https://www.runoob.com/docker/docker-command-manual.html)

***dockerfile构建镜像的时候是运行一行命令构建一个，意思就是每行命令运行结束后，运行之后的命令都是一个新的环境***

**例如** `RUN cd /opt/conda` 后再运行其他命令基于的目录仍是 根目录，而不是`/opt/conda`

**如有需要，可以使用`&&`来连接多条指令**

### 多级构建

比如我们要使用cuda、anaconda等多个基础环境的时候就会用到多级构建

多级构建需要用到`COPY`命令来复制上级环境，用法如下

```dockerfile
FROM image-1 as base
WORKDIR /base
RUN do something

FROM image-2 as bulid
WORKDIR /bulid
COPY --from=base /base ./build/base
```



### WORKDIR

工作目录，设置这个后，运行这个镜像直接进入到这个目录

在dockerfile中某一级别设置工作目录后，之后的命令都会在这个目录下进行（因为再次进入这个镜像的默认目录就是工作目录）



### 使用conda打包环境，供离线安装

**1. 安装打包工具**

```bash
pip install conda-pack
```

**2. 打包conda环境**

```bash
conda pack -n 虚拟环境名称  -o xxx.tar.gz
或者
conda pack -n 虚拟环境名称 --ignore-editable-packages -o xxx.tar.gz
```

**3. 上传到需要安装的机器上，解压安装**

**为了方便使用conda命令激活，推荐解压到anaconda安装目录的envs目录中**

在envs目录下创建环境名

```shell
mkdir env_name
```

解压环境

```shell
tar -xzf xxx.tar.gz -C env_name
```

激活使用环境

```shell
conda env list
source activate env_name
```

### 实例：打包mmsegmentation深度学习环境

**dockerfile的RUN命令默认是使用`/bin/sh`**

```dockerfile
# 从conda开始构建
FROM continuumio/anaconda3:2020.07

# 作者
MAINTAINER xxxx

# 从cuda复制基础环境
COPY --from=nvidia/cuda:11.4.0-devel-ubuntu20.04 / /

# 复制代码
COPY swin_seg /swin_seg

# 复制conda环境
COPY mmseg.tar.gz /opt/conda/envs/

# 解压、创建conda环境
RUN mkdir /opt/conda/envs/mmseg
RUN tar -zxf /opt/conda/envs/mmseg.tar.gz -C /opt/conda/envs/mmseg

# 设置全局shell环境，使用/bin/bash
SHELL ["/bin/bash","-c"]

# 安装mmcv的环境
WORkDIR /swin_seg
RUN source activate mmseg && pip install -r requirements.txt && pip install --no-cache-dir -e .

RUN echo "source activate mmseg" >> ~/.bashrc

```

**编译、构建镜像**

```bash
docker build -t swin_seg:v1 .
```

**导出镜像**

`格式：docker save imagesID(或者image_id) > /存放位置/打包文件名.tar`  **[参考](https://blog.csdn.net/ichen820/article/details/119144468)**

```shell
docker save imagesID > /xxx.tar
```

**导入**

`格式：docker load < 打包文件名.tar`

```shell
docker load < xx.tar
```



### 运行深度学习镜像

**使用显卡，需要安装NVIDIA Container Toolkit**

安装参考[NVIDIA Container Toolkit安装指南](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#)

**运行容器需要注意两个参数**

```shell
docker run -idt --shm-size 4096m  --gpus all --name swin_seg xx:xx

# 参数一 
--shm-size  # 设置容器的共享内存大小，这里设置的4g

# 参数二
--gpus all # 开启gpu

```

**[详细文档](https://docs.docker.com/engine/reference/commandline/run/)**
