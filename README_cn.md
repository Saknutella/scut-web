# 华南理工大学校园网破解

本文档详细说明了华南理工大学校园网破解系统，概述了系统架构、各组件功能以及部署所需的步骤。

## 系统架构

该系统采用单臂路由模型，并利用 `macvlan` 实现虚拟旁路路由。这种设置可以透明地代理网络流量，无需复杂的路由配置。整个系统使用 Docker 进行容器化，每个服务都在其自己的容器中运行。

## 服务

### 1. scutweb

*   **目的：** 这是负责破解校园网强制门户的核心服务。它通过 HTTP、SOCKS5 和 TProxy 协议提供 IPv4 和 IPv6 互联网访问。
*   **Docker 镜像：** `dnomd343/tproxy:v1.3`
*   **配置：** 该服务通过主机上挂载的 `/etc/scutweb` 目录中的文件进行配置。这包括用于路由、入站和出站的 Xray 配置文件。

### 2. cleardns

*   **目的：** 此服务提供纯净且未经过滤的 DNS 服务。它可以拦截广告、防止 DNS 污染，并可以劫持特定域名。其核心功能使用 AdGuardHome。
*   **Docker 镜像：** `dnomd343/cleardns:v1.2`
*   **配置：** 该服务通过 `/etc/cleardns` 目录中的文件进行配置。这包括主要的 `cleardns.yml` 配置文件和 AdGuardHome 设置。

### 3. route

*   **目的：** 此服务充当内部网络的虚拟路由器。它根据一组规则拦截和过滤客户端流量，并可用于管理客户端访问。
*   **Docker 镜像：** `dnomd343/tproxy:v1.3`
*   **配置：** 路由规则由 `start.sh` 脚本动态生成，并基于 `/etc/route/rules` 目录中的文件。

### 4. freedom

*   **目的：** 此服务提供用于访问外部网站的出站代理。它支持各种加密协议。
*   **Docker 镜像：** `mzz2017/v2raya`
*   **配置：** 该服务通过主机上挂载的 `/etc/freedom` 目录进行配置。

### 5. nginx

*   **目的：** 此服务为整个系统提供基于 Web 的管理界面。它使用反向代理来提供对不同服务的访问。
*   **配置：** Nginx 配置文件位于 `/etc/nginx/conf.d` 目录中。每个服务都有自己的配置文件，用于定义反向代理设置。

## 部署

要部署该系统，您需要一台装有 Docker 的 Linux 发行版机器。以下步骤概述了部署过程：

1.  **克隆代码仓库：**
    ```bash
    git clone <repository-url>
    ```
2.  **配置服务：**
    *   每个服务都有自己的配置目录。您需要编辑每个目录中的配置文件以匹配您的网络设置。
    *   请特别注意每个服务的 `start.sh` 脚本中的 `macvlan` 网络配置。
3.  **启动服务：**
    *   每个服务都可以通过运行其各自目录中的 `start.sh` 脚本来启动。
    *   建议按以下顺序启动服务： `cleardns`、`scutweb`、`route`、`freedom`、`nginx`。

## 使用的软件

以下是该项目中使用的关键软件和技术列表：

*   [Docker](https://docs.docker.com/)
*   [TProxy](https://github.com/dnomd343/TProxy)
*   [ClearDNS](https://github.com/dnomd343/ClearDNS)
*   [Xray](https://xtls.github.io/)
*   [AdGuardHome](https://github.com/AdguardTeam/AdguardHome)
*   [Nginx](https://nginx.org/en/docs/)
*   [v2rayA](https://github.com/v2rayA/v2rayA)
