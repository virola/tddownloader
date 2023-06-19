# 糖豆视频下载器

> > 简易修改版本

将下载地址配置到 `listfile` 文件中，启动执行 `python main.py` 则自动下载视频至 Downloads 文件夹，不剪辑。

![Python](https://img.shields.io/badge/Python-3.8.3-red)
![Author](https://img.shields.io/badge/Author-CCBP-blue)
![license](https://img.shields.io/badge/license-MIT-lightgrey)

# 使用说明

使用过程中需要注意的是：

- 需要提供想要下载的视频链接，链接中必须包含 vid 参数，或者也可以直接输入 vid 编号；
- 使用回车键（Enter）选择默认值；
- 由于使用单线程下载，故下载速度可能并不会很快；
- 会在选择的储存目录下自动生成 Download 文件夹，之后所有的文件都将储存至该文件夹；
- 若想要下载的视频已经存在则不会再次下载；
- 若需要进行视频剪辑则需要输入起始与结束时间，时、分、秒之间可以使用” “(空格)、”.“(英文句号)、”:“(英文冒号)、”:“(中文冒号)、”,“(英文逗号)、”，“(中文逗号) 进行分隔，以上分隔符也可以混用（不推荐），时、分、秒若高位为 0 也可不全部给出，合法的输入示例如下（以 0 时 2 分 30 秒为例）：
  - 0:2:30
  - 2:30
  - 0.2.30
  - 2 30
  - 0.2:30(不推荐)
- 可以选择不进行剪辑，无论是否进行剪辑都可以在之后的提示中选择将视频文件转换为音频文件；
- 剪辑过的视频可以选择不保存，无论是否选择保存都可以在之后的提示中选择将视频文件转换为音频文件；
- 使用 Ctrl+C 可随时结束程序。

运行应用后会打印出简单的使用提示，示例交互过程如下：

```
===================糖豆视频下载器 By CCBP===================
     使用回车键（Enter）选择默认值，使用Ctrl+C退出程序
视频剪辑的时间输入以" "、"."、":"、"："、","、"，"作为分隔符
============================================================
请输入视频链接或vid编号:https://www.tangdoucdn.com/h5/play?vid=20000002258422&utm_campaign=client_share&utm_source=tangdou_android&utm_medium=wx_chat&utm_type=0&share_uid=#1652176249257
请输入文件储存目录(默认为当前目录):
[308.71 s] Download completed, save to d:\Workspace\Python\tangdou\Download\安徽金社《母亲》网红一夜火爆最新男生版 母亲节献礼附教学.mp4
剪辑起始时间(默认为不剪辑):2.30
剪辑截止时间:5.0
是否保存剪辑过的视频（y/n）:y
[00:02:30<--->00:05:00]
Moviepy - Building video d:\Workspace\Python\tangdou\Download\安徽金社《母亲》网红一夜火爆最新男生版 母亲节献礼附教学_edited.mp4.
MoviePy - Writing audio in 安徽金社《母亲》网红一夜火爆最新男生版 母亲节献礼附教学_editedTEMP_MPY_wvf_snd.mp3
MoviePy - Done.
Moviepy - Writing video d:\Workspace\Python\tangdou\Download\安徽金社《母亲》网红一夜火爆最新男生版 母亲节献礼附教学_edited.mp4

Moviepy - Done !
Moviepy - video ready d:\Workspace\Python\tangdou\Download\安徽金社《母亲》网红一夜火爆最新男生版 母亲节献礼附教学_edited.mp4
是否转换为音频（y/n）:y
MoviePy - Writing audio in d:\Workspace\Python\tangdou\Download\安徽金社《母亲》网红一夜火爆最新男生版 母亲节献礼附教学.mp3
MoviePy - Done.
请输入视频链接或vid编号:https://www.tangdoucdn.com/h5/play?vid=20000002258422&utm_campaign=client_share&utm_source=tangdou_android&utm_medium=wx_chat&utm_type=0&share_uid=#1652176249257
请输入文件储存目录(默认为当前目录):
[11.35 s] Download completed, save to D:\Workspace\Python\tangdou\dist\Download\雨凡《疯疯疯》64步弹跳附分解.mp4
剪辑起始时间(默认为不剪辑):11
剪辑截止时间:3.56
是否保存剪辑过的视频（y/n）:n
是否转换为音频（y/n）:y
MoviePy - Writing audio in D:\Workspace\Python\tangdou\dist\Download\雨凡《疯疯疯》64步弹跳附分解.mp3
MoviePy - Done.
请输入视频链接或vid编号:Traceback (most recent call last):
  File "d:\Workspace\Python\tangdou\main.py", line 144, in <module>
    main()
  File "d:\Workspace\Python\tangdou\main.py", line 62, in main
    url = input('请输入视频链接或vid编号:')
KeyboardInterrupt
```

> Linux 用户可以自行安装 python 以及使用 `pip install -r requirements.txt` 安装依赖，执行命令 `python main.py` 运行程序

# 实现方法

[糖豆](https://www.tangdoucdn.com/)视频都是以 URL 的参数`vid`作为引索，可以通过`vid`获得想要的视频。原始视频链接的获取有 HTML 解析与 API 接口请求两种方式。

## 视频 API 请求

接口地址为`api-h5.tangdou.com/sample/share/main?vid=`，该接口返回一个 JSON 格式数据，对该数据进行解析后`data`属性下的`video_url`的值正是原始视频链接，`title`属性的值则为使用 Unicode 编码的视频名称，但要想从此接口正常获取数据需提供请求标头如下：

```
Accept: application/json, text/plain, */*
Accept-Encoding: gzip, deflate, br
Accept-Language: zh,zh-CN;q=0.9
Connection: keep-alive
Host: api-h5.tangdou.com
Referer: https://www.tangdoucdn.com/
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.54 Safari/537.36
```

## 音频 API 请求

糖豆视频页面下方会有推荐的**相关视频**，接口地址为`api-h5.tangdou.com/sample/share/recommend?page_num=1&vid=`。该接口返回一个 JSON 格式数据，对该数据进行解析后`data`属性的值为一个**数组**，数组的每一个元素即为一个推荐的视频，其中的`mp3url`的值为原始音频链接，`title`属性的值为使用 Unicode 编码的音频名称，所需请求标头同上。

其中`mp3url`的值为`https://mp3qingiu.tangdou.com/wuqu/xxxx.mp3`，直接通过该地址即可获取音频文件，但`xxxx.mp3`为一个长度为 32 的编号，通过观察推断该编号为使用 MD5 算法生成的，由于不清楚原文是什么，故无法直接使用该链接地址获取音频。

🔴 此接口实际上为获取视频下方**推荐视频**对应的音频，并不是我们实际想要的音频，故暂时**弃用**此方法。

## HTML 解析

通过访问视频链接，通过 GET 方法获得响应体，响应体为 HTML 文档，对其进行解析寻找`video`标签便可获得视频原始连接。但若直接访问`www.tangdoucdn.com/h5/play?vid=`是无法找到`video`标签的，因为该地址中的`video`标签为动态生成的。而访问`share.tangdou.com/splay.php?vid=`则可以直接获取`video`标签。

## 注意

下载时必须附带如下请求头才可正常获取我们想要的视频：

```
Referer: https://www.tangdoucdn.com
```

否则下载到的视频则是一个名为`hello.mp4`的默认视频文件。

# 致谢

<a href="https://www.flaticon.com/free-icons/direct-download" title="direct download icons">Direct download icons created by Freepik - Flaticon</a>
