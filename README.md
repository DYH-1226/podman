# podman (docker) ---- 日常使用的基础命令

#### 个人镜像 
```bash
>>> podman pull docker.io/mooxe/node
>>> podman pull docker.io/mooxe/deno
```

#### 本地镜像的导入，导出
- 方法一：使用export和import
  ```bash
  # 注意export import 是通过容器来导入，导出镜像。
  # 使用podman ps -a来查看容器id
  # 导出镜像
  >>> podman export [容器id] > xxxx.tar
  # 导入镜像
  >>> podman import - [image_name] < xxxx.tar
  ```
- 方法二：使用save和load
  ```bash
  # save和load  与  export和import 不同的是保存和加载的镜像文件
  # 使用podman images来查看镜像id
  # 保存镜像
  >>> podman save [镜像id] > xxxx.tar
  # 载入镜像
  >>> podman load < xxxx.tar
  ```
- *podman export* 的应用场景：主要用来制作基础镜像，比如我们从一个 ubuntu 镜像启动一个容器，然后安装一些软件和进行一些设置后，使用 podman export 保存为一个基础镜像。然后，把这个镜像分发给其他人使用，比如作为基础的开发环境。

- *podman save* 的应用场景：如果我们的应用是使用 podman-compose.yml 编排的多个镜像组合，但我们要部署的客户服务器并不能连外网。这时就可以使用 podman save 将用到的镜像打个包，然后拷贝到客户服务器上使用 podman load 载入。

#### 本地镜像的运行
```bash 
# 运行本地镜像
>>> podman run -ti --rm -p 9000:9000 [镜像id] /bin/bash
# 让本地文件运行在容器中
>>> podman run --rm -ti -v $PWD:/root/deno -p 3000:3000 mooxe/node /bin/bash
```

#### podman wsl2 在windows重启后出错
参考链接：https://www.cnblogs.com/turingguo/p/14986014.html
```bash
# 1. error joining network namespace for container
# 解决办法
>>> podman stop $(podman ps 2>&1 | grep -oE 'for container [a-f0-9]+' | awk '$0=$NF')
>>> podman start $(podman ps --format '{{.Names}}' --filter status=exited)

# 2. Error refreshing volume
# podman ps -a 出现一系列ERRO[0000]错误
# 解决办法
>>> podman system renumber
```