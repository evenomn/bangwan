# BangwanShell

Go 编写的跨平台 **好看** 的 WebShell 管理工具（GUI 版）。支持 **JSP / PHP** 两种马，通信走加密协议，内置命令执行、文件管理、无文件执行、网络扩展、环境审计、敏感扫描、代码执行、数据库连接、内存马注入等能力。

**单文件二进制，开箱即用**：无需安装任何依赖、无需构建前端，下载对应平台的包直接运行即可。

> ⚠️ 仅供授权的渗透测试、CTF、自建实验环境使用，请勿用于非法用途。

---

## ✨ 好看 —— 界面主题

不将就的 Web 管理面板，内置前端，开箱即用：

- **一键换背景图**：设置 → 上传任意图片（支持 PNG/JPG/GIF/SVG/WebP），立即铺满全屏，无需重启
- **背景透明度**：可调，图片太花也不挡内容
- **多套配色方案**：内置 default / blue / green / purple / red 五套主题色，一键切换
- **细节拉满**：类终端配色、暗色风 UI、响应式布局
- **记忆你的偏好**：配色、背景图、透明度全部持久化（存浏览器 localStorage，换台电脑/浏览器不会丢，回到同一台机器自动恢复）

拿它管理 shell，界面也能赏心悦目。

---

## 快速开始

1. 下载对应平台的二进制（macOS arm64 / Windows amd64），直接运行：
   ```bash
   ./BangwanShell            # 默认监听 127.0.0.1:9000，也可指定端口：./BangwanShell -p 9001
   ```
2. 浏览器打开 `http://127.0.0.1:9000`，点「添加」填入 Shell 信息（URL / 密码 / 类型 / Profile）。
3. 没有新马？用「生成」功能按 URL / 密码 / 类型 / Profile 生成一段 JSP 或 PHP 代码，上传到目标站点，把路径填进 Shell URL 即可。
4. 选中目标 Shell 后就能用终端、文件、插件等模块了。

---

## 功能特性

### 通信协议
- **AES-256-CBC + HMAC-SHA256**（encrypt-then-MAC，先加密后签名）
- 密钥派生：`HMAC-SHA256(password, "bangwan.shell.v2.dk")`
- 线上格式：`IV(16) || MAC(32) || AES-CBC 密文`
- 同一套协议同时用于 PHP / JSP，兼容老版本 PHP（≥5.3，无需 AES-GCM 的 6 参数 `openssl_decrypt`）

### 马类型与 Profile
| 类型 | Profile | 说明 |
|---|---|---|
| JSP | `default` | 表单 `sign` 参数传密文，返回 404 伪装页（密文藏 csrf-token meta） |
| JSP | `json_api` | JSON `{"data": "..."}` 请求/响应，适合反代/API 网关环境 |
| JSP | `multipart` | multipart/form-data 传参，兼容 Tomcat（手动解析 body） |
| PHP | `default` | 标准 PHP 马 |

JSP 载荷基于 JDK 8+ 标准 API，Java 8 ~ 21 均可使用。

### 功能模块

| 模块 | 能力 |
|---|---|
| **终端** | 命令执行，跨平台 EXEC（Windows `cmd /c` / Linux `/bin/sh -c`），自动拼接当前目录 |
| **文件** | 列目录、上传、下载、新建/编辑/删除、远程下载（URL 拉文件到目标机）、盘符切换（Windows） |
| **无文件执行** | Linux 下利用 `/dev/shm` 内存文件系统加载并执行 ELF 二进制，执行完立即清理，无文件落地 |
| **网络扩展** | SOCKS5 代理隧道（走 WebShell 加密通道、无文件，curl/proxychains 直连内网）；反弹 Shell（Java 启动 /bin/sh 或 cmd.exe 并重定向 I/O）；端口扫描（PHP fsockopen / JSP 多线程，支持 `22,80`、`1-1000` 混写）；端口映射（目标机监听端口转发到内网任意 IP:端口） |
| **环境审计** | 一键收集系统信息（OS / 架构 / 用户）、网络接口、环境变量，并检测 Docker / K8s 容器环境 |
| **敏感扫描** | 按目录 + 文件后缀 + 关键词批量扫描配置文件，快速定位 `jdbc:mysql`、`password`、`accessKey`、`secretKey` 等敏感信息 |
| **代码执行** | JSP EVAL：在目标机编译并运行任意 Java 代码；PHP eval |
| **数据库** | JDBC 直连目标可达数据库（MySQL / PostgreSQL / SQLite / SQLServer / Oracle）；PHP DB 执行 |
| **内存马** | 注入 Tomcat Filter 型内存马（无文件落地、重启失效），支持注入 / 状态查询 / 移除 |

---

## 免责声明

本项目仅用于**授权范围内**的安全测试与学习研究。使用者应对其行为负全部责任，请勿对未授权目标使用。
