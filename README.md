# wiliwili 自定义布局

## 介绍

没有哪个应用是完美的，wiliwili 也不例外。为了让更多人能够以自己喜欢的方式使用 wiliwili，提供了自定义布局的支持。

只要简单调整，你就能拥有一个属于自己的 wiliwili。

## 已有布局介绍

1. no_live 去除了直播页面（需求来自一位不希望孩子被直播荼毒的父亲）
2. single_collection 在主页显示指定的收藏夹（需求来自一位家里老人不会用电视找节目的网友）
3. small_icon 更小的主页视频图标 （需求来自一位大屏用户）

## 如何使用

1. 打开 wiliwili 配置目录（应用设置/实用工具 有快捷打开方式）
2. 将本项目的内容复制到 wiliwili 配置目录下 theme 文件夹内
3. 重启应用，在设置中选择自定义布局即可

复制好之后，配置目录应该长这样：

```
wiliwili
├── theme
│   ├── layout_1
│   │   ├── resources_meta.json
│   │   └── xml
│   ├── layout_2
│   │   ├── resources_meta.json
│   │   └── xml
│   └── layout_3
│       ├── resources_meta.json
│       └── xml
└── wiliwili_config.json
```

## 如何自定义

如果你也想自定义布局，可以参考本项目的内容。

