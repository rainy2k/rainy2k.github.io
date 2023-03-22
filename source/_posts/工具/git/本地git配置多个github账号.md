---
title: 本地git配置多个github账号
date: 2023-03-20 14:02:38
tags: 
categories:
- develop
- git
---

## 生成ssh key

```shell
ssh-keygen -t rsa -C “account-1@gmail.com”
```
假设账号account1生成的密钥保存在\~/.ssh/id_rsa和\~/.ssh/id_rsa.pub

```shell
ssh-keygen -t rsa -C “account-2@gmail.com”
```
假设账号account2生成的密钥保存在\~/.ssh/id_rsa_another和\~/.ssh/id_rsa_another.pub


## 编辑config
\~/.ssh/config
```shell
Host account1
  HostName github.com
  IdentityFile ~/.ssh/id_rsa
  port 22

Host account2
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_another
  port 22
```

## 测试
```shell
ssh -T git@account1
ssh -T git@account2
```

## 用户名设置
```shell
git config --global --unset user.name
git config --global --unset user.mail
```

在仓库本地设置:
```shell
git config --local user.name xxxx
git config --local user.mail xxxx
```

## 远程连接

```shell
# 原本的方式:
git remote add origin https://github.com/<github usernmae>/<your repository name>.git
# 现在使用config中的别名:
git remote add origin git@account1:<github usernmae>/<your repository name>.git
```

## 生效ssh密钥

```shell
ssh-add ~/.ssh/id_rsa
ssh-add ~/.ssh/id_rsa_another
```

## Link

https://blog.csdn.net/cheng5055251/article/details/127544657