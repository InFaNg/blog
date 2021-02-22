---
title: 使用 Visual Studio Code 的 Remote – WSL 插件进行开发
date: 2020-02-13 16:37:25
tags: [VS Code, WSL]
categories: 技术向
---

**原文写于 2020 年 2 月，内容可能较旧，且官方文档现已提供中文版本。建议参考[这里](https://aka.ms/wsl)**

<!-- more -->

最近学习 C 语言，由于 MSVC 编译器对于 C 标准支持非常之差，又不想为了在笔记本上安装 Linux 系统来折腾一大堆的驱动问题，于是打算使用 Windows Subsystem for Linux (WSL)，用 Linux 下的 GCC 编译器进行编译。前几天尝试了一下 VSCode 下的 Remote – WSL 插件，配合 WSL 来使用可以说是非常方便

## 必要的准备工作
要想使用 Visual Studio Code 的 Remote – WSL 插件进行开发，首先需要确保已经做好以下准备工作：
1. 已经在 Windows 下安装了 WSL。完整的教程可以在微软官方的这篇文章：[适用于 Linux 的 Windows 子系统安装指南 (Windows 10)](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)
2. 在 Windows 下安装了 Visual Studio Code 编辑器（注意：不是在 WSL 系统安装，而是直接在 Windows 系统下）
3. 在 Visual Studio Code 上安装 [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) 这一扩展。

## 配置开发环境
1. 打开 WSL 命令行（可以直接点击开始菜单中的 WSL 系统图标，也可以在 cmd 中输入 `wsl`）
2. 在命令行中打开开发目录，例如我的开发目录是 `D:\wsl_workspace`，则在命令行中输入 `cd \mnt\d\wsl_workspace` 以跳转到开发目录。在 WSL 中，硬盘统一挂载在`\mnt\` 下，即 `\mnt\` 后的目录名是硬盘盘符，例如打开 C 盘可使用命令 `cd \mnt\c`
3. 在命令行中输入 `code .`，此时，Visual Studio Code 会拷贝在 WSL 下需要用到的文件。整个过程可能会需要一段时间，不过一般不会很长
4. 一段时间之后，会自动打开一个新的 VS Code 窗口，你会看到一个 VS Code 运行在 WSL 所对应的工作目录的提示

    ![wsl-starting-notification](/images/wsl-starting-notification.png)

    此时，VS Code 仍在自动配置运行在 WSL 中的相关环境

5. VS Code 自动配置结束后，左下角会出现一个提示运行在 WSL 开发环境中的小标记。接下来只需在 WSL 中配置相关的开发环境如安装 GCC 后，就可以和往常一样进行相应的开发

    ![wsl-statusbar-indicator](/images/wsl-statusbar-indicator.png)

至此，相关的开发环境配置已全部完成。你可以像往常一样进行编剧文件、调试等各种操作，只是现在这些操作都是在 WSL 下进行

如果想从 WSL 环境中重新回到 Windows 开发环境，只需在 VS Code 中按下`F1` 键，然后输入命令 `Remote-WSL: Reopen Folder in Windows` 即可

![reopen-folder-in-Windows](/images/reopen-folder-in-windows.png)

同理，如果想从 Windows 开发环境下跳转到 WSL 环境，也只需在 VS Code 中按下`F1` 键，然后输入命令 `Remote-WSL: Reopen Folder in WSL`

## 解决 Git 出现大量文件改动的问题
在 Windows中，行尾是两个个字符 `\r\n`，而在 Linux 中，行尾只有一个字符 `\n`。由于两者对于行尾的处理方式不同，使用 Git 同步时 Git 可能会检测到大量的文件改动。不过我们可以通过禁用行尾转换来修复这一问题

我们可以在工作目录下创建一个名为 `.gitattributes` 的文件，文件中内容如下：
```
* text=auto eol=lf
*.{cmd,[cC][mM][dD]} text eol=crlf
*.{bat,[bB][aA][tT]} text eol=crlf
```

## 关于扩展
配置完 WSL 开发环境后，VS Code 的扩展会被安装在本地端或是 WSL 端。一般来说，只有少数扩展如主题之类的会被安装在本地端，剩下的大多数都需要安装在 WSL 端。因此，我们还需对 VS Code 中的扩展进行相关操作

之前大量安装在本地的扩展会被切换到 "Disabled" 状态，需要我们将其安装在 WSL 端才可继续使用原有扩展

![wsl-disabled-extensions](/images/wsl-disabled-extensions.png)

不过好在我们只需点击 `Install on WSL` 即可一键将原有扩展安装在 WSL 端

## 在 VS Code 中打开 WSL 终端
如果VS Code中打开了一个 WSL 下的文件，那么在 VS Code 中打开的终端都会自动在 WSL 下运行而不是在 Windows 下的 CMD 或是 PowerShell运行，很是方便

## 在 WSL 下进行调试
在 WSL 下进行程序的调试也无需进行额外配置，VS Code 会自动根据 `launch.json` 对 WSL 下运行的程序进行相关的调试

## 资料来源
本文大多数内容根据官方文档翻译而成，所参考的官方文档如下：
* [Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
* [Developing in the Windows Subsystem for Linux with Visual Studio Code](https://code.visualstudio.com/docs/remote/wsl)
* [Supporting Remote Development and Visual Studio Online](https://code.visualstudio.com/api/advanced-topics/remote-extensions)