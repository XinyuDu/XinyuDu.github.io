---
title: 'tesseract训练'
date: 2018-2-1
permalink: /posts/2018/2/blog-post-1/
tags:
  - OCR
  - 微信
  - 内网穿透
---

使用Ngrok能实现内网穿透功能。但是免费用户分配的地址不是永久的，大概24小时变化一次。总得想着手动更，想着做一个监测程序，监测到变化后就自动更新。解决思路是定时截屏，然后从截屏图片上利用OCR解读出地址信息。OCR采用python的tesseract库。可是识别准确率比较低，百度了一下可以对引擎进行训练来提高识别率。训练步骤如下：

系统：ubuntu 16.04，python 2.7，tesseract 3.04.01

1. 写一个python程序随机生成100个6位数的地址并存入trainning_text.txt文件。

2. 用命令text2image生成tif文件（其中monaco为字体名子，fonts_dir=是字体所在目录）：

   ```
   text2image --text=training_text.txt --outputbase=eng.monaco.exp0 --font=monaco --fonts_dir=~/orc_trainning
   ```

3. 根据生成的tif文件生成box文件

   ```
   tesseract eng.monaco.exp0.tif eng.monaco.exp0 box.train.stderr
   ```

4. 生成unicharset文件

   ```
   unicharset_extractor eng.monaco.exp0.box
   ```

5. 创建font_properties文件，内容为：

   ```
   monaco 0 0 1 1 0
   ```

6. 生成训练数据相关文件，共三条命令：

   ```
   shapeclustering -F ./font_properties -U unicharset eng.monaco.exp0.tr
   mftraining -F ./font_properties -U unicharset -O eng.unicharset eng.monaco.exp0.tr
   cntraining eng.monaco.exp0.tr
   ```

7. 将生成的四个文件名称加上前缀，四个文件为：shapetable, normproto, inttemp, pffmtable，改名后为：eng.shapetable, eng.normproto, eng.inttemp, eng.pffmtable

8. 合并生成训练数据文件

   ```
   combine_tessdata eng.
   ```

9. 将生成的eng.traineddata改名为monaco.traineddata并拷贝到/usr/local/share/tessdata目录下

10. 最后可以测试一下，截图保存为test.png （识别的文字保存在tesseract-line文件里）

   ```
   tesseract test.png tesseract-line -l monaco
   ```

   ​

