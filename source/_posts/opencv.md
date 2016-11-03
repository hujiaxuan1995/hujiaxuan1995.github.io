title: Opencv+python入门
date: 2015-04-25 15:14:44
tags: opencv python
categories: python
---
最近真是忙啊~~,周六花了一天的时间把Opencv入了个门。
Opencv是一个专门处理数字图像的库，如果是做数字图像处理方面的研究的话，Opencv+Python的组合非常适合。
#安装
首先，进行Opencv的开发时推荐一个网站。[Opencv-Python tutorial](http://opencv-python-tutroals.readthedocs.org/en/latest/py_tutorials/py_setup/py_setup_in_windows/py_setup_in_windows.html#install-opencv-python-in-windows)。在上面可以下载python-2.7x,Numpy,Matplotlib。下面，详细介绍安装步骤：
1.安装Python2.7及numpy. 建议不安装在C盘，个人安装为D:\Python27，然后下载对应的numpy包，numpy对应python2.7，打开后numpy自动安装在python\lib\site-package中，再打开python-IDLE输入import numpy包，检查numpy是否安装成功，若无错误显示，则表明成功。
2.安装opencv-2.4.9
下载opencv-2.4,9，打开点击extract，个人放在D:\Program Files\opencv目录下，然后进入D:\Program Files\opencv/build/python/2.7 folder目录下，复制 cv2.pyd（注意x86，与x64区别） 文件放在D:/Python27/lib/site-packeges中，然后打开python-IDLE,输入
>>> import cv2
>>> print cv2.__version__
如果结果没有输出错误，则证明Opencv安装成功。
3.下载matplotlib对应的python版本，我的是matplotlib-1.4.3.win32-py2.7，然后点击安装，会自动安装在D:/Python27/lib/site-packeges下，不需去设置安装路径。安装好之后，进入python IDLE中，键入<<<import matplot,会出现错误，因为此时没安装six包，安装好six之后，再次键入<<import matplotlib会缺少dateutiil包,此时需要用到setuptools安装包进行安装，输入<<import matplotlib出现缺少pyparsing包提示，下载安装在即可。最后键入<<import matplotlib将不会出错。（关于six的安装可以问度娘，很详细的）。
#入门 
##图片的显示
				import numpy as np
				import cv2
				img = cv2.imread('E:\girl.jpg',0)
				cv2.imshow('jhgf',img)
				k = cv2.waitKey(0)
				if k == 27:
					cv2.destroyAllWindows()
				elif k == ord('s'):
					cv2.imwrite('E:\messigray.png',img)
					cv2.destroyAllWindows()
##视频的播放
				import numpy as np
				import cv2
				cap = cv2.VideoCapture('E:\Wildlife.wmv')
				while(cap.isOpened()):
					cv2.waitKey(25);
					ret, frame = cap.read()
					if frame is None:
						break
					gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
					cv2.imshow('frame',gray)
					if cv2.waitKey(1) & 0xFF == ord('q'):
						break
				cap.release()
				cv2.destroyAllWindows()
cv2.VideoCapture()，cv2.VideoWrite()。与从摄像头中捕获一样，只需要把设备索引号改成视频文件的名字。在播放每一帧时，使用 cv2.waiKey() 设置适当的持续时间。如果设置的太低视频就会播放的非常快，如果设置的太高就会播放的很慢（可以使用这种方法控制视频的播放速度）。通常情况下 25 毫秒就可以了。
