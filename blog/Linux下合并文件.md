Linux下合并文件
==
```
#!/bin/bash

for ((i=0; i<700; i++))
do 
cat $i.ts >> all.ts
done
```
某视频APP下载位置：手机存储/Android/data/com.tencent.qqlive/files/videos_***/

在这个目录下找下载的视频文件(以.hls结束的），找找就好了。