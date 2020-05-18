![2l449y](FRDS.png)

# FRDS小组介绍

## MNHD和MUHD

## cXcY

## YuMI

# 压制软件

> 所有提到软件，都可以在组内共享获取，如果发生跟新不及时问题，请自行下载。

- **[x265](https://www.videohelp.com/software/x265-Encoder)**:压制必备软件，请保持最新版本及时更新。
- **[Mediainfo](https://mediaarea.net/en/MediaInfo/Download/Windows)**：媒体信息检查软件，查看片源以及压制信息使用。
- **[MKVToolNix](https://www.fosshub.com/MKVToolNix.html)**：mkv混流软件，也能实现以下基本的视频分割。
- **[SubtitleEdit](https://github.com/SubtitleEdit/subtitleedit/releases)**：字幕编辑软，可以实现ORC等功能。
- **[eac3to](https://www.videohelp.com/software/eac3to)** :音轨转码编辑软件，也可实现Demux等功能。
- **[BDinfo](https://www.videohelp.com/software/BDInfo)**:蓝光原盘信息检测分析软件。
- **[VapourSynth64Portable]()**：本站主要使用的压制处理程序，此版本内置x265以及等本站编写脚本工具严禁外泄

其他选择

- [AviSynth](http://www.videohelp.com/tools/Avisynth)：脚本

# 快速查询

## x265命令行参数

#### **非HDR：**

```
-D 10 --preset veryslower --high-tier --ctu 32 --rd 4 --subme 7 --ref 6 --pmode --no-rect --no-amp --no-rskip --tu-intra-depth 4 --tu-inter-depth 4 --range limited --no-open-gop --no-sao --rc-lookahead 100 --no-cutree --bframes 16 --vbv-bufsize 160000 --vbv-maxrate 160000 --colorprim bt709 --transfer bt709 --colormatrix bt709 --deblock -3:-3 --ipratio 1.3 --pbratio 1.2 --qcomp 0.6 --aq-mode 1 --aq-strength 1.0 --psy-rd 1.00 --psy-rdoq 1.00
```

#### **HDR：**

```
-D 10 --preset veryslower --level-idc 5.1 --high-tier --ref 6 --bframes 16 --rd 4 --me star --subme 5 --merange 57 --ipratio 1.3 --pbratio 1.2 --aq-mode 4 --aq-strength 1.00 --qcomp 0.60 --psy-rd 1.25 --psy-rdoq 1.00 --ctu 32 --rc-lookahead 60 --deblock -3:-3 --cbqpoffs 0 --crqpoffs 0 --qg-size 8 --range limited --no-frame-dup --selective-sao 0 --no-cutree --tu-intra-depth 4 --tu-inter-depth 4 --no-rskip --no-tskip --no-early-skip --no-rect --no-amp --no-sao --aud --repeat-headers --hrd --hdr-opt --colorprim bt2020 --colormatrix bt2020nc --transfer smpte2084 --master-display "G(13250,34500)B(7500,3000)R(34000,16000)WP(15635,16450)L(40000000,50)" --max-cll=577,512 --chromaloc 2 --pmode
```

## **1080p x265压制：**

##### 文件命名：

> 英文名字.年份.版本信息.BluRay.1080p.MNHD-FRDS.mkv

例如：The.Hobbit.an.Unexpected.Journey.2012.Directors.Cut.BluRay.1080p.MNHD-FRDS.mkv

##### 变形/输出结果：

- 变形：禁止
- 输出最大值：1920 x 1080

##### 参考帧数：

- ref 6

##### 主音轨以及国语配音：

###### 	**源为无损音轨**

​		—提取核心core

​		—转换为AC3、E-AC3或者QAAC

​			源7.1音轨，转换为E-AC3可以保留7.1声道，也可选择ac3 640

​			源5.1音轨，推荐ac3 640，可以转为E-AC3

​			源双声道 ，推荐QAAC V127

​		—音乐会类型允许保留原音

​		—国语配音

​			推荐QAAC V127，国语音轨在Title必须写明对应版本信息

###### 	**源为有损音轨**

​		直接添加，禁止任何转码

##### 评论音轨：

​	—QAAC V 63

​	评论音轨必须进行Title命名

​		—Commentary with/by Position Name Surname

##### **Movie Title**规定：

> [IMDB名字] [年份] 1080p BluRay - FRDS

# 详细指南：

##### 1.Demux分离

如果是从原盘开始压制，推荐进行将原盘进行分离之后进行压制。可以提取软件有很多，推荐eac3to进行分离，不过eac3to对于从原盘分离sup字幕会有bug，当提取sup字幕出现问题推荐使用---。

关于eac3to分离软件本文推荐GUI软件，可以结合音轨处理同时进行。

##### 2.音轨处理

###### 	(1).eac3to分离与音频转码

> 本教程推荐使用GUI方式，推荐全部的eac3to工具将打包提供。

![image-20200111155246930](UsEac3To.png)

通过GUI可以实现**ac3**多种码率转换，**qacc**格式制作。

###### 	(2).E-AC-3制作教程

略

##### 3.vpy脚本文件

###### 	(0).导入核心

```python
import vapoursynth as vs	
core = vs.get_core()		#导入vapoursynth核心代码
core.max_cache_size = 16384	#VS最大使用内存设置,单位MB
#-------------------------#
#        其它函数代码       #                	
#-------------------------#
clip.set_output()
```

###### 	(1).源的解码

​	**原盘BD处理**：

原盘BD可以直接进行导入，需要使用BDinfo确定对应的playlist。

```python
mpls = core.mpls.Read(string bd_path, int playlist)	#读取BD对应playlist的对应文件，playlist为MPLS对应的完整数字
clip = core.std.Splice([core.ffms2.Source(mpls['clip'][i]) for i in range(mpls['count'])])	#拼接多个视频片段
```

​	**Remux** :

Remux或者提取成mkv怎比较简单，直接导入就可，VS editor支持拖入功能。

```python
 clip=core.ffms2.Source(source='文件路径')
```

###### 	(2).Crop切割

Crop在编辑器内预览具体数值进行切割，切割到最外边缘，数值只能为偶数，但出现奇数保留在脏边里进行处理。

```python
clip=core.std.Crop(clip,int left,int right, int top, int bottom)
```

###### 	(3).修复脏线，脏边

脏边修复根据实际选用不同修复方式，根据**saiclabs**写到：

> About FixC(R)Br, bb(mod), Fixer:
> \- FixC(R)Br changes only the brightness of the line
> \- bb(mod) changes the brightness of the line but it could change the data if blur value is low
> \- Fixer (EdgeFixer) uses least squares regression (similar to interpolation) and changes/creates data
>
> If FixC(R)Br gives good result it's recommended using it since the original data is preserved.
> If bb(mod) gives good result with blur >20 it's preferable using it.
> If FixC(R)Br doesn't give good result and using protected value doesn't help too, and bb(mod) with blur >20 doesn't help too it's preferable using Fixer.
> If bb(mod) with blur >20 doesn't give good result it's preferable using Fixer.

对于Crop出现奇数切割或者较为严重没有意义的像素，使用下面滤镜填充修复。

```python
Filtered = core.fb.FillBorders(crop, left, right, top, bottom, mode="fillmargins")
```

优先尝试亮度修复，亮度修复为无损修复。

需要导入**sgvsfunc**，对于亮度修复提供5种方式，根据需要选择。

>   * FixRowBrightness
>   * FixColumnBrightness
>   * FixRowBrightnessProtect2
>   * FixColumnBrightnessProtect2
>   * FixBrightnessProtect2

颜色修复可以尝试使用

> core.edgefixer.ContinuityFixer

如果效果不理想可以使用较为激进的修复方式，导入**muvsfunc**

> muvs.BalanceBorders

```python
clip=svf.FixRowBrightnessProtect2(clip,5,2.5)
clip=svf.FixRowBrightnessProtect2(clip,2,6.5)
clip=svf.FixRowBrightnessProtect2(clip,1,-7.5)
clip=svf.FixRowBrightnessProtect2(clip,0,13.8)
clip = core.edgefixer.ContinuityFixer(clip, left=[0,3,3], top=[2,2,2], right=[0,1,1], bottom=[0,0,0])
```

###### (4).片源修复

​		**请谨慎适用使用全球滤镜！！！！！！！！**

##### 4.参数测试

在正式压制前应进行主要必要参数测试，一般选取中间每2000帧抽取1000帧左右，越5000-8000帧进行测试，输出重置帧率等信息。

```python
select=core.std.SelectEvery(clip[8000:-8000],cycle=2000, offsets=range(85))
clip= core.std.AssumeFPS(select, fpsnum=clip.fps.numerator, fpsden=clip.fps.denominator)
```

参数测试**CRF**为必须测试参数，组内提供方便的测试工具，推荐测试参数如下：

> ap-strong、ap-mod、

##### 5.压制

去除测试代码的抽取测试部分，使用CLI或者本站工具使用测试数值进行压制。

```
111
```

##### 6.字幕处理



##### 7.MKV混流

将视频、音轨、字幕、章节目录重新混流封装为mkv。应该注意各个轨道语言标签以及标题，并设定好对应的默认标签，同时要添加媒体标题。

###### (1).视频轨道

直接添加 可以再title进行压制署名（个人意愿）

###### (2).音频轨道

所有音轨都应具有正确的语言标签，必须将原音音轨设为默认音轨，对于中文音轨，应该在Title内添加具体信息：**Cantonese or Mandarin**，如果多条配音国语音轨，应有响应标明。评论音轨应正确标记语言，同时在Title内写明**Commentary with/by Position Name Surname** 。

音轨应注意排列顺序，原音为首位，国语配音按照个人推荐程度排列，有对应版本字幕优先，评论音轨至后。

###### (3).字幕

所有字幕都应具有正确的语言标签，并完善标题，字幕对应相关国语配音时，应在TiTle做简单比标明。字幕注意默认字幕设置，优先设置高质量字幕。

###### (4).章节标题

原盘附带章节应该添加，对于章节名要使用英文命名，没有名字的请使用Chapter 1、Chapter  2 进行命名

###### (5).文件命名

参考上面规则

###### (6).Movie Title

添加Movie Title，使用下面命名方法:

> ????  [年份] 1080p BluRay MNHD- FRDS

##### 9.生成mediainfo以及对比截图



##### 10.发布与上传



# 常见问题

##### 1.



##### 2.