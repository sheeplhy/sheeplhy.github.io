---
layout: post
title: "从这里开始：我的技术博客"
description: 记录 iOS/Android App、计算机视觉、消费电子与嵌入式软件实践。
date: 2026-10-08 12:00:00 +0800
categories: 随笔
---

欢迎来到我的技术博客。

这里将记录我在**iOS/Android 系统与 App、计算机视觉、消费电子和嵌入式软件**方向的学习和实践，包括：

- 移动端 App、系统体验与相机多媒体链路
- 移动终端相机与 ISP 图像处理链路
- AE、AWB、AF 等 3A 调试方法
- 图像质量与显示质量测试
- Python、OpenCV 与视觉算法实践
- 专业软件工程、测试自动化和问题定位
- 智能硬件与多传感器融合项目

## 为什么建立这个博客

技术实践中有许多值得反复整理的问题：现象如何稳定复现、数据如何支持判断、参数变化为什么影响最终画面，以及软件、算法和硬件之间如何协同。

我希望把这些问题记录下来，让每一次调试和验证都成为可以复用的经验。

```python
import cv2

image = cv2.imread("sample.jpg")
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
cv2.imwrite("sample-gray.jpg", gray)
```

后续会持续更新更多内容。
