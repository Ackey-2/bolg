---
title: docker初体验
publishDate: 2025-02-27
description: 'docker初体验'
tags:
  - docker
heroImage: { src: './thumbnail.jpg', color: '#64574D' }
language: 'Chinese'
---

# docker使用教程

在 Windows 上使用 Docker 涉及一些特定的配置和步骤。下面是详细的教程，包括如何安装 Docker Desktop、常用指令及示例。

## 一，安装 WSL2

在微软商店搜索WSL2，选择对应的ubuntu版本安装即可。

### WSL迁移（避免C盘爆满）

1. 查看wsl下的Linux是否为关闭状态，当wsl为Stopped才能进行下一步。

   ```bash
   wsl -l -v
   ```

2. 导出系统镜像

   ```bash
   wsl --export Ubuntu D:\UbuntuWSL\ubuntu.tar
   ```

3. 注销原有的linux系统

   ```bash
   wsl --unregister Ubuntu
   ```

4. 导入系统

   ```bash
   wsl --import <导入的Linux名称> <导入盘的路径> <ubuntu.tar的路径> --version 2 (代表wsl2)
   ```

5. 修改默认用户

   

在 Windows 上使用 Docker 涉及一些特定的配置和步骤。下面是详细的教程，包括如何安装 Docker Desktop、常用指令及示例。

## 一、安装 Docker Desktop for Windows

### 1.1 **下载 Docker Desktop**
- 访问 Docker 官方网站：[Docker Desktop 下载页面](https://www.docker.com/products/docker-desktop)。
- 选择适合 Windows 版本（Windows 10 或更高版本）的安装包进行下载。

### 1.2 **安装 Docker Desktop**
- 运行下载的安装包，按照提示完成安装。
- 在安装过程中，Docker 会要求启用 Hyper-V 和容器功能，确保在 Windows 上启用了这些功能（通常安装程序会自动启用）。

### 1.3 **启动 Docker Desktop**
- 安装完成后，启动 Docker Desktop 应用程序。
- 启动后，系统托盘（屏幕右下角）会显示 Docker 的图标，表示 Docker 已经成功启动。
  
### 1.4 **检查 Docker 是否成功安装**
- 打开命令行（PowerShell 或 CMD），输入以下命令：
    ```bash
    docker --version
    ```
    如果安装成功，应该会看到类似如下的输出：
    ```
    Docker version 20.10.x, build xxxxxxx
    ```

## 二、配置 Docker 环境

Docker Desktop 默认使用 WSL 2（Windows Subsystem for Linux 2）作为虚拟化引擎。如果你的系统没有启用 WSL 2，Docker Desktop 会提示你安装。

### 2.1 **启用 WSL 2**
- 打开 PowerShell 以管理员身份运行，执行以下命令启用 WSL：
    ```bash
    wsl --install
    ```
- 选择 Linux 发行版，通常推荐选择 Ubuntu。

### 2.2 **配置 Docker Desktop 使用 WSL 2**
- 打开 Docker Desktop，进入设置（Settings）。
- 在 "General" 页面中，确保 "Use the WSL 2 based engine" 选项被选中。
- 在 "Resources" 下，可以选择限制 Docker 的资源（CPU、内存、磁盘等），根据你的机器性能进行配置。

### 2.3 **确保 Docker 与 WSL 2 配合使用**
- 在 WSL 2 下安装 Docker 时，Docker 会在 Ubuntu 等 WSL 环境中运行容器。
- 如果有多个 WSL 发行版，可以在 Docker Desktop 设置中的 "Resources > WSL Integration" 中启用对某些发行版的支持。

## 三、Docker 基本操作

### 3.1 **常用 Docker 命令**

#### 3.1.1 **查看 Docker 版本**
- 查看 Docker 版本信息：
    ```bash
    docker --version
    ```

#### 3.1.2 **拉取镜像**
- 拉取官方的 Docker 镜像。例如，拉取最新版本的 Ubuntu 镜像：
    ```bash
    docker pull ubuntu:latest
    ```

#### 3.1.3 **列出所有镜像**
- 列出本地的所有 Docker 镜像：
    ```bash
    docker images
    ```
    输出示例：
    ```
    REPOSITORY          TAG       IMAGE ID       CREATED         SIZE
    ubuntu              latest    d3b16d8bc2f8   2 weeks ago     72.9MB
    ```

#### 3.1.4 **进入容器**
- 进入一个基于 Ubuntu 镜像的容器：
    ```bash
    docker exec -it <docker name> /bin/bash
    ```
    这个命令会启动一个交互式终端（`-it`），进入容器内部，使用的是最新的 Ubuntu 镜像。

#### 3.1.5 **查看运行中的容器**
- 查看当前运行的容器：
    ```bash
    docker ps
    ```
    如果你想查看所有容器（包括停止的），可以加上 `-a` 参数：
    ```bash
    docker ps -a
    ```

#### 3.1.6 **停止容器**
- 停止一个容器，假设容器的 ID 是 `abcdef123456`：
    ```bash
    docker stop abcdef123456
    ```

#### 3.1.7 **启动已停止的容器**
- 启动一个已停止的容器：
    ```bash
    docker start abcdef123456
    ```

#### 3.1.8 **删除容器**
- 删除一个容器，假设容器的 ID 是 `abcdef123456`：
    ```bash
    docker rm abcdef123456
    ```
- 要强制删除正在运行的容器，可以加上 `-f` 参数：
    ```bash
    docker rm -f abcdef123456
    ```

#### 3.1.9 **删除镜像**
- 删除镜像，假设镜像的 ID 是 `d3b16d8bc2f8`：
    ```bash
    docker rmi d3b16d8bc2f8
    ```
- 如果镜像正在被容器使用，可以加上 `-f` 强制删除：
    ```bash
    docker rmi -f d3b16d8bc2f8
    ```

#### 3.1.10 **查看容器日志**
- 查看某个容器的日志：
    ```bash
    docker logs <container_id>
    ```
