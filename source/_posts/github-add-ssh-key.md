---
title: Github添加SSH KEY
date: 2020-01-30 18:31:51
tags: [github,ssh]
---

对开发人员来说，SSH密钥在很多场景都要用到，而大多数人在不同平台使用同一个密钥对，比如公司代码仓库、公司服务器、个人服务器、甚至多个GitHub账号等等。
个人建议是每个平台使用专门的密钥对，并好好存档管理。

## 1、生成一个GitHub专用的密钥对

比如GitHub用户名为`xyz`

```bash
mkdir -p ~/.ssh; cd ~/.ssh; ssh-keygen -t rsa -f id_github_xyz -C "your email"
```

## 2、设置秘钥对文件权限

```bash
chmod 400 id_github_xyz id_github_xyz.pub
```

## 3、把SSH KEY添加到GitHub管理后台

```
cat id_github_xyz.pub
```

访问[https://github.com/settings/keys](https://github.com/settings/keys)，将上面公钥的内容添加进去。

## 4、配置本地GitHub账号别名

编辑`~/.ssh/config`文件，添加以下内容

```
Host github_xyz
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_github_xyz
```

## 5、使用ssh-agent管理生成的私钥

```bash
ssh-agent bash && ssh-add id_github_xyz
```

## 6、从远程仓库克隆

```bash
git clone github_xyz:xyz/repository-name.git
```