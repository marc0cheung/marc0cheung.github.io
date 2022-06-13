---
title: 'Use Git and GitHub on macOS'
date: 2022-06-12
permalink: /doc/2022/06/git-github-macOS-zhHK/
tags:
  - Git
  - GitHub
  - Tutorials
  - macOS
---



**選擇語言**: [English](https://marc0cheung.github.io/doc/2022/06/git-github-macOS/) / 正體中文 / [简体中文](https://marc0cheung.github.io/doc/2022/06/git-github-macOS-zhCN/)

<br>

## Step 1: 登記一個 GitHub 戶口

打開 [GitHub](https://www.github.com/) 登記一個 GitHub 戶口，具體過程不再贅述。

<br>

## Step 2: 在 macOS 上下載并安裝 Git 

在 macOS 上打開 Terminal ，輸入 `git --version` 指令，如果出現了 Git 版本號，則說明已經正確安裝了 Git 。若沒有，則說明需要在 macOS 上安裝 Git 。具體方法如下文所示：

- **方法一：直接透過官網下載 Git 的安裝包進行安裝**

  - 前往 [Git Download for macOS](https://git-scm.com/download/mac) 下載 Git 安裝包
  - 安裝完畢後，在 Terminal 輸入 `git --version` 指令，如果出現了 Git 的版本號，則說明已經正確安裝了 Git 。

- **方法二：使用 Homebrew 安裝**

  - 確保你可連接上互聯網，而不會遭到網絡長城的阻礙，在 Terminal 中輸入如下指令：

  - ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```

  - 如果妳需要通過中國的鏡像源進行下載，請在 Terminal 中使用如下指令：

    ```bash
    /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
    ```

    使用中國國內之鏡像源，下載速度較快，但安全性問題請自己定奪。

  - 安裝 Homebrew 後，在 Terminal 中執行 ：`brew install git` 安裝 Git。

  - 安裝完畢後，在 Terminal 中輸入 `git --version` 指令，如出現 Git 的版本號，則說明已經正確安裝了 Git 。

<br>

## Step 3: 在 macOS 上正確配置 Git

- **配置用戶名與郵箱**

  - ```bash
    git config --global user.name "your_github_username"
    git config --global user.email "your_github_email_address"
    ```

- **生成 SSH 密鑰並復制**

  - ```bash
    ssh-keygen -t rsa -C "yourGitHubEmailAddress@hostname.com"
    ```

  - 執行上述命令後，會提示要求輸入生成路徑和密碼，路徑使用默認即可，默認為 `~/.ssh` 文件夾。

    注意 `.ssh` 文件夾是隱藏文件夾，在 macOS 中查看隱藏文件的方法是，在 Finder 窗口中使用 `Command` + `Shift` + `.` 即可查看當前目錄下的所有隱藏文件與文件夾。

  - 進入 `.ssh` 文件夾，打開 `id_rsa.pub` 文件，將文件包含的所有內容全選後復制。

- **將 SSH 密鑰 添加到 GitHub 賬戶中**

  - 打開並登陸網頁版 GitHub，在個人頭像下選擇 Settings ，選擇左側選單中的 SSH and GPG Keys，進入該菜單後選擇 New SSH Key。
  - Title 可隨意填寫，建議填入使用這一個 SSH Key 的電腦標記，例如 `my-macbookpro-m1` 
  - 將剛剛復制的 `id_rsa.pub` 中的內容黏貼到 Key 下的文本編輯框中，最後選擇 Add SSH Key。

- **驗證 SSH 鏈接**

  - 打開 Terminal ，輸入指令 `ssh -T git@github.com` ，註意後面的 git@github.com 不要更改。
  - 執行指令後，終端會要求妳輸入之前創建 SSH Key 時所使用的密碼，輸入這個密碼後回車。
  - 當終端輸出下圖結果後表示已經鏈接成功。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/ssh_verification_success.png"  width="700px"></div>

<br>

## Step 4: 將本地的文件推到 Repo 中

- 首先在 GitHub 上新建一個 Repo ，或者用自己已有的 Repo 也可以。

- 將這個 Repo 克隆到本地，在 Terminal 中執行如下代碼：

  ```bash
  git clone https://github.com/GithubUsername/Template.git
  ```

- 將新文件放入到 Clone 下來的文件夾中

- 在終端中使用 `git status` 指令，查看文件更改情況。

- ```bash
  git status
  // 使用 . 代錶提交所有文件到倉庫中，也可以將 . 替換為需要提交的文件名
  git add .
  git commit -m "First Commit using macOS"
  git push
  ```

- 在輸入 `git push` 後，Terminal 會讓你輸入用戶名和密碼以進行確認，用戶名對應的是你的 GitHub 賬戶用戶名，但密碼並非是你的 GitHub 登陸密碼，而是一個被稱為 Token 的密鑰，申請方法見下方。

- 輸入了用戶名和 GitHub 賬戶的 Personal Access Token 之後，就可以完成一次 git push 了。

- **申請 Personal Access Token**

  - 從 2021 年開始，GitHub 不再允許用戶直接使用 SSH 鏈接到 GitHub 中，所以還需要去申請一個 Token 給你配置了 Git 的電腦，代替密碼使用。具體流程如下：

    - 在 GitHub 網頁版中，打開 Settings ，進入左側選單最底部的 Developer Settings ，選擇 Personal Access Tokens，選擇 Generate new token 。

    <div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/token_menu.png" width="700px" /></div>

    - 此時，GitHub 會要求你輸入密碼，進入 sudo 模式。
    - 對新 Token 命名，然後勾選權限，選擇到期日，按下 Generate token 按鈕即可得到一個 Token。
    - 這個 Token 就是執行 Git Push 命令後，需要輸入的密碼。
    - 在首次 Commit & Push 時輸入一次後，除非該 Token 過期，否則以後都無需輸入，macOS 會將這個 Token 自動存儲在 Keychain 中。也可以進入 Keychain 對該 Token 進行修改。

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/token_setting.png" width="700px" /></div>