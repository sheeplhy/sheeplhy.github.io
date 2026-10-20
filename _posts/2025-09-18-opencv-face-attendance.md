---
layout: post
title: "我用 Python 与 OpenCV 做了一个轻量化人脸考勤工具"
description: "从摄像头取帧、Haar 人脸检测到 Tkinter 交互和本地打卡记录，我完成了一次小型计算机视觉应用实践。"
date: 2026-10-19 09:00:00 +0800
categories: [计算机视觉, Python, OpenCV, 桌面应用]
permalink: /posts/opencv-face-attendance/
---

为了把 OpenCV 的图像处理流程和桌面界面开发串联起来，我做了一个轻量化人脸考勤工具。程序能够读取电脑摄像头画面，在当前帧中检测正面人脸，并在我输入姓名、点击“打卡”后，把姓名和时间追加写入本地记录文件。

我需要先说明它的能力边界：**这个版本实现的是人脸检测，不是人脸身份识别。** 姓名由我手动输入，Haar 分类器只能判断画面里是否存在类似正面人脸的区域，不能证明镜头前的人就是输入框里的姓名，也没有活体检测。因此它适合作为个人学习和流程原型，不应该直接用于真实单位的正式考勤。

## 我想练习的不只是一个检测框

如果只调用一次 OpenCV 的检测函数，代码很快就能跑起来。但我更希望把它做成一个完整的小程序，所以给自己设置了几项具体目标：

- 实时读取摄像头，并允许在设备不可用时降级到测试图片或提示画面；
- 对每一帧执行灰度转换和轻量降噪，再进行人脸检测；
- 将 OpenCV 画面持续显示在 Tkinter 窗口中，同时保持按钮可以正常响应；
- 在点击打卡时检查姓名、画面和人脸状态，避免直接写入无效记录；
- 将结果保存为简单、可查看的本地文本，完成从视觉输入到业务记录的最小闭环。

最终的数据流可以概括为：

```text
摄像头 / 测试图片
        ↓
OpenCV 逐帧读取
        ↓
灰度转换 + 高斯模糊
        ↓
Haar 正面人脸检测
        ↓
画框并显示到 Tkinter
        ↓
输入姓名 → 点击打卡 → 写入姓名和时间
```

## 技术栈与程序结构

我使用 Python 完成整体逻辑，主要依赖 OpenCV、Tkinter、Pillow 和 NumPy：

| 组件 | 我在项目中的用途 |
| --- | --- |
| OpenCV | 摄像头读取、灰度转换、高斯模糊、Haar 人脸检测和标注框绘制 |
| Tkinter / ttk | 搭建视频区域、姓名输入框、打卡按钮和状态提示 |
| Pillow | 把 OpenCV 的 NumPy 图像转换为 Tkinter 可显示的对象 |
| NumPy | 表示图像数组，并生成视频源不可用时的兜底画面 |
| 本地文本文件 | 按行保存姓名和打卡时间 |

代码集中在一个 `main.py` 中，但我仍然按职责拆成了视频源初始化、图像预处理、人脸检测、画面标注、记录写入和界面类几个部分。这样做让我在排查问题时可以先确认是哪一层出现异常，而不是把所有逻辑都塞进一个循环。

## 视频源：先保证程序能够启动

我先尝试打开摄像头 0，再尝试摄像头 1。如果都不可用，就读取本地测试图片；如果连测试图片也不存在，程序会生成一张深灰色提示画面。

```python
def init_video_source():
    for idx in (0, 1):
        cap = cv2.VideoCapture(idx)
        if cap.isOpened():
            return cap, "摄像头 #%d" % idx

    cap = cv2.VideoCapture("test.jpg")
    if cap.isOpened():
        return cap, "模拟源：test.jpg"

    return None, None
```

这个三级降级设计并不会让检测变得更准确，但它提升了程序的可调试性：即使换到没有摄像头的环境，我仍然可以打开界面，继续检查布局、状态提示和记录逻辑。

