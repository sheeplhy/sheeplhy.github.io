---
layout: page
title: 项目目录
description: 我做过和仍在推进的 App、计算机视觉与嵌入式项目
permalink: /projects/
---

这个页面只放我真正动手做过的项目。我会写清楚现在做到哪一步，也保留没有完成和仍需改进的部分。点击项目即可查看具体实现和复盘。

<nav class="project-directory" aria-label="项目目录">
  <a class="project-entry" href="{{ '/posts/drivelink-hub-windows-automotive-projection/' | relative_url }}">
    <span class="project-entry-index">01</span>
    <span class="project-entry-body">
      <span class="project-entry-meta">2026 · Windows App</span>
      <strong>DriveLink Hub</strong>
      <span class="project-entry-summary">我用 .NET 8、WPF 与 MVVM 搭建了车载互联原型，分别组织 Android Auto DHU 和 AirPlay 接收流程，并处理连接状态、外部进程与异常恢复。</span>
    </span>
    <span class="project-entry-action">查看记录 →</span>
  </a>

  <a class="project-entry" href="{{ '/posts/spark-keeper-flutter-local-agent/' | relative_url }}">
    <span class="project-entry-index">02</span>
    <span class="project-entry-body">
      <span class="project-entry-meta">2026 · Flutter / 本地工具</span>
      <strong>火花管家 Spark Keeper</strong>
      <span class="project-entry-summary">我用 Flutter 做手机端、Python 做本地执行端，尝试把好友与互动计划集中管理。记录中也说明了账号安全边界，以及尚未完成的真实环境验收。</span>
    </span>
    <span class="project-entry-action">查看记录 →</span>
  </a>

  <a class="project-entry" href="{{ '/posts/iris-recognition-pipeline/' | relative_url }}">
    <span class="project-entry-index">03</span>
    <span class="project-entry-body">
      <span class="project-entry-meta">2026 · 计算机视觉</span>
      <strong>第一版虹膜分割模型</strong>
      <span class="project-entry-summary">我从数据整理和训练恢复做起，逐步补上 GUI 推理与自动标注，完成第一版 DINOv2 双视图分割流程，也记录了模型在全脸照片上的不足。</span>
    </span>
    <span class="project-entry-action">查看记录 →</span>
  </a>

  <a class="project-entry" href="{{ '/posts/opencv-face-attendance/' | relative_url }}">
    <span class="project-entry-index">04</span>
    <span class="project-entry-body">
      <span class="project-entry-meta">2026 · Python / OpenCV · 1.2</span>
      <strong>轻量化人脸考勤工具</strong>
      <span class="project-entry-summary">我把最初只能检测人脸的 Demo 重新做成了离线考勤客户端，补上 LBPH 身份识别、员工建档、SQLite 记录、升级备份和异常处理。</span>
    </span>
    <span class="project-entry-action">查看记录 →</span>
  </a>

  <a class="project-entry" href="{{ '/posts/esp32-s3-smart-cane/' | relative_url }}">
    <span class="project-entry-index">05</span>
    <span class="project-entry-body">
      <span class="project-entry-meta">2026 · 物联网 / 嵌入式</span>
      <strong>明杖智联</strong>
      <span class="project-entry-summary">一份未完成的智能辅助杖作品复盘：目前完成基础硬件联通和激光测距灵敏度标定，整机闭环、封装与比赛答辩没有完成。</span>
    </span>
    <span class="project-entry-action">查看记录 →</span>
  </a>
</nav>

其他学习心得、参展随笔和阶段记录，统一放在[项目记录]({{ '/blog/' | relative_url }})中。
