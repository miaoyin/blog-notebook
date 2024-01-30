+++
title = "openssh8.6默认不支持公钥ssh"
date = 2022-07-25T12:12:00+08:00
summary = "最近升级了自己的linux后, 更新gogs代码时，发现用不了了"
categories = ["linux"]
tags = ["gogs", "openssh"]
draft = false
+++

### 起因

    最近升级了自己的linux后, 更新gogs代码时，发现用不了了

    反复测试原因，发现是最新版本openssh8.6关闭了公钥登录


### 错误提示

尝试了各种办法，会发现始终提示ssh登录失败

```bash
username@ip: Permission denied (publickey).
```

### 查看ssh详细信息

输出ssh调试信息，查看失败的具体原因

```bash
ssh -Tv username@ip

#发现了一条提示，这就是登录失败的原因
...
debug1: Authentications that can continue: publickey
debug1: Next authentication method: publickey
debug1: Offering public key: /home/xxx/.ssh/id_rsa RSA SHA256:0IxiHWniJDBbsM8x0zx+zliWrl7PINcALzFFqVgKX/M agent
debug1: send_pubkey_test: no mutual signature algorithm
...
```


### 解决办法


```bash
sudo vim ~/.ssh/config

Host *
    HostkeyAlgorithms +ssh-rsa
    PubkeyAcceptedKeyTypes +ssh-rsa
```


### 问题原因

    rsa有安全问题，新版本openssh，默认不支持rsa公钥登录，需要自己打开

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://www.sxjy360.top/page-download/)
[净土大经科注2014-doc](http://www.sxjy360.top/page-download/)
[此生必看的科学实验-水知道答案](http://www.sxjy360.top/page-download/)
[印光大师十念法（胡小林主讲第1集）](http://www.sxjy360.top/page-download/)
