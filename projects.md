---
layout: page
title: 项目与实践
description: iOS/Android App、计算机视觉、消费电子与嵌入式软件项目
permalink: /projects/
---

## 专业软件工程实践

以需求分析、模块拆分、代码管理、测试验证和问题闭环为主线组织项目，使用 Git 维护版本，通过 Markdown 持续沉淀设计说明、调试记录和技术文章，并关注后续自动化与 CI/CD 能力建设。

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

[阅读技术文章：iOS / Android 系统 App 开发实践 →]({{ '/posts/ios-android-app-development/' | relative_url }})

---

## 虹膜识别模型训练

基于 Python 深度学习框架开展虹膜识别模型训练，完成图像数据采集、清洗、预处理和数据增强，构建训练数据集并进行模型调参与效果评估。

**关键词：** Python、深度学习、图像预处理、数据增强、模型评估

[阅读技术文章：虹膜识别的数据处理与训练流程 →]({{ '/posts/iris-recognition-pipeline/' | relative_url }})

---

## 轻量化人脸考勤工具

使用 Python 与 OpenCV 构建简易人脸考勤工具，模拟相机图像采集流程，对实时画面执行灰度转换、高斯滤波和人脸检测，并将姓名与打卡时间写入本地记录。

**关键词：** Python、OpenCV、Haar 分类器、人脸检测、图像降噪

[阅读技术文章：OpenCV 轻量化人脸考勤工具 →]({{ '/posts/opencv-face-attendance/' | relative_url }})

---

## ESP32-S3 智能导盲杖

基于 ESP32-S3-CAM 集成摄像头、MPU6050、超声波、GPS 和离线语音模块，实现跌倒检测、障碍物分级预警、路线偏离提醒与多模态反馈。

**关键词：** ESP32-S3、嵌入式、计算机视觉、传感器融合、I2C

[阅读技术文章：ESP32-S3 多传感器嵌入式实践 →]({{ '/posts/esp32-s3-smart-cane/' | relative_url }})

---

## 雷鸟 V4 Camera 与 ISP 画质调试

围绕雷鸟 V4 智能眼镜 Camera 开展 ISP 影像链路调试与效果验证，覆盖 RAW 域处理、3A、降噪、色彩管理和画质问题定位，并配合研发完成迭代优化。

**关键词：** ISP、AE、AWB、AF、图像质量、参数标定

[阅读技术文章：智能眼镜 Camera / ISP 调试方法 →]({{ '/posts/rayneo-v4-camera-isp/' | relative_url }})
