---
layout: post
title: "DriveLink Hub：用 .NET 8 与 WPF 搭建 Windows 车载互联原型"
description: 从双手机生态接入、会话状态机到外部进程管理，复盘一个 Windows 车载娱乐前端的设计与实现。
date: 2026-10-09 10:00:00 +0800
categories: [App开发, Windows, 软件工程]
permalink: /posts/drivelink-hub-windows-automotive-projection/
---

> DriveLink Hub 是我围绕 Windows 桌面 App 与手机互联做的一次工程实践。目前它更适合学习、演示和验证设计思路，还不是经过车规认证的量产车机系统。本文尽量把已经完成的能力、依赖条件和暂时的局限分别讲清楚。

**项目仓库：** [sheeplhy/DriveLink-Hub](https://github.com/sheeplhy/DriveLink-Hub)

## 1. 我想解决什么问题

最初的想法并不复杂：在 Windows 上做一个接近车机使用方式的桌面应用，让 Android 手机和 iPhone 都能找到各自可用的连接入口。

真正开始实现后，我发现难点不只在界面。手机互联还涉及 USB 与局域网、设备授权、外部协议引擎、媒体进程、异步回调以及断开后的资源清理。如果把这些逻辑直接写进窗口点击事件，功能也许能暂时跑起来，但重连、异常退出和后续测试会越来越难处理。

因此，我把项目定位为一个 **Windows 车载娱乐外壳与连接编排器**：WPF 负责界面、状态和操作流程，已经存在的协议工具负责具体的手机连接能力。

## 2. 技术选型与能力边界

应用主体使用 **.NET 8、WPF 和 MVVM**，目标平台为 Windows 10/11 x64。两个手机生态采用不同的接入方式：

| 设备 | 当前实现 | 能提供的体验 | 需要说明的限制 |
| --- | --- | --- | --- |
| Android | Google Android Auto Desktop Head Unit（DHU）+ ADB | Android Auto 车机界面、导航、媒体、语音和兼容应用 | DHU 属于开发测试工具，不代表量产车机认证 |
| iPhone | UxPlay + GStreamer | 局域网内的 AirPlay 音视频接收 | 这是 AirPlay 投屏，不是原生 CarPlay |

这里最重要的取舍，是不把普通镜像描述成原生 CarPlay。原生 CarPlay 涉及 Apple MFi 授权与受控认证链，软件项目无法用自签名证书替代。当前仓库因此选择公开、可验证的 AirPlay 路线，并在界面与文档中明确标注。

## 3. 整体结构：界面与协议解耦

项目没有让 WPF 主窗口直接处理所有连接细节，而是按职责拆分为几层：

```text
WPF Automotive Shell
        │
        ▼
MainViewModel ──► SessionStateMachine
        │                 │
        ├──► Device / Network Discovery
        │
        └──► ProjectionEngineService
                    ├──► Google DHU + ADB
                    └──► UxPlay + GStreamer
```

- `MainWindow.xaml` 主要负责深色大触控界面；
- `MainViewModel` 把连接、停止、刷新设备和导出诊断组织成命令；
- `SessionStateMachine` 管理 Idle、Discovering、Connecting、Streaming、Error 等会话状态；
- `ProjectionEngineService` 查找、启动和停止 DHU、ADB、UxPlay 等外部进程；
- 发现服务读取当前蓝牙设备与网络环境；
- 诊断服务记录有限、脱敏后的运行信息。

这套结构还比较轻量，但至少让 UI、会话规则和第三方协议进程之间有了清晰边界，便于逐步替换或扩展。

## 4. Android Auto：USB 与无线开发路径

Android 侧并不是简单地镜像手机屏幕，而是启动 Google DHU，让地图、媒体、电话等入口由 Android Auto 自己编排。

在 USB 场景中，程序优先检查 ADB 是否存在已授权设备。如果手机已经启动 Head Unit Server，就建立 5277 端口转发，再让 DHU 通过该端口连接：

```text
Android Head Unit Server
        │
        └── ADB forward tcp:5277 ──► DHU
```

如果 ADB 未授权或不可用，程序会尝试 DHU 的直接 USB/AOA 模式。两种方式并存，是为了适应不同手机、驱动和开发设置，而不是假设一条路径能够覆盖所有环境。

无线连接目前采用 Android 开发环境中的 ADB 网络连接与端口转发。它便于观察和排查，但与量产无线 Android Auto 的蓝牙发现、Wi-Fi 切换流程仍有差异，这也是后续需要继续研究的部分。

## 5. iPhone：把 AirPlay 接收放在独立进程中

iPhone 侧由 UxPlay 发布局域网接收服务，并交给 GStreamer 完成媒体解码。WPF 进程负责启动、停止和状态展示，不直接在 UI 线程中搬运媒体数据。

这样处理有两个好处：一是协议或媒体进程异常时，不会直接拖垮主界面；二是可以为子进程单独配置 `PATH`、GStreamer 插件目录和扫描器路径，而不必修改整台电脑的系统环境变量。

这一方案仍然受网络环境影响。同网段可达性、mDNS、Windows 防火墙和公共 Wi-Fi 的客户端隔离，都可能导致 iPhone 无法发现接收端。遇到这类问题时，比起笼统提示“连接失败”，展示网络接口并保留适量诊断信息更有帮助。

## 6. 连接状态中最容易忽略的竞态

项目中一个比较典型的问题是“迟到的回调”：第一次连接还没完成，用户已经点击停止并开始第二次连接；随后第一次连接的成功事件返回，错误地把第二次会话改成 Streaming。

我的处理方式是为每次连接分配递增的 `attempt id`。异步结果必须携带创建它的 id，只有当前会话的事件才能更新状态。简化后的逻辑可以理解为：

```text
开始连接：currentAttempt += 1
收到回调：仅当 callbackAttempt == currentAttempt 时处理
停止连接：让当前 attempt 失效，并清理对应进程
```

这不是复杂的算法，却直接关系到重连是否可靠。项目也针对旧 attempt、停止后的迟到首帧以及进程退出等情况设置了测试，避免 UI 看似正常、内部状态却已经错乱。

## 7. 外部进程、打包与许可

DHU 和 UxPlay 都是独立进程，因此“进程启动成功”并不等于“手机会话已经成功”。应用还需要观察进程退出、接收端准备状态和媒体首帧，并在停止时结束进程树、释放订阅和端口资源。

发布方面，项目可以生成 Windows x64 自包含的 .NET 应用，但完整运行环境不只是一个 EXE：AirPlay 还依赖 UxPlay、GStreamer 插件与 DLL，Android 侧则需要 DHU 和 ADB。仓库提供构建、发布和本地便携包脚本，不过第三方二进制不会直接提交进源码仓库。

这样做一方面控制仓库体积，另一方面也是为了尊重不同组件的分发条款。Google 工具仍受其条款约束，UxPlay 则采用 GPL-3.0，使用和再分发时都需要单独核对。

## 8. 诊断信息也要考虑隐私

设备连接日志很容易包含本机用户名、SSID、IP、MAC 地址、令牌或证书内容。项目目前从两个位置降低泄露风险：

1. 通过 `.gitignore` 排除私钥、配对信息和本地诊断文件；
2. 导出诊断时对密码、令牌、网络标识、PEM 内容和个人 Windows 路径做遮蔽，并限制记录长度。

这部分实现仍需要持续检查，因为“没有主动打印”不等于第三方进程不会输出敏感信息。但至少在设计阶段，就把日志安全当成了功能的一部分，而不是发布前才临时清理。

## 9. 当前完成度与不足

目前项目已经完成 WPF 主界面、Android/iPhone 模式选择、连接生命周期管理、蓝牙与网络信息读取、外部接收器启动、诊断脱敏以及关键竞态测试。

同时，我也不希望把原型写成成熟产品。现阶段仍有这些限制：

- Android Auto 依赖 Google DHU、ADB、手机开发者设置与合适的 Windows 驱动；
- iPhone 只提供 AirPlay 接收，不具备原生 CarPlay 控制能力；
- 真机表现会受手机型号、网络和防火墙环境影响；
- 方向盘按键、音频焦点、麦克风路由、车辆信号和硬件加速渲染尚未接入；
- 自动测试覆盖核心编排逻辑，但不能替代多设备、多网络环境下的真机验证。

## 10. 这次实践带来的认识

DriveLink Hub 对我而言，更有价值的部分不是做出一张“像车机”的界面，而是练习如何把不稳定的设备、网络和第三方进程纳入一个相对可预测的软件结构。

状态机让会话流转更明确，attempt id 隔离旧回调，进程边界降低协议故障对主程序的影响，诊断脱敏减少隐私风险，脚本化构建则让运行环境更容易复现。这些经验也可以迁移到其他桌面 App、设备控制工具和消费电子软件中。

项目仍会继续调整。如果你想查看源码、运行条件或后续更新，可以访问：

**[GitHub：sheeplhy/DriveLink-Hub](https://github.com/sheeplhy/DriveLink-Hub)**

