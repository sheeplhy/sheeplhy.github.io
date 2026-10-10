<p align="center">
  <img src="./assets/images/faylen-liu-profile.jpg" width="118" alt="Faylen Liu 刘飞扬">
</p>

<h1 align="center">Faylen Liu · 刘飞扬</h1>

<p align="center">
  软件工程 · App 开发 · 计算机视觉 · 消费电子 · 嵌入式软件
</p>

<p align="center">
  <a href="https://sheeplhy.github.io/"><strong>个人网站</strong></a>
  ·
  <a href="https://sheeplhy.github.io/blog/"><strong>技术文章</strong></a>
  ·
  <a href="https://sheeplhy.github.io/projects/"><strong>项目实践</strong></a>
  ·
  <a href="mailto:15683337662@163.com"><strong>联系我</strong></a>
</p>

---

## 关于这个仓库

这里是我的个人技术博客与项目作品集源码，使用 **Jekyll + GitHub Pages** 构建。

我主要关注 iOS / Android App 开发、Windows 桌面应用、计算机视觉、消费电子与嵌入式软件。博客用于整理项目设计、开发过程、测试方法与问题复盘，希望把零散的实践沉淀成能够再次使用的工程记录。

> 网站地址：[sheeplhy.github.io](https://sheeplhy.github.io/)

## 技术方向

| 方向 | 关注内容 |
| --- | --- |
| **App 开发** | iOS / Android 应用、Windows 桌面程序、界面交互、系统能力接入与发布迭代 |
| **计算机视觉** | Python、OpenCV、图像处理、数据预处理、识别模型与效果评估 |
| **消费电子** | 智能眼镜、显示设备、Camera / ISP、整机验证与问题定位 |
| **嵌入式软件** | ESP32-S3、传感器融合、软硬件联调与端侧功能原型 |

## 精选项目

### DriveLink Hub

基于 **.NET 8、WPF 与 MVVM** 构建的 Windows 车载互联原型。项目编排 Google Android Auto DHU 与 iPhone AirPlay 接收流程，并围绕连接状态机、外部进程生命周期、异常恢复和诊断脱敏进行工程化整理。

- [查看项目仓库](https://github.com/sheeplhy/DriveLink-Hub)
- [阅读技术文章：DriveLink Hub 的设计与实现](https://sheeplhy.github.io/posts/drivelink-hub-windows-automotive-projection/)

### 其他实践

- [iOS / Android 系统 App 开发实践](https://sheeplhy.github.io/posts/ios-android-app-development/)
- [虹膜识别的数据处理与训练流程](https://sheeplhy.github.io/posts/iris-recognition-pipeline/)
- [OpenCV 轻量化人脸考勤工具](https://sheeplhy.github.io/posts/opencv-face-attendance/)
- [ESP32-S3 多传感器嵌入式实践](https://sheeplhy.github.io/posts/esp32-s3-smart-cane/)
- [智能眼镜 Camera / ISP 调试方法](https://sheeplhy.github.io/posts/rayneo-v4-camera-isp/)

## 仓库结构

```text
_data/       首页项目、方向与经历数据
_includes/   页头、页脚等可复用组件
_layouts/    首页、文章与列表页面模板
_posts/      Markdown 技术文章
assets/      样式、脚本与图片资源
```

<details>
<summary><strong>如何发布一篇新文章</strong></summary>

在 `_posts` 目录中创建 Markdown 文件，文件名格式为：

```text
YYYY-MM-DD-english-title.md
```

文章开头使用 Jekyll Front Matter：

```yaml
---
layout: post
title: "文章标题"
description: 一句话摘要
date: 2026-10-10 12:00:00 +0800
categories: [App开发, 软件工程]
permalink: /posts/english-title/
---
```

提交到 `main` 分支后，GitHub Pages 会自动构建并发布。

</details>

## 联系方式

- GitHub：[@sheeplhy](https://github.com/sheeplhy)
- Email：[15683337662@163.com](mailto:15683337662@163.com)

---

<p align="center">持续学习，认真记录。</p>
