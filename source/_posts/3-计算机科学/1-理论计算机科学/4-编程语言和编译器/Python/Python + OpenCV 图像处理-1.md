---
title: Python + OpenCV 图像处理-1
date: 2017/9/16
categories:
  - 3-计算机科学
  - 1-理论计算机科学
  - 4-编程语言和编译器
  - Python
tags:
  - 图像处理
copyright: true
---

## 1. 读取、写入和展示图片

### 1.1 图像读入：cv2.imread()

使用函数 `cv2.imread()` 读入图像。这幅图像应该在此程序的工作路径，或者给函数提供完整路径，第二个参数是要告诉函数应该如何读取这幅图片。

`cv2.IMREAD_COLOR`：读入一副彩色图像。图像的透明度会被忽略，这是默认参数。

`cv2.IMREAD_GRAYSCALE`：以灰度模式读入图像

PS：调用 `OpenCV`，就算图像的路径是错的，`OpenCV` 也不会提醒你的，但是当你使用命令 `print img` 时得到的结果是 `None`。

代码示例：

```python
# -*- coding: utf-8 -*-
import cv2

# 灰度图模式加载一副彩图
img = cv2.imread('lena.jpg',0) 
```

### 1.2 图像显示：cv2.imshow()

使用函数 `cv2.imshow()` 显示图像。窗口会自动调整为图像大小。第一个参数是窗口的名字，其次才是我们的图像。你可以创建多个窗口，只要你喜欢，但是必须给他们不同的名字。

代码示例：

```python
cv2.imshow('image',img)

cv2.waitKey(0)
"""
cv2.waitKey() 是一个键盘绑定函数。需要指出的是它的时间尺度是毫
秒级。函数等待特定的几毫秒，看是否有键盘输入。特定的几毫秒之内，如果
按下任意键，这个函数会返回按键的ASCII 码值，程序将会继续运行。如果没
有键盘输入，返回值为-1，如果我们设置这个函数的参数为0，那它将会无限
期的等待键盘输入。它也可以被用来检测特定键是否被按下，例如按键a 是否
被按下，这个后面我们会接着讨论。
"""

cv2.destroyAllWindows()
"""
cv2.destroyAllWindows() 可以轻易删除任何我们建立的窗口。如果
你想删除特定的窗口可以使用cv2.destroyWindow()，在括号内输入你想删
除的窗口名。
"""
```

### 1.3 图像保存：cv2.imwrite()

代码示例：

```python
# 图像名 要保存的句柄
cv2.imwrite('lena.png',img)
```

## 2. 调用摄像头拍照

代码示例：

```python
import cv2    # 引入opencv
 
cap = cv2.VideoCapture(0)    # 创建摄像头句柄，打开摄像头
while(1):
    # 读取摄像头资源
    ret, frame = cap.read()
    # 展示读取到的摄像头图像
    cv2.imshow("capture", frame)
    # 判断当键盘输入Q时
    #waitKey()函数的功能是不断刷新图像，频率时间为delay，单位为ms。
    #返回值为当前键盘按键值。
    if cv2.waitKey(1) & 0xFF == ord('q'):
        # 保存当前时刻的照片到指定路径
        cv2.imwrite("/pant/name.jpg", frame)
        break
cap.release()    # 清空摄像头资源
cv2.destroyAllWindows()    # 销毁窗口
```

## 3. 调用摄像头录制视频

代码示例：

```python
# coding:utf-8
import cv2
import sys
 
reload(sys)    # 重新导入sys来设置系统编码.
sys.setdefaultencoding('utf8')
 
cap = cv2.VideoCapture(0)    # 创建一个摄像头句柄，开启摄像头
cap.set(3,640)
cap.set(4,480)
cap.set(1, 10.0)
 
# 第一个参数：视频保存路径
# 第二个参数：视频编码模式，-1表示自己选择
# 第三个参数：镜头快慢
# 第四个参数：视频窗口大小
out = cv2.VideoWriter('/path/output.avi',-1,10,(640,480))
 
# 展示视频窗口
while True:
    # cap.read()返回了一个元组，分别用ret和frame接收
    ret,frame = cap.read()
    if ret == True:
        frame = cv2.flip(frame, 1)
        a = out.write(frame)
        cv2.imshow("frame", frame)
        # 接受键盘输入，若输入q则终止程序
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
    else:
        break
 
# 释放资源，销毁窗口
cap.release()
out.release()
cv2.destroyAllWindows()
```