Linux屏录软件
==

### 一、SimpleScreenRecorder
可以记录其他的应用程序和你的屏幕上运行的游戏。这是一个简单但功能强大，功能丰富的屏幕记录软件，包括一个易于使用的界面。
只支持Linux系统，包含在rpmfusion中。

`# yum install simplescreenrecorder`

`# apt install simplescreenrecorder`

其它安装方式：https://www.maartenbaert.be/simplescreenrecorder/

它的一些显著特点包括:

基于QT的简单GUI
可以记录整个屏幕或它的一部分
从OpenGL的应用程序直接记录
良好的视频和音频同步
有助于减少慢速机的视频帧速率
支持暂停和恢复功能
显示了了在记录过程期间的统计
支持录制过程中预览
默认设置已经很好，不需要进行过多设置
### 二、 OBS (Open Broadcaster Software)
OBS 是一个免费、开源和跨平台的视频记录和流媒体应用程序,它可以工作在Linux、Windows和Mac OS X。
Windows需要从官网下载（https://obsproject.com/），Linux系统包含在rpmfusion中。

`＃ yum install obs-studio`

`# apt install ffmpeg`

`# add-apt-repository ppa:obsproject/obs-studio`

`# apt update`

`# apt install obs-studio`

它有几个强大的功能和显著的功能包括:

支持使用H264和AAC编码
支持英特尔QSV和NVENC
支持无限数量的场景和输入源
输出文件使用MP4 或FLV 格式
允许在记录会话中访问网络摄像头，采集卡等
高度可扩展的插件,开发人员可以使用api编写自己的插件