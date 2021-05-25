## Centos8安装docker

1、Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。

 通过 **uname -r** 命令查看你当前的内核版本

```bash
[root@epg-0001 ~]# uname -r
4.18.0-80.el8.x86_64
```

2、使用 `root` 权限登录 Centos。确保 yum 包更新到最新。

```bash
[root@epg-0001 ~]# yum update
Repository AppStream is listed more than once in the configuration
Repository BaseOS is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository PowerTools is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration
Last metadata expiration check: 0:09:18 ago on Fri 25 Dec 2020 09:04:58 PM CST.
Dependencies resolved.
Nothing to do.
Complete!
[root@epg-0001 ~]# 
```

3、卸载旧版本（如果安装过旧版本的话）

```bash
[root@epg-0001 ~]# yum remove docker  docker-common docker-selinux docker-engine
```

4、安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

```bash
[root@epg-0001 ~]# yum install -y yum-utils device-mapper-persistent-data lvm2
```

5、设置yum源

⚠️：国外镜像一般很难访问，建议配置阿里云镜像。

yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

```bash
[root@epg-0001 ~]# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
Repository AppStream is listed more than once in the configuration
Repository BaseOS is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository PowerTools is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration
Adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
[root@epg-0001 ~]# 
```

6、可以查看所有仓库中所有docker版本，并选择特定版本安装

```bash
[root@epg-0001 ~]# yum list docker-ce --showduplicates | sort -r
Repository AppStream is listed more than once in the configuration
Repository BaseOS is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository PowerTools is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration
Last metadata expiration check: 0:00:02 ago on Fri 25 Dec 2020 09:20:58 PM CST.
docker-ce.x86_64                3:20.10.1-3.el8                 docker-ce-stable
docker-ce.x86_64                3:20.10.0-3.el8                 docker-ce-stable
docker-ce.x86_64                3:19.03.14-3.el8                docker-ce-stable
docker-ce.x86_64                3:19.03.13-3.el8                docker-ce-stable
Docker CE Stable - x86_64                       887  B/s | 7.1 kB     00:08    
Available Packages
[root@epg-0001 ~]# 
```

7、安装docker

```bash
[root@epg-0001 ~]# yum install docker-ce
Repository AppStream is listed more than once in the configuration
Repository BaseOS is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository PowerTools is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration
Docker CE Stable - x86_64                                                                  107 kB/s |  28 kB     00:00    
Error: 
 Problem: package docker-ce-3:20.10.1-3.el7.x86_64 requires containerd.io >= 1.4.1, but none of the providers can be installed
  - cannot install the best candidate for the job
  - package containerd.io-1.4.3-3.1.el7.x86_64 is filtered out by modular filtering
(try to add '--skip-broken' to skip uninstallable packages or '--nobest' to use not only best candidate packages)
[root@epg-0001 ~]# 
```

⚠️：安装 docker-ce 报错：

```
Error:  Problem: package docker-ce-3:20.10.1-3.el7.x86_64 requires containerd.io >= 1.4.1, but none of the providers can be installed
```

于是安装containerd.io ：

```bash
yum install https://download.docker.com/linux/centos/8/x86_64/stable/Packages/containerd.io-1.4.3-3.1.el8.x86_64.rpm
```

⚠️：如果出现 podman 冲突的问题，大致错误如下：

```bash
[root@epg-0003 ~]# yum install https://download.docker.com/linux/centos/8/x86_64/stable/Packages/containerd.io-1.4.3-3.1.el8.x86_64.rpm
Repository AppStream is listed more than once in the configuration
Repository BaseOS is listed more than once in the configuration
Repository extras is listed more than once in the configuration
Repository PowerTools is listed more than once in the configuration
Repository centosplus is listed more than once in the configuration
Last metadata expiration check: 0:00:25 ago on Sun 27 Dec 2020 01:15:16 AM CST.
containerd.io-1.4.3-3.1.el8.x86_64.rpm                                                                          1.1 MB/s |  33 MB     00:30    
Error: 
 Problem: problem with installed package podman-1.6.4-10.module_el8.2.0+305+5e198a41.x86_64
  - package podman-1.6.4-10.module_el8.2.0+305+5e198a41.x86_64 requires runc >= 1.0.0-57, but none of the providers can be installed
  - package containerd.io-1.4.3-3.1.el8.x86_64 conflicts with runc provided by runc-1.0.0-65.rc10.module_el8.2.0+305+5e198a41.x86_64
  - package containerd.io-1.4.3-3.1.el8.x86_64 obsoletes runc provided by runc-1.0.0-65.rc10.module_el8.2.0+305+5e198a41.x86_64
  - conflicting requests
  - package runc-1.0.0-64.rc10.module_el8.2.0+304+65a3c2ac.x86_64 is filtered out by modular filtering
  - package containerd.io-1.2.10-3.2.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.2.13-3.1.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.2.13-3.2.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.2.2-3.3.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.2.2-3.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.2.4-3.1.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.2.5-3.1.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.2.6-3.3.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.3.7-3.1.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.3.9-3.1.el7.x86_64 is filtered out by modular filtering
  - package containerd.io-1.4.3-3.1.el7.x86_64 is filtered out by modular filtering
(try to add '--allowerasing' to command line to replace conflicting packages or '--skip-broken' to skip uninstallable packages or '--nobest' to use not only best candidate packages)
```

