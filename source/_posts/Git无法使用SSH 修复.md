---
title: Git无法使用SSH bug修复
date: 2024-01-25
file-created: 2024 01 25
last-modified: 2024 01 25
---
<link rel="stylesheet" href="/css/admonition.css">

偶然发现我不能使用SSH从github上拉取代码
报错信息


``` txt
ssh: connect to host github.com port 22: Connection timed out fatal: Could not read from remote repository.

Please make sure you have the correct access rights and the repository exists.
```


我们可以执行如下命令
```shell
$ ssh -v git@github.com
```

```txt
OpenSSH_for_Windows_8.6p1, LibreSSL 3.4.3
debug1: Authenticator provider $SSH_SK_PROVIDER did not resolve; disabling
debug1: Connecting to github.com [20.205.243.166] port 22.
```

我们仔细看一下会发现  `github.com[20.205.243.166]` 后面这个ip地址根本不是github的ip地址，它被篡改了。
所以我们到hosts文件夹中添加一条github的记录就能够成功访问了，但是还是需要SSH的密钥的。
生成密钥的方式网上都有，不再赘述。

```txt
    debug1: Trying private key: C:\\Users\\HD/.ssh/id_rsa
    debug1: Trying private key: C:\\Users\\HD/.ssh/id_dsa
    debug1: Trying private key: C:\\Users\\HD/.ssh/id_ecdsa
    debug1: Trying private key: C:\\Users\\HD/.ssh/id_ecdsa_sk
    debug1: Trying private key: C:\\Users\\HD/.ssh/id_ed25519
    debug1: Trying private key: C:\\Users\\HD/.ssh/id_ed25519_sk
    debug1: Trying private key: C:\\Users\\HD/.ssh/id_xmss
    debug1: No more authentication methods to try.
    git@github.com: Permission denied (publickey).
```

最后的不允许访问是因为我没有添加密钥。
Ubantu 22.04 如果也出现这种情况，可以做相同处理

!!! info 其他情况
    try another shell.