## 图像预处理与 Haar 人脸检测

每一帧进入检测器前，我先把 BGR 彩色图像转换为灰度图，再做一次 5×5 高斯模糊。

```python
def preprocess(frame):
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    return cv2.GaussianBlur(gray, (5, 5), 0)
```

Haar 特征主要利用区域亮度差进行判断，灰度图已经能够提供需要的信息；高斯模糊可以轻微抑制噪点，减少细碎纹理对检测的干扰。随后我调用 OpenCV 自带的正面人脸级联分类器：

```python
def detect_faces(gray):
    return face_cascade.detectMultiScale(
        gray,
        scaleFactor=1.1,
        minNeighbors=5,
        minSize=(80, 80),
    )
```

我把 `scaleFactor` 设置为 1.1，在检测速度和尺度搜索细度之间取一个简单平衡；`minNeighbors=5` 用于减少过于松散的候选框；`minSize=(80, 80)` 则过滤画面中过小的区域。这些参数只适合当前练习环境，并不是通用最优值。换摄像头、距离、光照或角度后，都需要重新测试漏检和误检情况。

<figure>
  <img src="{{ '/assets/images/face-attendance/01-face-detected.png' | relative_url }}" alt="轻量化人脸考勤工具检测到一张人脸的界面">
  <figcaption>检测阶段：程序读取摄像头 #0，在当前帧中标出一张正面人脸，此时尚未写入打卡记录。</figcaption>
</figure>

## Tkinter 中持续显示视频

这个项目里让我印象最深的并不是检测器，而是如何让 Tkinter 在刷新视频时仍然响应按钮。直接使用 `while True` 会占住主线程，窗口容易卡死，所以我使用 `root.after()` 让界面事件循环每隔约 30 毫秒调度一次更新函数。

```python
def _update_loop(self):
    frame = self._grab_frame()
    gray = preprocess(frame)
    faces = detect_faces(gray)

    self.latest_frame = frame
    self.latest_faces = faces

    annotated = annotate(frame, faces)
    rgb = cv2.cvtColor(annotated, cv2.COLOR_BGR2RGB)
    self._photo = ImageTk.PhotoImage(Image.fromarray(rgb))
    self.video_label.configure(image=self._photo)

    self.root.after(30, self._update_loop)
```

OpenCV 使用 BGR 通道顺序，而 Pillow 和 Tkinter 需要 RGB，所以显示前要进行颜色转换。我还把 `PhotoImage` 保存到 `self._photo`，避免局部变量在函数结束后被回收，导致界面中的画面消失。

检测结果同样被缓存为 `self.latest_faces`。点击打卡时，我直接检查最新一帧的状态，不再重复运行一次检测。

## 打卡校验与本地记录

我给打卡按钮设置了三层基础校验：没有输入姓名时拒绝写入；画面尚未就绪时提示等待；当前帧没有检测到人脸时判定打卡失败。全部通过后，程序才把记录追加到文本文件。

```python
def on_checkin(self):
    name = self.name_entry.get().strip()

    if not name:
        self._set_status("请先输入姓名", "orange")
        return
    if self.latest_frame is None:
        self._set_status("画面未就绪，请稍候", "orange")
        return
    if len(self.latest_faces) == 0:
        self._set_status("未检测到人脸，打卡失败", "red")
        return

    line = write_record(name)
    self._set_status("打卡成功！%s" % line, "green")
```

写入时使用追加模式，每条记录由姓名和当前时间组成：

```python
def write_record(name):
    timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    line = "%s,%s" % (name, timestamp)
    with open("attendance_records.txt", "a", encoding="utf-8") as f:
        f.write(line + "\n")
    return line
```

<figure>
  <img src="{{ '/assets/images/face-attendance/02-checkin-success.png' | relative_url }}" alt="输入姓名并成功写入打卡时间的界面">
  <figcaption>打卡阶段：姓名、人脸状态和画面状态通过检查后，程序在右侧显示写入成功的时间。</figcaption>
