---
title: 'Use Git and GitHub on macOS'
date: 2022-06-12
permalink: /doc/2022/06/git-github-macOS-zhCN/
tags:
  - Git
  - GitHub
  - Tutorials
  - macOS
---



**选择语言**: [English](https://marc0cheung.github.io/doc/2022/06/git-github-macOS/) / [正體中文](https://marc0cheung.github.io/doc/2022/06/git-github-macOS-zhHK/) / 简体中文

<br>

## Step 1: 注册一个 GitHub 账户

打开 [GitHub](https://www.github.com/) 注册一个 GitHub 账户，具体过程不再赘述。

<br>

## Step 2: 在 macOS 上下载并安装 Git

在 macOS 上打开 Terminal ，输入 `git --version` 指令，如果出现了 Git 的版本号，则说明已经正确安装了 Git 。若没有，则说明需要在 macOS 上安装 Git 。具体方法如下文所示：

- **方法一：直接通过官网下载 Git 的安装包进行安装**

  - 前往 [Git Download for macOS](https://git-scm.com/download/mac) 下载 Git 安装包
  - 安装完毕后，在 Terminal 输入 `git --version` 指令，如果出现了 Git 的版本号，则说明已经正确安装了 Git 。

- **方法二：使用 Homebrew 安装**

  - 确保你可连接上互联网，而不会遭到网络长城的阻碍，在 Terminal 中输入如下指令：

  - ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```

  - 如果你需要通过中国的镜像源进行下载，请在 Terminal 中使用如下指令：

    ```bash
    /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
    ```

    使用国内源，下载速度较快，但安全性问题请自己定夺。

  - 安装 Homebrew 后，在 Terminal 中执行 ：`brew install git` 安装 Git。

  - 安装完毕后，在 Terminal 中输入 `git --version` 指令，如果出现了 Git 的版本号，则说明已经正确安装了 Git 。

<br>

## Step 3: 在 macOS 上正确配置 Git

- **配置用户名与邮箱**

  - ```bash
    git config --global user.name "your_github_username"
    git config --global user.email "your_github_email_address"
    ```

- **生成 SSH 密钥并复制**

  - ```bash
    ssh-keygen -t rsa -C "yourGitHubEmailAddress@hostname.com"
    ```

  - 执行上述命令后，会提示要求输入生成路径和密码，路径使用默认的即可，默认为 `~/.ssh` 文件夹。

    注意 `.ssh` 文件夹是隐藏文件夹，在 macOS 中查看隐藏文件的方法是，在 Finder 窗口中使用 `Command` + `Shift` + `.` 即可查看当前目录下的所有隐藏文件与文件夹。

  - 进入 `.ssh` 文件夹，打开 `id_rsa.pub` 文件，将里面的内容全选后复制。

- **将 SSH 密钥 添加到 GitHub 账户中**

  - 打开并登陆网页版 GitHub，在个人头像下选择 Settings ，选择左侧选单中的 SSH and GPG Keys，进入该菜单后选择 New SSH Key。
  -  Title 可随意填写，建议填入使用这一个 SSH Key 的电脑标记，例如 `my-macbookpro-m1` 
  - 将刚刚复制的 `id_rsa.pub` 中的内容粘贴到 Key 下的文本编辑框中，最后选择 Add SSH Key。

- **验证 SSH 链接**

  - 打开 Terminal ，输入指令 `ssh -T git@github.com` ，注意后面的 git@github.com 不要更改。
  - 执行指令后，终端会要求你输入之前创建 SSH Key 时所使用的密码，输入这个密码后回车。
  - 当终端输出下图结果后表示已经链接成功。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/ssh_verification_success.png"  width="700px"></div>

<br>

## Step 4: 将本地的文件推到 Repo 中

- 首先在 GitHub 上新建一个 Repo ，或者用自己已有的 Repo 也可以。

- 将这个 Repo 克隆到本地，在 Terminal 中执行如下代码：

  ```bash
  git clone https://github.com/GithubUsername/Template.git
  ```

- 将新文件放入到 Clone 下来的文件夹中

- 在终端中使用 `git status` 指令，查看文件更改情况。

- ```bash
  git status
  // 使用 . 代表提交所有文件到仓库中，也可以将 . 替换为需要提交的文件名
  git add .
  git commit -m "First Commit using macOS"
  git push
  ```

- 在输入 `git push` 后，Terminal 会让你输入用户名和密码以进行确认，用户名对应的是你的 GitHub 账户用户名，但密码并不是你的 GitHub 登陆密码，而是一个被称为 Token 的密钥，申请方法见下方。

- 输入了用户名和 GitHub 账户的 Personal Access Token 之后，就可以完成一次 git push 了。

- **申请 Personal Access Token**

  - 从 2021 年开始，GitHub 不再允许用户直接使用 SSH 链接到 GitHub 中，所以还需要去申请一个 Token 给你配置了 Git 的电脑，代替密码使用。具体流程如下：

    - 在 GitHub 网页版中，打开 Settings ，进入左侧选单最底部的 Developer Settings ，选择 Personal Access Tokens，选择 Generate new token 。

    <div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/token_menu.png" width="700px" /></div>

    - 此时，GitHub 会要求你输入密码，进入 sudo 模式。
    - 对新 Token 命名，然后勾选权限，选择到期日，按下 Generate token 按钮即可得到一个 Token。
    - 这个 Token 就是执行 Git Push 命令后，需要输入的密码。
    - 在首次 Commit & Push 时输入一次后，除非该 Token 过期，否则以后都无需输入，macOS 会将这个 Token 自动存储在 Keychain 中。也可以进入 Keychain 对该 Token 进行修改。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/token_setting.png" width="700px" /></div>