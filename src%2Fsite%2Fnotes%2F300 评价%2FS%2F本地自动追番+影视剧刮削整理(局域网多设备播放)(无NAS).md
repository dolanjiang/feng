---
{"dg-publish":true,"permalink":"/300 评价/S/本地自动追番+影视剧刮削整理(局域网多设备播放)(无NAS)/","title":"本地自动追番+影视剧刮削整理(局域网多设备播放)(无NAS)","created":"2024-01-25T18:45:04.000+08:00","updated":"2024-01-25T18:45:04.000+08:00"}
---

# 实现效果（借助工具）：
1. <span style="background:rgba(5, 117, 197, 0.2)">一键订阅番剧</span>（https://mikanime.tv/）（也能补番）
2. <span style="background:rgba(5, 117, 197, 0.2)">自动下载和重命名</span>（AutoBangumi+qBittorrent-Enhanced-Edition）（如果不看番的应该从qBee开始就行了，AutoBangumi主要是追番的）
3. <span style="background:rgba(5, 117, 197, 0.2)">自动刮削和整理</span>（Emby）（升级版付费，但PC和手机都有破解版，只在PC观看无需升级。Jellyfin免费，但这里就不谈了，两者会一个就都会。）
4. <span style="background:rgba(5, 117, 197, 0.2)">一键拉起播放器</span>（电脑播放：Potplayer+油猴插件+Python；其他设备：Emby手机端平板端）（嫌麻烦PC也可以用Emby自己播放）

# 实现要求：
1. 2~8h
2. 一台Windows电脑（mac应该也差不多）
3. 多百度

# 目标人群：
1. 对B站番剧不全、删减、画质不满意，正版受害者等
2. 找盗版网站嫌麻烦，或追求画质和字幕、讨厌广告等
3. 认为手动追更不优美
4. 赛博仓鼠，强迫症，喜欢整理、美化和折腾，当然搭好了之后其实是省时间的
5. 对吸血雷不满，喜欢种子资源或保种等（~~我能说这个刮削也可以刮那种吗~~）
6. <span style="background:#fff88f">其实你会发现没有NAS的话Emby作用确实不大，觉得麻烦的话不妨qBee下载完直接用Potplayer看吧</span>
7. 不想用NAS的（~~我是不想用吗，我那是~~）

# 实现步骤：
## 1. 一键订阅（https://mikanime.tv/）
见[AutoBangumi：自动追番，解放双手](https://www.himiku.com/archives/auto-bangumi.html)中<font color="#ffc000">蜜柑计划</font>部分，最终获得<font color="#9bbb59">RSS链接</font>。

## 2.自动下载和重命名（AutoBangumi+qBittorrent-Enhanced-Edition）
1. AutoBangumi：见[【看番教程】全自动追番！小手一点直接入库！](https://www.bilibili.com/video/BV1gu4y1z75W/?share_source=copy_web&vd_source=4912e4efad2fac6720eb381a701b72f8)中<font color="#ffc000">AutoBangumi</font>部分，可直接用up提供的AutoBangumi版本并完成初始设置。进阶设置见[AutoBangumi：自动追番，解放双手](https://www.himiku.com/archives/auto-bangumi.html)中<font color="#ffc000">环境变量配置</font>部分。连无线网的非NAS用户IP地址一般用路由器地址即可，百度就知道怎么查自己的IP了。这样AB设置好就可以连上设置好的qBee，即两者都设置好才能连上。
2. qBittorrent-Enhanced-Edition：见[【看番教程】机械硬盘超加速、在线多人协同剪辑、几乎无延迟的文件搜索，你可能不知道的好用小软件分享](https://www.bilibili.com/video/BV11X4y1J7N8/?share_source=copy_web&vd_source=4912e4efad2fac6720eb381a701b72f8)中<font color="#ffc000">qBee</font>部分，此时AB中输入刚刚获得的<font color="#9bbb59">RSS链接</font>即会将视频下载到qBee设置好的文件夹。这里推荐后面Emby的媒体库直接设置为这里的文件夹。

## 3.自动刮削（Emby）
1. 见[【看番教程】找不到地方看番？不如自己搭！超详细的媒体服务器搭建教程](https://www.bilibili.com/video/BV1ZP411z7pb/?share_source=copy_web&vd_source=4912e4efad2fac6720eb381a701b72f8)，注意人家是NAS版本，但总体差不多。
2. 特别要补充的是如果刮削不成功，很可能需要修改Host，利用网站如[站长工具 - 站长之家](https://tool.chinaz.com/)寻找tmdb的api（如api.themoviedb.org等）能ping通的ip，加入Host中就不用科学上网了。

## 4.一键播放（Potplayer+油猴插件+Python）
1. Potplayer：[大河李斯特视频工作室-合集·硬核看番教程系列](https://space.bilibili.com/12562485/channel/collectiondetail?sid=1325566)会讲解Potplayer的下载和设置，值得一提的是SVP4 pro对相当一部分动画进行补帧效果确实不错。
2. 油猴插件+Python：见[kjtsune/embyToLocalPlayer: Emby/Jellyfin 调用外部本地播放器，并回传播放记录。适配 Plex。](https://github.com/kjtsune/embyToLocalPlayer)config中播放器改为pot再运行debug选1即可使用。

# 参考：
1. [AutoBangumi：自动追番，解放双手](https://www.himiku.com/archives/auto-bangumi.html)（作者：mikusa）
2. [大河李斯特视频工作室](https://space.bilibili.com/12562485)
3. [kjtsune/embyToLocalPlayer: Emby/Jellyfin 调用外部本地播放器，并回传播放记录。适配 Plex。 (github.com)](https://github.com/kjtsune/embyToLocalPlayer)