</figure>

文本追加实现简单，也便于直接打开检查，但它没有数据库的查询、去重、权限和并发能力。因此我把它看作原型阶段的数据出口，不是正式考勤系统的存储方案。

## 我在调试中遇到的几个问题

### 1. NumPy 数组不能直接判断真假

`detectMultiScale()` 检测到人脸时可能返回数组。我最初如果写成 `if not faces`，就可能遇到数组真值不明确的问题。后来统一使用 `len(faces) == 0` 判断检测结果是否为空。

### 2. 界面刷新不能阻塞事件循环

实时视频很容易让人自然地写出无限循环，但 GUI 主线程还需要处理按钮、关闭窗口和重绘事件。使用 `after()` 后，视频更新和用户交互才能在同一个事件循环中协调运行。

### 3. 摄像头成功打开不代表后续每帧都成功

设备可能在运行中被占用或断开，所以每次 `read()` 都需要检查返回状态。读取失败时，我让程序回到兜底画面，而不是继续传递空帧。

### 4. 检测到人脸不等于识别出身份

这是这个项目最重要的边界。绿色框只能证明分类器在当前画面找到了一块人脸候选区域，不能证明姓名真实，也不能抵御照片、视频回放或他人代填姓名。

## 隐私、安全与实际使用限制

人脸属于敏感生物特征信息。虽然这个原型没有保存人脸照片和特征向量，但摄像头画面、姓名和考勤时间仍然涉及个人信息。如果继续开发，我需要在采集前取得明确同意，说明用途、保存期限和删除方式，并尽量减少不必要的数据留存。

当前版本还存在以下限制：

- 没有身份识别，任何人都可以输入其他姓名；
- 没有活体检测，照片或屏幕回放可能通过人脸检测；
- Haar 检测对侧脸、遮挡、逆光和较远距离的人脸适应性有限；
- 文本记录没有加密、权限控制、去重和防篡改能力；
- 没有对误检率、漏检率、延迟和不同人群表现做系统评估；
- 界面和处理都在主线程，复杂模型加入后可能影响交互流畅度。

因此，我不会把这个程序用于真实考勤决策，也不会把一次成功截图当作准确率证明。

## 如果继续迭代

下一步我会优先补齐工程闭环，而不是只替换一个更大的模型：

1. 加入同一姓名在固定时间内的重复打卡限制；
2. 使用 SQLite 管理记录，并提供查询、导出和删除功能；
3. 将视频采集与推理移到工作线程，避免阻塞界面；
4. 对摄像头失效、文件写入失败和模型加载异常给出明确提示；
5. 在获得用户授权的前提下，再评估身份特征注册、比对和活体检测；
6. 建立包含不同光照、角度和遮挡条件的测试集，记录误检、漏检和响应时间；
7. 增加数据加密、访问权限、留存期限与删除机制。

这个小项目让我第一次把图像处理、事件驱动 GUI 和本地数据记录串成了一个可以交互的应用。它的算法并不复杂，但我从中更清楚地理解了摄像头输入、图像格式转换、检测结果缓存、界面调度和异常兜底之间的关系。更重要的是，我也认识到：做出一个能够检测人脸的界面，只是开始；要成为真正可信的考勤系统，还需要身份验证、安全、隐私、评估和长期维护。

---

## 图片与内容说明

- 两张界面截图来自我的本地项目运行过程，仅用于技术实践展示。
- 截图中的姓名和画面由我本人提供；文章没有公开其他人员的考勤记录。
- 本文介绍的是个人练习原型，不构成真实考勤、人事管理或身份认证方案。

---

## 后续版本

这个项目后来继续更新到了 1.2。我把身份识别、SQLite 数据管理、升级备份和实际使用中的问题单独写成了新文章：

[查看《轻量化人脸考勤 1.2：我把最初的检测 Demo 重新做了一遍》]({{ '/posts/face-attendance-1-2/' | relative_url }})
