+++
title = "ceph分布式文件系统环境搭建"
date = 2022-07-25T12:04:00+08:00
categories = ["docker"]
tags = ["ceph", "docker", "docker-compose"]
draft = false
summary = "wikijs文档有docker-compose安装说明, 实际中会遇到几个问题"
+++

### 目的 

在一台机器上, 利用多块硬盘, 搭建一个cephfs文件系统. 具体来说就是1个mon, 1个mds, 1个mgr, 3个osd

### 注意

a. 使用vmware会很方便

b. 安装过程中会遇到很多问题，我都没有记录, 尽量安装下面步骤

### 环境准备

a. vmware虚拟机fedora30

b. 添加3块虚拟机硬盘 /dev/sdb  /dev/sdc /dev/sdd (osd最少需要3个,需要有3块磁盘)

c. ceph容器版本 ceph/daemon:latest-luminous


### 搭建步骤

1. 下载镜像

```
docker pull ceph/daemon:latest-luminous
```

2. 挂载硬盘
vmware虚拟机添加硬盘很方便, 直接加就可以. fdisk -l 查看硬盘

3. 清理硬盘

```
# 格式化
mkfs.xfs /dev/sdb -f
mkfs.xfs /dev/sdc -f
mkfs.xfs /dev/sdd -f

# 如果已经是xfs格式, 上面命令并不能清除已有数据, 需要用zap_device清理
docker run -d --net=host --name=osd0  --rm \
--privileged=true \
-v /dev/:/dev/ \
-e OSD_DEVICE=/dev/sde  \
 ceph/daemon:latest-luminous zap_device
```

4. 准备目录

```
/root/ceph
/root/ceph/etc
/root/ceph/lib
```


5. 启动mon (监控节点必需)

```
docker run -d --net=host  --name=mon \
-v /root/ceph/etc:/etc/ceph \
-v /root/ceph/lib/:/var/lib/ceph/ \
-e MON_IP=192.168.10.125 \
-e CEPH_PUBLIC_NETWORK=192.168.10.0/24 \
 ceph/daemon:latest-luminous mon
```

6. 启动mgr(可以不用)

```
docker run -d --net=host --name=mgr \
-v /root/ceph/etc:/etc/ceph  \
-v /root/ceph/lib/:/var/lib/ceph  \
ceph/daemon:latest-luminous  mgr
```

7. 启动osd

```
# 修改-name和OSD_DEVICE启动三个osd
docker run -d --net=host --name=osd0   \
--privileged=true \
-v /root/ceph/etc:/etc/ceph  \
-v /root/ceph/lib/:/var/lib/ceph  \
-v /dev/:/dev/ \
-e OSD_DEVICE=/dev/sdb  \
-e OSD_TYPE=disk \
 ceph/daemon:latest-luminous osd
```

8. 启动mds(cephfs系统必需)

```
#  一定要在osd之后创建启动, 因为CEPHFS_CREATE=1会创建cephfs文件系统，受osd数量影响
docker run -d --net=host --name=mds \
-v /root/ceph/etc:/etc/ceph \
-v /root/ceph/lib/:/var/lib/ceph/ \
-e CEPHFS_CREATE=1 \      # 默认创建cephfs文件系统
  ceph/daemon:latest-luminous mds
```

9. 进入mon查看ceph状态

```
# 进入容器
docker exec -it mon bash

# 查看状态
[root@localhost /]# ceph -s
  cluster:
    id:     4d74fd53-84e0-47e6-a06c-5418e4b3b653
    health: HEALTH_WARN
            1 MDSs report slow metadata IOs
            2 osds down
            34/51 objects misplaced (66.667%)
            Reduced data availability: 4 pgs inactive, 16 pgs stale
            Degraded data redundancy: 16 pgs undersized
            too few PGs per OSD (4 < min 30)
 
  services:
    mon: 1 daemons, quorum localhost
    mgr: localhost(active)
    mds: cephfs-1/1/1 up  {0=localhost=up:creating}
    osd: 5 osds: 2 up, 4 in
 
  data:
    pools:   2 pools, 16 pgs
    objects: 17 objects, 2.19KiB
    usage:   4.01GiB used, 75.6GiB / 79.6GiB avail
    pgs:     25.000% pgs not active
             34/51 objects misplaced (66.667%)
             12 stale+active+undersized+remapped
             4  stale+undersized+peered
```

10. ceph调参: too few PGs per OSD (4 < min 30)

存储池的pg_num, pgp_num太小了, 设置大一点

```
ceph osd pool set cephfs_data pg_num 64
ceph osd pool set cephfs_data pgp_num 64
ceph osd pool set  cephfs_metadata pg_num 32
ceph osd pool set  cephfs_metadata pgp_num 32
```

11. ceph调参: mds: cephfs-1/1/1 up  {0=localhost=up:creating}

mds一直处在creating状态, 因为默认I/O需要的最小副本数是2，我们需要调成1

```
ceph osd pool set cephfs_metadata min_size 1
ceph osd pool set cephfs_data min_size 1
```

12. 再看ceph状态, mds状态是active表示cephfs搭建好了

```
mds: cephfs-1/1/1 up  {0=localhost=up:active}
```

13. 挂载cephfs目录(直接mount)

```
# 获取key
cat /root/ceph/etc/ceph.client.admin.keyring
# 直接挂载
mount -t ceph 192.168.10.125:6789:/ /root/abc -o name=admin,secret=AQAvoctebqeuBRAAp+FoatmQ5CUlSlo8dmvGAg==
# 取消挂载
umount /root/abc
```

14. 挂载cephfs目录(ceph-fuse)

```
# 安装ceph-fuse
yum install ceph-fuse

# 挂载(-k指定key -c表示配置文件)
ceph-fuse -m 192.168.10.125:6789 /root/abc1 -k /root/ceph/etc/ceph.client.admin.keyring  -c /root/ceph/etc/ceph.conf

#取消挂载
umount /root/abc1
```

15. 查看结果

```
df -h

192.168.10.125:6789:/                     18G     0   18G    0% /root/abc
ceph-fuse                                 18G     0   18G    0% /root/abc1
```

[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
