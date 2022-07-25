
### 描述

python版本的supervisrod, 在linux、windows系统中离线安装都不怎么方便.  是否有go版本的呢?


## github地址

[https://github.com/ochinchina/supervisord/](https://github.com/ochinchina/supervisord/)

## 踩坑记录

* 编译32版本, 有些报错, 需要修改源码

* 在ui界面快速点击start/stop, 进程可能会启动多个。需要加锁处理处理.

## 用法

* 配置文件查找顺序

```bash
$CWD/supervisord.conf
$CWD/etc/supervisord.conf
/etc/supervisord.conf
/etc/supervisor/supervisord.conf (since Supervisor 3.3.0)
../etc/supervisord.conf (Relative to the executable)
../supervisord.conf (Relative to the executable)
```


* 最简单配置supervisord.conf

```conf
[program:test]
command = /your/program args
```


* 常用配置

```conf
[inet_http_server]          ;http接口
port=:9002
;username=xxxx
;password=xxxx

[supervisorctl]             ;ui界面
serverurl=http://127.0.0.1:9002

[supervisord]               ;日志
logfile=%(here)s/../log/supervisord.log
logfileMaxbytes=50MB        ;文件大小
logfileBackups=10           ;10个文件
loglevel=info               ;日志级别

[program:middleware1]       ;进程
command = %(here)s/../middleware1.exe   ;here指的是supervisord.conf的路径，而不是supervisord.exe的路径
autorestart = true
numprocs=1                  ;进程数量

[program:programname1]
command = %(here)s/../programname1.exe
autorestart = true
numprocs=1
depends_on=middleware1      ;依赖进程


[program:programname2]
command = %(here)s/../programname2.exe
autorestart = true
numprocs=1
depends_on=middleware1
```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)