则可以执行如下命令解决 podmon 冲突：

```
[root@epg-0003 ~]# yum erase podman buildah
```

然后重新安装 docker：

```bash
yum install docker-ce      #由于repo中默认只开启stable仓库，故这里安装的是最新稳定版
yum install <FQPN>         # 例如： yum install docker-ce-3:20.10.1-3.el8
```

8、启动并加入开机启动

```bash
systemctl start docker
systemctl enable docker
```

9、验证安装是否成功（有client和service两部分表示docker安装启动都成功了）

```bash
[root@epg-0001 ~]# docker version
Client: Docker Engine - Community
 Version:           20.10.1
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        831ebea
 Built:             Tue Dec 15 04:37:17 2020
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.1
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       f001486
  Built:            Tue Dec 15 04:35:42 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.3
  GitCommit:        269548fa27e0089a8b8278fc4fc781d7f65a939b
 runc:
  Version:          1.0.0-rc92
  GitCommit:        ff819c7e9184c13b7c2607fe6c30ae19403a7aff
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
[root@epg-0001 ~]# 
```



### 确保网络模块开机自动加载

```bash
[root@ecs-2aae ~]# lsmod |grep overlay
overlay               126976  10
[root@ecs-2aae ~]# lsmod | grep br_netfilter
br_netfilter           24576  0
bridge                188416  1 br_netfilter
[root@ecs-2aae ~]# 


####################################################
# 如上面命令无返回值输出或提示文件不存在，需执行以下命令
cat > /etc/modules-load.d/docker.conf <<EOF
overlay
br_netfilter
EOF
modprobe overlay
modprobe br_netfilter
```



### 使桥接流量对 iptables 可见

```bash
cat > /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system

## 验证是否生效， 如均返回 1 即正确
sysctl -n net.bridge.bridge-nf-call-iptables
sysctl -n net.bridge.bridge-nf-call-ip6tables
```



### 配置 docker

```bash
mkdir /etc/docker
#修改cgroup驱动为systemd[k8s官方推荐]、限制容器日志量、修改存储类型，最后的docker家目录可修改
cat > /etc/docker/daemon.json <<EOF
{
  // 配置仓库镜像地址
  "registry-mirrors": [
     "https://7uuu3esz.mirror.aliyuncs.com"
  ],
  // 默认http私有仓库不能访问，设置后才可以
  "insecure-registries": [
     "http://192.168.1.61"
  ],
  // 开启docker-API远程访问
  "hosts": [
     "tcp://0.0.0.0:2375",
     "unix:///var/run/docker.sock"
  ]
}
EOF
# 重启 docker 生效
systemctl daemon-reload
systemctl restart docker
```



### 验证 docker 是否正常

```bash
#查看docker信息，判断是否与配置一致
[root@ecs-2aae ~]# docker info
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)
  buildx: Build with BuildKit (Docker Inc., v0.5.0-docker)

Server:
 Containers: 10
  Running: 10
  Paused: 0
  Stopped: 0
 Images: 9
 Server Version: 20.10.1
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 269548fa27e0089a8b8278fc4fc781d7f65a939b
 runc version: ff819c7e9184c13b7c2607fe6c30ae19403a7aff
 init version: de40ad0
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 4.18.0-80.el8.x86_64
 Operating System: CentOS Linux 8 (Core)
 OSType: linux
 Architecture: x86_64
 CPUs: 4
 Total Memory: 7.6GiB
 Name: ecs-2aae
 ID: TUMK:ZAXS:B73T:5BN5:G6AI:ON26:VQG6:NZOZ:RBE6:BVTX:LGEM:6PJO
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Registry Mirrors:
  https://registry.docker-cn.com/
  https://gj9l74ds.mirror.aliyuncs.com/
  http://hub-mirror.c.163.com/
 Live Restore Enabled: false

WARNING: API is accessible on http://192.168.1.61:2375 without encryption.
         Access to the remote API is equivalent to root access on the host. Refer
         to the 'Docker daemon attack surface' section in the documentation for
         more information: https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface
WARNING: No blkio weight support
WARNING: No blkio weight_device support
[root@ecs-2aae ~]# 
```



### 修改docker 默认存储路径

【docker 安装后的默认存储路径是 /var/lib/docker （查看上面的 Docker Root Dir 属性），这里存储着镜像、容器等相关文件。】

1、创建docker新的默认目录

```
mkdir -p /home/docker
```

2、修改docker的systemd的 docker.service的配置文件

不知道 配置文件在哪里可以使用systemd 命令显示一下.

```bash
systemctl disable docker
systemctl enable docker
# 显示结果
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
```

3、修改 **docker.service** 文件

```
vim /usr/lib/systemd/system/docker.service
# 在里面的EXECStart的后面增加后如下: 
ExecStart=/usr/bin/dockerd --graph /home/docker
```

4、然后重启 docker

```bash
systecmtl daemon-reload
systemctl restart docker
```



### 添加用户到docker组

非root用户，无需sudo即可使用docker命令

```bash
#添加用户到docker组
usermod -aG docker <USERNAME>
#当前会话立即更新docker组
newgrp docker
```

