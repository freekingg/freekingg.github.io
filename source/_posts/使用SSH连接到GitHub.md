---
title: 使用SSH连接到GitHub
date: 2020-04-08 09:25:44
tags:
- ssh
- github
categories:
- 工具
---

## 关于 SSH
使用 SSH 协议可以连接远程服务器和服务并向它们验证。 利用 SSH 密钥可以连接 GitHub，而无需在每次访问时提供用户名或密码。

## 检查现有 SSH 密钥
在生成 SSH 密钥之前，您可以检查是否有任何现有的 SSH 密钥。

- 打开 Git Bash。
- 输入 `ls -al ~/.ssh` 以查看是否存在现有 SSH 密钥：
  ``` shell
  $ ls -al ~/.ssh
  # 列出 .ssh 目录中的文件（如果有）
  ```
- 检查目录列表以查看是否已经有 SSH 公钥。 默认情况下，公钥的文件名是以下之一：
  - id_rsa.pub
  - id_ecdsa.pub
  - id_ed25519.pub

## 生成新 SSH 密钥
检查现有 SSH 密钥后，您可以生成新 SSH 密钥以用于身份验证
如果您还没有 SSH 密钥，则必须生成新 SSH 密钥。 如果您不确定是否已有 SSH 密钥，请检查现有密钥。

**生成新 SSH 密钥**
- 打开 Git Bash或者其它终端命令行工具。
- 粘贴下面的文本（替换为您的 GitHub 电子邮件地址）。
  ``` shell
  ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
  ```
  这将创建以所提供的电子邮件地址为标签的新 SSH 密钥。
  ``` shell
  > Generating public/private rsa key pair.
  ```
- 提示您“Enter a file in which to save the key（输入要保存密钥的文件）”时，按 Enter 键。 这将接受默认文件位置。
   ``` shell
   > Enter a file in which to save the key (/c/Users/you/.ssh/id_rsa):[Press enter]
   ```
- 在提示时输入安全密码(  可以默认回车 )

## 新增 SSH 密钥到 GitHub 帐户
要配置 GitHub 帐户使用新的（或现有）SSH 密钥，您还需要将其添加到 GitHub 帐户。

- 将 SSH 密钥复制到剪贴板。
  在复制密钥时，请勿添加任何新行或空格。
  ``` shell 
  $ clip < ~/.ssh/id_rsa.pub
  # Copies the contents of the id_rsa.pub file to your clipboard
  ```
  > 提示：如果 clip 不可用，可找到隐藏的 .ssh 文件夹，在常用的文本编辑器中打开该文件，并将其复制到剪贴板。
- 在github用户设置侧边栏中，单击 SSH and GPG keys（SSH 和 GPG 密钥）。
- 单击 New SSH key（新 SSH 密钥）或 Add SSH key（添加 SSH 密钥）
- 在 "Title"（标题）字段中，为新密钥添加描述性标签。 例如，如果您使用的是个人 Mac，此密钥名称可能是 "Personal MacBook Air"。
- 将密钥粘贴到 "Key"（密钥）字段。

## 测试 SSH 连接
- 打开 Git Bash或者其它终端命令行工具。
- 输入以下内容
  ``` shell 
  $ ssh -T git@github.com
  # 对 GitHub 尝试 ssh
  ```
  您可能会看到类似如下的警告：
  ```
  > The authenticity of host 'github.com (IP ADDRESS)' can't be established.
  > RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
  > Are you sure you want to continue connecting (yes/no)?
  ```
  或类似如下：
  ```
  > The authenticity of host 'github.com (IP ADDRESS)' can't be established.
  > RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
  > Are you sure you want to continue connecting (yes/no)?
  ```
  验证您看到的消息中的指纹匹配步骤 2 中的消息之一，然后输入 yes：
  ```
  > Hi username! You've successfully authenticated, but GitHub does not
  > provide shell access.
  ```

## 权限被拒绝/验证失败
https://help.github.com/cn/github/authenticating-to-github/error-permission-denied-publickey