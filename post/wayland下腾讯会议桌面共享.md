 

### Wayland下腾讯会议桌面共享

1. 安装 `v4l2loopback-dkms`

   ```shell
   sudo pacman -Si v4l2loopback-dkms
   ```

2. 加载模块

   ```shell
   sudo modprobe v4l2loopback video_nr=9 card_label=Video-Loopback exclusive_caps=1
   ```

   查看虚拟v4l2设备

   ```shell
   v4l2-ctl --list-devices       
   ```

   ```
   我的输出：
   VirtualVideoDevice (platform:v4l2loopback-000):
   	/dev/video0
   ```

   

3. 使用wayland录屏工具将显示内容写到虚拟设备里，推荐使用 `wl-screenrec-git` 资源占用比 `wf-recorder`低很多。

   ```shell
   wl-screenrec --ffmpeg-muxer v4l2 -f /dev/video0 --output DP-1
   ```

4. 启动一个新的Xwayland， 下面的参数是直接复制过来的，具体含义没细致了解，去掉了全屏参数，添加了适应我显示设备分辨率的选项`-geometry 2560x1440`。

   ```shell
   Xwayland :114 -ac -retro +extension RANDR +extension RENDER +extension GLX +extension XVideo +extension DOUBLE-BUFFER +extension SECURITY +extension DAMAGE +extension X-Resource -extension XINERAMA -xinerama -extension MIT-SHM +extension Composite +extension COMPOSITE -extension XTEST -tst -dpms -s off -geometry 2560x1440
   ```

5. 在xwayland里播放虚拟设备里要分享的屏幕内容

   ```shell
   DISPLAY=:114 ffplay /dev/video0
   ```

6. 在xwayland里打开腾讯会议

   ```shell
   DISPLAY=:114 wemeet-x11
   ```

   腾讯会议里输入法是能使用的，就是位置可能不太正确

7. 剪切板共享

   从xwayland里复制内容:

   ```shell
   DISPLAY=:114 xclip -selection clipboard
   ```

   复制内容到xwayland:

   ```shell
   DISPLAY=:114 echo "your content" | xclip -i -selection clipboard 
   ```

![](https://github.com/xiaohuirong/blog/blob/main/img/wemeet-wayland.png?raw=true)




> 参考链接:
>
> 1. https://blog.taoky.moe/2023-05-22/wemeet-screencast-in-wayland.html
>
> 2. https://wiki.archlinux.org/title/V4l2loopback#Loading_the_kernel_module