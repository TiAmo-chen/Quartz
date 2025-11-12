---
title: "Batch脚本常用命令参考"
description: "Windows Batch脚本编程中常用命令的详细说明和使用示例，包括文件操作、系统管理、网络命令等。"
permalink: "/batch-commands-reference"
tags: [Batch脚本, Windows命令行, 批处理, 系统管理, 脚本编程]
aliases: [Batch脚本, Windows命令行, 批处理, 系统管理, 脚本编程]
created: "2025-11-12"
modified: "2025-11-12"
published: "2025-11-12"
publish: false
draft: false
enableToc: true
lang: "zh-CN"
---


| **命令**        | **用途**                                                         | **示例**                                               |
|-----------------|------------------------------------------------------------------|--------------------------------------------------------|
| `echo`          | 显示一段文字或控制脚本的输出                                       | `echo Hello, World!`                                   |
| `@echo off`     | 关闭命令行回显（不显示命令行执行的内容）                           | `@echo off`                                            |
| `pause`         | 暂停脚本执行，并等待用户按任意键继续                               | `pause`                                                |
| `cls`           | 清除命令行窗口中的屏幕内容                                         | `cls`                                                  |
| `cd` / `chdir`  | 改变当前工作目录                                                 | `cd C:\Users\YourName`                                 |
| `dir`           | 显示目录列表（文件和文件夹）                                       | `dir`                                                  |
| `copy`          | 复制文件                                                           | `copy source_file destination_file`                    |
| `xcopy`         | 复制文件和目录树（比 `copy` 更强大）                               | `xcopy C:\Source\* C:\Destination\ /s /e`              |
| `del` / `erase` | 删除文件                                                           | `del filename.txt`                                     |
| `rd` / `rmdir`  | 删除空目录                                                         | `rmdir C:\MyFolder`                                    |
| `del /f /q`     | 强制删除文件，不显示确认信息                                       | `del /f /q filename.txt`                               |
| `mkdir` / `md`   | 创建新目录                                                         | `mkdir C:\NewFolder`                                   |
| `ren` / `rename` | 重命名文件或目录                                                   | `ren oldname.txt newname.txt`                          |
| `move`          | 移动文件或目录                                                     | `move filename.txt D:\NewFolder\`                      |
| `set`           | 设置环境变量                                                       | `set varname=value`                                    |
| `if`            | 条件判断，常用于控制流程                                           | `if exist "file.txt" echo File exists!`                |
| `for`           | 循环语句                                                           | `for %%i in (1, 2, 3) do echo %%i`                    |
| `timeout`       | 等待一段时间后继续执行                                             | `timeout /t 5`                                         |
| `start`         | 启动一个程序或打开一个新的命令窗口                                 | `start notepad.exe`                                    |
| `taskkill`      | 终止运行中的进程                                                   | `taskkill /f /im processname.exe`                      |
| `ipconfig`      | 显示网络配置（IP 地址、子网掩码等）                               | `ipconfig`                                             |
| `ping`          | 检查网络连接                                                       | `ping 127.0.0.1`                                       |
| `netstat`       | 查看网络连接和端口使用情况                                         | `netstat -an`                                          |
| `net`           | 管理网络相关的设置（如用户账户、共享、服务等）                   | `net user`                                             |
| `assoc`         | 显示或更改文件扩展名与程序的关联                                   | `assoc .txt=txtfile`                                   |
| `shutdown`      | 关闭或重启计算机                                                   | `shutdown /s /f /t 0`  (立即关机) <br> `shutdown /r /f /t 0` (立即重启) |
| `call`          | 调用另一个批处理文件                                               | `call another_script.bat`                              |
| `echo.`         | 输出空行                                                           | `echo.`                                                |
| `choice`        | 从用户输入的选项中进行选择                                         | `choice /c y/n /m "Do you want to continue?"`          |
| `setlocal`      | 开始本地化变量（作用域仅限当前批处理文件）                         | `setlocal`                                             |
| `endlocal`      | 结束本地化变量作用域                                               | `endlocal`                                             |
