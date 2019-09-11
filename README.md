# 最近在树莓派上搞语音识别, 总是碰到错误, 今天总结一下碰到的错误以及解决方法

首先是这两天碰到的问题:
在ubuntu 19.04系统上写好的语音识别程序转到树莓派系统上就运行不成功

本地环境: 树莓派3.0 B+, 一个USB麦克风

我的原因是没配置树莓派的环境,然后我搜了好多其他的问题

我的错误是：OSError: [Errno -9997] Invalid sample rate, 无效的采样率

我google这个问题,然后说要更改open()中的rate采样率，然后我更改为44100Hz，
这个是设备的默认采样率: 
import pyaudio
p = pyaudio.PyAudio()
p.get_default_input_device_info()
这是查看自己麦克风设备的信息，里边包含rate

我更改为44100Hz后，使用pocketsphinx进行识别，识别总是出错
然后我google发现pocketsphinx中的默认采样率是16000Hz，
然后我当时有两种选择:更改pocketsphinx种的默认采样率或者更改pyaudio中open()的采样率
更改pocktsphinx默认采样率还要重新下载/en-us文件
所以我选择更改pyaudio中的open()中的采样率,将rate更改16000Hz
然后又回到最初的那个问题：
OSError: [Errno -9997] Invalid sample rate, 无效的采样率
然后我想找到一种方式能把设备的rate兼容到16000Hz
最后我同事帮我找到这个问题，是最基本的配置问题
首先安装 sudo apt-get install pulseaudio
在 /home/pi 中找到 .asoundrc 改为一下格式
pcm.!default {
    type hw
    card 1
}
ctl.!default {
    type hw
    card 1
}
然后试运行一下，成功运行。
https://blog.csdn.net/u013372900/article/details/80296125