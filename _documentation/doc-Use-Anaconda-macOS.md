---
title: 'Install, Configure and Use Anaconda on macOS'
date: 2022-07-08
permalink: /doc/2022/07/anaconda-macOS/
tags:
  - anaconda
  - Python
  - Tutorials
  - macOS
---



**Select Language**: English / [正體中文](https://marc0cheung.github.io/doc/2022/07/anaconda-macOS-zhHK/) / [简体中文](https://marc0cheung.github.io/doc/2022/07/anaconda-macOS-zhCN/)

<br>

## Step 1: Download Anaconda Installation

Open [Anaconda Distribution](https://www.anaconda.com/products/distribution) and download Anaconda Distribution for macOS. Run the `.pkg` file and install Anaconda to your Mac properly by following the steps on your screen.

When you see "Thank you for installing Anaconda!", the installation is done.

Run:
```bash
$ source ~/.bash_profile
```
in Terminal to activate Anaconda.

<br>

## Step 2: Configure Anaconda in Anaconda-Navigator

Open Anaconda-Navigator and Click "Environments", you can create new Python environments and manage the packages under the environment here.

<br>

## Step 3: How to Use Anaconda in Terminal

**Commands that are often used**

- Check Anaconda Version: `conda --v`
- Update Anaconda: `conda update conda`
- Activate Conda Envs: `conda activate env_name` or `source activate env_name`
- Deactivate Conda Envs: `conda deactivate`
- Check Installed Libs: `conda list` 
- Create New Python Env: `conda create --name myenv`
- Create Certain Python Version Env: `conda create -n myenv python=3.7`
- Clone Env: `conda create --name myclone --clone myenv`
- Delete Env: `conda remove --name myenv --all`
- Check all the envs: `conda info -e` or `conda env list` or `conda info --envs`
- 

<div align="center"><img src="https://github.com/marc0cheung/marc0cheung.github.io/raw/main/_documentation/doc-Use-Git-and-GitHub-macOS-zhCN.assets/ssh_verification_success.png"  width="700px"></div>

<br>

