---
title: 在Linux上快速搭建好看的Command Line
date: 2025-10-18
categories: Software
tags: [Linux,Blog,Tech,useful tools]
mathjax: true
---

# 在Linux上快速搭建好看的Command Line

> **作者：重庆大学国家卓越工程师学院 Kaiden Poon**
>
> **时间：2025年10月18日**

🛻明明是相同的系统，你的命令行为什么总是简陋无比，指示不清，而别人的却简洁明了视觉友好？本文将通过**Zshell+oh-my-zsh+powerlevel10k**的方式，带你把Linux原装的简陋shell替换成精装Z shell✈️

------

## PART I：什么是Z Shell？

### **1.1  初识Shell**

{% note warning %}Shell是**操作系统中用户与内核之间的“外壳”或界面**，它是一个命令解释器，负责解析用户输入的命令并执行相应的操作。用户可以通过**命令行界面（CLI）或图形用户界面（GUI）**与Shell交互。除了作为交互式命令解释器，Shell还是一种**强大的脚本语言**，可以用来编写脚本自动化任务。{% endnote %}

### **1.2  Shell的主要功能**

- **命令解释：**将用户输入的命令转换成内核能理解的指令
- **进程管理：**创建、终止和控制后台任务
- **输入输出管理：**支持重定向（如“ > ”、“ < ”）和管道（如“ | ”）等功能，以管理数据流
- **脚本执行：**运行.sh文件等Shell脚本，实现自动化操作
- **环境管理：**定义和管理环境变量，如$PATH

### **1.3  Shell的类型**

- **命令行Shell：**提供命令行界面（CLI）的Shell，例如Unix/Linux中的bash、zsh，Windows中的powershell等
- **图形Shell：**提供图形用户界面（GUI）的Shell，例如Windows系统中的Windows Explorer

### **1.4  Z shell（zsh）是什么？**

{% note warning %}Zsh是一个功能强大的**Unix shell**，与Bash类似，但是提供了**更高级的交互功能和更强的定制性**。相比与Bash，Zsh在**自动补全、语法高亮、拼写检查、插件和主题**等方面具有明显的优势，适合追求效率和个性化的用户。{% endnote %}

### **1.5 Oh-My-Zsh是什么？**

{% note warning %}Oh My Zsh是一个**管理Zsh配置的开源框架**，它通过提供大量插件和主题，让原本强大的Zsh命令行体验变得**更易用、更美观、更高效**。它本身不是一个独立的应用程序，而是Zsh的一个**拓展工具集**，可以集成**代码高亮、命令提示、目录索引和多种语言支持**等功能{% endnote %}

### **1.6 powerlevel10k是什么？**

{% note warning %}Powerlevel10k（常简称为p10k）是一款针对Zsh的**快速、高度可定制化的主题**。它能美化并强化你的终端提示符，让你的命令行界面更具吸引力且能显示更多信息{% endnote %}

------

## PART II：在Linux系统安装并配置Zsh

### **2.1 安装zsh**

- **更新软件包**

    ```bash
    sudo apt-get update
    sudo apt-get upgrade
    ```

- **安装zsh**

    ```bash
    sudo apt install zsh
    ```

- **安装好后可以通过以下指令查看**

    ```bash
    car /etc/shells
    ```

- **更换login shell，更换后要重新登录系统才会生效**

    ```bash
    chsh -s $(which zsh)
    ```

    重新进入系统后命令行显示应会出现变化

    

### **2.2 安装oh-my-zsh**

- **访问oh-my-zsh的[GITHUB仓库](https://github.com/ohmyzsh/ohmyzsh)**

    **根据安装指示进行安装（curl、wget或fetch）**

```zsh
sh -c "$(wget -O- https://install.ohmyz.sh/)"
```

仓库克隆成功后显示如下：

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/d8313458fc54fc8a4948f08b34d2a737.png" alt="d8313458fc54fc8a4948f08b34d2a737" style="zoom: 33%;" />



### **2.3 安装powerlevel10k**

- **访问powerlevel10k的[GITHUB仓库](https://github.com/romkatv/powerlevel10k)，跳到Installation项中的Oh My Zsh，使用提供的命令下载**

    ```zsh
    git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git "${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k"
    ```

- **安装后要修改zsh的设定档.zshrc**

    ```zsh
    nano ~/.zshrc
    ```

- **将ZSH_THEME改为powerlevel10k**

    ```shell
    ZSH_THEME="powerlevel10k/powerlevel10k"
    ```

- **启用配置**

    ```zsh
    source ~/.zshrc
    ```

- **运行以下命令，按提示对命令行样式进行设置**

    ```zsh
    p10k configure
    ```

    配置完成后你的命令行将会变成这样！

<img src="https://tprimgs-1335307553.cos.ap-chongqing.myqcloud.com/images/8d20565e34bc174ba5d31523c93b5ae1.png" alt="8d20565e34bc174ba5d31523c93b5ae1" style="zoom: 33%;" />

享受这超棒的人机交互体验吧！