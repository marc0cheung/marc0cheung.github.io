---
title: 'Use Git and GitHub on macOS'
date: 2022-06-12
permalink: /doc/2022/06/git-github-macOS/
tags:
  - Git
  - GitHub
  - Tutorials
  - macOS
---



**Select Language**: English / [正體中文](https://marc0cheung.github.io/doc/2022/06/git-github-macOS-zhHK/) / [简体中文](https://marc0cheung.github.io/doc/2022/06/git-github-macOS-zhCN/)

<br>

## Step 1: Sign up for a GitHub account

Open [GitHub](https://www.github.com/) and Sign up an GitHub Account, well, you know how to do it.

<br>

## Step 2: Download and Install Git to your macOS

Open Terminal on your macOS，Enter `git --version` , if git version pops up, then your Mac is properly installed with Git. However, if git version doesn't pop up, you need to install Git as the following shows. 

- **Method A: Use Git Installer to install Git**

  - Go to [Git Download for macOS](https://git-scm.com/download/mac) and download the installer of Git
  - After installation, use `git --version` in Terminal, if git version pops up, then your Mac is properly installed with Git.

- **Method B: Use Homebrew to install Git**

  - Ensure that you can connect to the Internet without being blocked by the Great Firewall, enter the following command in Terminal.

  - ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```

  - If you need to download from Gitee mirror in China, use the following command in Terminal.

    ```bash
    /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
    ```

    When you are in China, use a Chinese domestic mirror for faster download speeds, but please consider carefully for yourself about information security issues.

  - After installing Homebrew, run `brew install git` in Terminal to install Git.

  - Once installed, type the command `git --version` into Terminal and if the version number of Git appears, then Git has been installed correctly.

<br>

## Step 3: Configuring Git on macOS

- **Configure username and email of Git**

  - ```bash
    git config --global user.name "your_github_username"
    git config --global user.email "your_github_email_address"
    ```

- **Generate SSH Key and Copy the Public Key**

  - ```bash
    ssh-keygen -t rsa -C "yourGitHubEmailAddress@hostname.com"
    ```

  - After executing the above command, you will be prompted for the generated path and password, just use the default path, which is `~/.ssh` folder.

    Note that the `.ssh` folder is a hidden folder and the way to view hidden files in macOS is to use `Command` + `Shift` + `. ` shortcut to see all hidden files and folders in the current directory.

  - Go to the `.ssh` folder, open the `id_rsa.pub` file with Text Edit, select all of its contents and copy it.

- **Add SSH key to your GitHub account**

  - Open and login to the web version of GitHub, select Settings under your avatar, select SSH and GPG Keys from the menu on the left, and select New SSH Key when you get to that menu.
  - Title is optional, it is recommended to fill in the tag of the computer using this SSH Key, e.g. `my-macbookpro-m1`. 
  - Paste the contents of the `id_rsa.pub` you just copied into the text edit box under "Key" and press Add SSH Key button.

- **Verify SSH Connection**

  - Open Terminal and run the command `ssh -T git@github.com`, be careful **NOT** to change the git@github.com at the end of the command.
  - After executing the command, the terminal will ask you to enter the password you used to create the SSH Key, enter this password and press enter.
  - When the terminal outputs the following result, it means that the connection has been established successfully.

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/ssh_verification_success.png"  width="700px"></div>

<br>

## Step 4: _Git Push_ local files to your Repo

- Start by creating a new repo on GitHub, or you can use your own repo.

- To clone this Repo locally, execute the following code on the Terminal.

  ```bash
  git clone https://github.com/GithubUsername/Template.git
  ```

- Place the new files in the folder created after cloning

- Use the `git status` command in the terminal to check the status of changes to your files.

- ```bash
  git status
  // Use "." for committing all files to the repository, or you can replace "." with the name of the file to be committed
  git add .
  git commit -m "First Commit using macOS"
  git push
  ```

- After executing `git push`, Terminal will ask you to enter your username and password for confirmation. The username is your GitHub account username, but the password is not your GitHub login password, but a key called a Personal Access Token, which you can apply on your GitHub personal settings following the instruction below.

- After Enter your Personal Access Token of your GitHub Account, a git push is completed.

- **Generate Personal Access Token**

  - Starting in Dec 2021, GitHub will no longer allow users to link directly to GitHub using SSH, so you will need to request a Personal Access Token for your each computer linked to your github account instead of a password.

    - In the GitHub web version, open Settings, go to Developer Settings at the bottom of the left-hand menu, select Personal Access Tokens, and choose Generate new token.

    <div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/token_menu.png" width="700px" /></div>

    - At this point, GitHub will ask you to enter your password to enter sudo mode.
    - Name the new Token, tick the permissions, select the expiry date and press the Generate token button to get a Token.
    - This token is the password that you need to enter when you run the Git Push command.
    - Once entered this Token once during the first Commit & Push, the Token will **not** need to be entered again unless it expires. macOS will automatically store the Token in the Keychain. The Token can also be modified by accessing the Keychain.

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/token_setting.png" width="700px" /></div>