1. 找到你正在使用的 wiliwili 的布局文件，测试版文件在：[wiliwili/dev](https://github.com/xfangfang/wiliwili/tree/dev/resources)，稳定版文件在：[wiliwili/yoga](https://github.com/xfangfang/wiliwili/tree/yoga/resources)。
2. 在布局文件目录（resources）内添加 resources_meta.json，将里面的 name 改为你喜欢的名字，将里面的 version 改为 对应的软件版本。其他内容模仿本项目下其他布局填写即可。
3. 将布局文件目录 (resources) 复制到 配置目录的 theme 文件夹内，重命名为 layout_1（可以任意指定名称，不过尽量不要带空格，尽量使用英文小写名，保证全平台可用）

完成了这三步，你就可以在 wiliwili 的设置中选择你的布局了。当然，现在你的布局还是和原版一样的，接下来就是你的自定义时间了。

关于具体 xml 如何自定义，建议大家参考本项目下的其他布局和wiliwili各组件的源码，有任何编程问题都可以发送到 issue 中。

## 贡献

如果你希望你的布局能够被更多人使用，可以将你的布局文件提交到本项目中。注意你只需要提交修改过的文件，而不是提交全部文件。并修改 README.md 添加你的布局介绍。

## 关于需求

如果你不了解如何编写自定义布局，但是你有一个绝佳的想法，同样可以将你的需求发送的 issue 中，并期待其他有类似需求且了解如何编写布局的网友来完成你的需求。

你可以在发布需求时附带赏金，如果有人完成了你的需求，你可以给予他一定的赏金。

无论如何，请不要请求仓库的维护者来完成你的需求，维护者们通常有很多其他的事情在做，不要打扰他们的时间。

## 本项目中的自定义改动

本仓库在官方基础上额外包含以下修改：

- `small_icon/xml/activity/player_activity.xml`：播放页布局。视频下方信息栏高度 177 → 140，播放器宽度 = 窗口宽 − 390。
- `patches/sidebar-width.patch`：主页左侧导航栏固定像素宽度（默认 100px）、侧栏图标 32px、浅色主题页面背景纯白、播放器 OSD 控件缩放对齐、评论右键复制。
- `patches/borealis-right-click.patch`：鼠标右键不再映射为任何按键，改为广播 `Application::getRightClickEvent()`；仅在评论上用于复制。同时设置 Wayland app_id / X11 WM_CLASS 为 `cn.xfangfang.wiliwili`，使 dock 能关联到 .desktop。

> 侧栏宽度是用代码常量实现的，纯 XML 无法覆盖，需要重新编译 wiliwili：
> ```bash
> cd wiliwili && git apply /path/to/sidebar-width.patch
> cd library/borealis && git apply /path/to/borealis-right-click.patch   # 右键行为
> cd ../.. && cmake --build build -j 20
> ```
> 想改宽度就改 `wiliwili/source/view/auto_tab_frame.cpp` 里的 `AUTO_SIDEBAR_FIXED_PX`。

### 播放器控件与右键行为

- `resources/xml/views/video_view.xml`：底部 OSD 控件缩放并对齐（底栏 120→100、播放/暂停 100→44 且与进度条中线对齐、进度条 50→36、时间行 54、右侧图标框 60→48、图标 30→24、锁按钮 50→44 / 24→20，时间标签宽度保持 100）。
- 右键在评论上 = 复制整条评论文字（会弹提示）；首页/其它页面右键完全无动作（不会弹搜索框，也不会返回）。

### 调试参数

重新编译后可用 `wiliwili --bv BV1xxxxxxxx` 直接打开指定视频的播放页（方便验证界面改动，不需要手动点进去）。

### 主页侧栏细节

- 侧栏底部“消息/设置”两个按钮的容器宽度跟随侧栏固定宽度（`AutoTabFrame::AUTO_SIDEBAR_FIXED_PX`），随窗口尺寸订阅更新，保证水平居中。

### 首页列表间距

- `resources/xml/fragment/home_recommends.xml` 与 `small_icon/xml/fragment/home_recommends.xml`：视频网格 `marginTop` 10 → 4，列表与顶部标签栏更贴近。

### 评论区边距

- 评论与用户信息：`player_activity.xml` 评论网格 `paddingLeft/Right` 17；`video_comment.xml` 正文 `marginLeft` 4；`user_info.xml` 头像 `marginLeft` 4、关注按钮与用户名同行（间距 16）、头像垂直居中。

### 视频卡片圆角

- `resources/xml/views/video_card.xml`：封面图 `video/card/picture` 的 `cornerRadius` 4 → 10。

### 布局文件放哪了（small_icon）

播放页/首页/播放器 OSD 的改动都同时放进了 `small_icon/xml/`，按原相对路径覆盖，选“缩小图标”布局即可全部生效：

| 改了什么 | 文件（相对 small_icon/） |
|---|---|
| 播放页整体布局、视频区/评论栏宽度、信息栏高度、评论网格边距 17 | `xml/activity/player_activity.xml` |
| 底部图标栏容器（与侧栏同宽、居中） | `xml/activity/main.xml` |
| 首页网格与标签栏间距 4 | `xml/fragment/home_recommends.xml` |
| 视频卡片封面圆角 10 | `xml/views/video_card.xml` |
| 评论正文左右边距 4/4、点赞行对齐 | `xml/views/video_comment.xml` |
| 用户信息行头像 marginLeft 4；关注按钮与用户名同行（间距 16） | `xml/views/user_info.xml` |
| 播放器 OSD 按钮/进度条尺寸与对齐 | `xml/views/video_view.xml` |

注意：这些 XML 里引用了 `@style/wiliwili/player/width` 和 `main/user_box`，需要配合本项目补丁编译出的二进制（见 `patches/`），官方原版二进制不认这两个。

### 点赞/投币/收藏/分享按钮

- `resources/xml/activity/player_activity.xml`：图标 48→36（点赞）、40→30（其余），按钮框 56→42，按钮间距 22（由 spaceEvenly 改为 flexStart + 固定间距，约为原来 2/3）。

### 字号与右侧对齐

- `resources/xml/activity/player_activity.xml`：点赞/投币/收藏/分享整组紧跟「关注」按钮之后（左侧用户信息容器改 `width=auto`，按钮组 `marginLeft=16`、`width=auto`）；视频标题 `fontSize` 24 → 21。
- `resources/xml/views/video_comment.xml`：评论正文 `TextBox` 设 `fontSize=17`。

### 评论头像垂直居中

- `resources/xml/views/user_info.xml`：头像容器 `height=100%` + `justifyContent=center` + `marginBottom=12`，头像在「用户名 / 时间」两行之间垂直居中；用户名 `fontSize` 20 → 18。

### 退出确认框尺寸

- borealis `library/lib/views/dialog.cpp`：`brls/dialog/applet` 宽度 720 → 420，弹窗不再占满半屏。

### 页面白底（XML 方案）

- 6 个无视频的 activity 根 `AppletFrame` 加 `backgroundColor="#FFFFFF"`：`main.xml`、`pgc_index_activity.xml`、`search_activity.xml`、`search_activity_tv.xml`、`setting_activity.xml`、`hint_activity.xml`。
- 播放页不走根容器：`video_detail_info_box` 加 `backgroundColor="#FFFFFF"`（**根容器不能加**，不透明背景会盖住 mpv 视频层，导致播放区全白）。
- 不再修改主题色 `brls/background` / `brls/clear`，纯布局实现；首页、播放页实测背景为纯白。
