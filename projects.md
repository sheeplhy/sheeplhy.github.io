---
layout: page
title: 项目与实践
description: iOS/Android App、计算机视觉、消费电子与嵌入式软件项目
permalink: /projects/
---

## 专业软件工程实践

以需求分析、模块拆分、代码管理、测试验证和问题闭环为主线组织项目，使用 Git 维护版本，通过 Markdown 持续沉淀设计说明、调试记录和项目记录，并关注后续自动化与 CI/CD 能力建设。

**关键词：** 软件工程、Git、CI/CD、测试验证、工程文档

---

## DriveLink Hub：Windows 车载互联原型

基于 .NET 8、WPF 与 MVVM 构建 Windows 车载娱乐前端，分别编排 Google Android Auto DHU 和 UxPlay/AirPlay 接收流程，并通过状态机、attempt id、独立进程与脱敏诊断处理连接生命周期和异常恢复。

**关键词：** .NET 8、WPF、MVVM、Android Auto、AirPlay、状态机

[阅读技术文章：DriveLink Hub 的设计与实现 →]({{ '/posts/drivelink-hub-windows-automotive-projection/' | relative_url }})

[查看 GitHub 项目仓库 →](https://github.com/sheeplhy/DriveLink-Hub)

---

## iOS / Android 系统 App 开发

围绕移动端需求拆分、界面与功能实现、相机和多媒体能力、系统兼容性、测试与发布流程建立工程实践记录。

**关键词：** iOS、Android、App、系统能力、软件工程

---

## 虹膜分割模型与生物识别几何

基于 4,433 张样本训练 DINOv2 双视图虹膜分割模型，完成 CVAT/Mask 数据协议、训练恢复、EMA 推理、GUI、自动标注和轻量检查点导出。项目同时记录了全脸照片上的漏标与虚标问题，并规划虹膜身份识别、椭圆拟合和标定后瞳距检测路线。

**关键词：** Python、PyTorch、DINOv2、语义分割、虹膜识别、椭圆拟合、瞳距检测

[阅读技术文章：从虹膜分割到识别与瞳距检测 →]({{ '/posts/iris-recognition-pipeline/' | relative_url }})

[查看 GitHub 项目仓库 →](https://github.com/sheeplhy/iris-segmentation-model)

---

## 轻量化人脸考勤工具

使用 Python 与 OpenCV 构建简易人脸考勤工具，模拟相机图像采集流程，对实时画面执行灰度转换、高斯滤波和人脸检测，并将姓名与打卡时间写入本地记录。

**关键词：** Python、OpenCV、Haar 分类器、人脸检测、图像降噪

[阅读技术文章：OpenCV 轻量化人脸考勤工具 →]({{ '/posts/opencv-face-attendance/' | relative_url }})

---

## 高通 ISP 学习与调试实践

从 CHI-CDK 与 Chromatix 工具入手，逐步建立 IFE、BPS、IPE 图像链路认知，并围绕白平衡、色彩校正、Tone Mapping、降噪和时域融合整理可复现的学习方法。

**关键词：** Qualcomm、Camera、ISP、Chromatix、图像质量、调试方法

[阅读技术文章：我的高通 ISP 学习心得 →]({{ '/posts/qualcomm-isp-learning-notes/' | relative_url }})

---

## 雷鸟 V4 Camera 与 ISP 画质调试

围绕雷鸟 V4 智能眼镜 Camera 开展 ISP 影像链路调试与效果验证，覆盖 RAW 域处理、3A、降噪、色彩管理和画质问题定位，并配合研发完成迭代优化。

**关键词：** ISP、AE、AWB、AF、图像质量、参数标定
