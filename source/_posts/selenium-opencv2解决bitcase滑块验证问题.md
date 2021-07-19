---
layout: post
title: Selenium+OpenCV2解决bitcase滑块验证问题
date: 2021-07-19 11:45:39
index_img: https://gitee.com/pitcoft/blogimg/raw/master/img/20210610.jpg
banner_img: https://gitee.com/pitcoft/blogimg/raw/master/img/bda8e32d19707e8ac881c6a8ee9cd733.jpg
categories:
- Web
tags:
- Selenium
- OpenCV
copyright: true
---

### 问题描述

网站login页面：https://bitcash.jp/members/tri?act=login

今天在处理这个网站登录页面的时候，发现其有滑块验证码，要进行自动化滑块验证才能进行下一步的操作。

![image-20210719121218638](https://gitee.com/pitcoft/blogimg/raw/master/img/image-20210719121218638.png)

![](https://gitee.com/pitcoft/blogimg/raw/master/img/codepic.jpg)

验证码整体包含在`div`盒子的`img`里，滑块部分和背景部分共用一张图片，所以打算用`opencv`去识别滑块缺口，用`selenium`模拟拖拽。

### 识别部分

尝试使用`get_attribute("src")`直接把图片下载下来，发现下载下来的图片和网页上的不一致。

验证码服务商为防止他人破解，同样的url在访问时候会回传不一样的图片，改用selenium进行截图全图，并截取相应的验证码部分。

截取之后的效果：

![image-20210719124152331](https://gitee.com/pitcoft/blogimg/raw/master/img/image-20210719124152331.png)

通过HSV高低阈值，提取图像的白色区域，利用`inRange()`函数可实现二值化功能，

`inRange(InputArray src, InputArray lowerb, InputArray upperb, OutputArray dst)`

参数介绍：src：输入图像； lowerb：要提取颜色在hsv色彩空间取值的低值；upperb：要提取颜色在hsv色彩空间取值的高值；输出图

hsv色彩取值表：

![img](https://gitee.com/pitcoft/blogimg/raw/master/img/1322697-20180830095425840-318024232.png)

这里取得是白色的区域，效果如下：

![image-20210719131403193](https://gitee.com/pitcoft/blogimg/raw/master/img/image-20210719131403193.png)

再寻找白色的像素点坐标，白色像素值是255`np.where(dst==255)`

去除不属于滑块的其他噪声，取白色像素点y坐标均值，最后返回一个偏移值供selenium进行拖拽移动。

具体实现：

```python
import cv2
import numpy as np
import os

def get_xy(filename):
    imga = cv2.imread(filename)
    img = imga[500:585, 745:1155]
    cv2.imshow('img',img)
    hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)  # 色彩空间转换为hsv，分离.

    # 色相（H）是色彩的基本属性，就是平常所说的颜色名称，如红色、黄色等。
    # 饱和度（S）是指色彩的纯度，越高色彩越纯，低则逐渐变灰，取0-100%的数值。
    # 明度（V），取0-100%。
    # OpenCV中H,S,V范围是0-180,0-255,0-255
    low = np.array([0, 0, 221])
    high = np.array([180, 30, 255])

    dst = cv2.inRange(src=hsv, lowerb=low, upperb=high)  # HSV高低阈值，提取图像部分区域

    # 寻找白色的像素点坐标。
    # 白色像素值是255，所以np.where(dst==255)
    xy = np.column_stack(np.where(dst == 255))
    avglist = []
    for i in xy:
        if i[0] > 10 and i[1] > 10:
            avglist.append(i[1])
    num = (380-(sum(avglist) / len(avglist))) * -1
    # os.remove(filename)
    return int(num)
```

### 验证码拖拽

其实到了拖拽部分没什么好说的了，首先是全屏Chrome截图返回给opencv进行裁剪识别。

其次利用selenium定位到具体的滑块位置，单击并保持`click_and_hold()`进行拖拽。

根据之前识别来的偏移量进行移动，当然偏移量是相对总体来说的，具体使用的时候还得去调试。

因为发现定位元素时，滑块和背景是共用一个img标签的，所以使用绝对坐标来定位。

拖拽到指定位置后，释放鼠标就完成了。

具体实现：

```python
from selenium import webdriver
from selenium.webdriver.common.action_chains import ActionChains
import time
import getxy_hsv


def run():
    dr = webdriver.Chrome()
    dr.get('https://bitcash.jp/members/tri?act=mypage&suppressWarn=true&ref=header&sv=2&_lh')
    dr.maximize_window()
    time.sleep(0.5)
    dr.get_screenshot_as_file("./1.png")
    xy = getxy_hsv.get_xy("./1.png")
    print(xy)

    time.sleep(0.1)

    ActionChains(dr).move_by_offset(1120, 540).click_and_hold().perform()
    time.sleep(0.5)
    ActionChains(dr).move_by_offset(xy, -5).click().perform()
    # 释放鼠标
    ActionChains(dr).release().perform()
    dr.quit()


if __name__ == '__main__':
    run()
```

### 验证效果



![录制_2021_07_19_13_31_23_113](https://gitee.com/pitcoft/blogimg/raw/master/img/%E5%BD%95%E5%88%B6_2021_07_19_13_31_23_113.gif)