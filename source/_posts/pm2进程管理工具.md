---
title: pm2进程管理工具
comments: true
donate: true
tags: pm2进程
categories: 网站管理
description: 一个针对 Node.js 应用的先进的进程管理工具，提供了很多实用功能，便于开发者和运维人员管理和监控 Node.js 应用。
sticky: 
abbrlink: 
date: 2024-06-13 00:00:00
from:
url:
author:
about:
avatar:
---
PM2 (Process Manager 2) 是一个针对 Node.js 应用的先进的进程管理工具，提供了很多实用功能，便于开发者和运维人员管理和监控 Node.js 应用。以下是 PM2 的一些主要特性和使用介绍：
## 主要特性

### 进程管理：

    启动、停止、重启、删除 Node.js 应用。
    支持应用的负载均衡和多实例部署。

### 监控：

    提供实时的日志、CPU 和内存使用情况。
    支持在线 Web 界面（PM2 Plus/PM2 Enterprise）进行更高级的监控和分析。

### 自动化：

    自动重启崩溃的应用，保证应用的高可用性。
    支持开机自启配置，确保服务器重启后应用自动启动。

### 日志管理：

    提供日志记录和日志轮换功能，方便日志的管理和分析。
    支持将日志文件输出到指定位置。

### 环境配置：

    支持不同环境（如开发、测试、生产）的配置管理。
    通过 JSON 配置文件进行批量应用配置管理。

# 安装 PM2

你可以使用 npm（Node 包管理器）安装 PM2：

```bash
    npm install pm2@latest -g

```

## 常用命令

### 启动应用：

```bash
    pm2 start app.js
```
或使用配置文件启动：

```bash
    pm2 start ecosystem.config.js
```

### 列出所有进程：

```bash
    pm2 list
```

### 查看应用日志：

```bash
    pm2 logs
```

### 停止应用：

```bash
    pm2 stop app_name_or_id
```

### 重启应用：

```bash
    pm2 restart app_name_or_id
```

### 监控应用：

```bash
    pm2 monit
```
    
## 配置文件示例

> 你可以使用 ecosystem.config.js 文件来管理多个应用和不同环境配置。以下是一个示例：

```javascript
    module.exports = {
      apps : [{
        name   : "app",
        script : "./app.js",
        instances : "max",
        exec_mode : "cluster",
        env: {
          NODE_ENV: "development",
        },
        env_production : {
          NODE_ENV: "production"
        }
      }]
    }
```

## 持久化配置（开机自启）

> 你可以通过以下命令将 PM2 配置为开机自启：


```bash
    pm2 startup
    pm2 save
```

## 总结

PM2 是一个功能强大的工具，它不仅能够简化 Node.js 应用的管理和部署，还能够显著提高应用的稳定性和可维护性。无论是在开发环境还是生产环境中，PM2 都是管理 Node.js 应用的得力助手。
