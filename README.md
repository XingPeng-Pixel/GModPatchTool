# GModPatchTool <sub>_原名 GModCEFCodecFix_</sub>

![GModPatchTool](GModPatchToolLogo.png)

***GModPatchTool** 做了 Facepunch [懒得做](https://github.com/Facepunch/gmod-html/pull/8) 的事！*

**由 Solstice Game Studios 出品（solsticegamestudios.com）**

# 🛠️ 我们会打哪些补丁
### 全平台通用
- 修复 macOS 和 Linux 上各种启动失败、主菜单加载不出来的问题
- 为 GMod 增加 `-chromium_fps_max` 启动参数
  - 可以限制**所有 CEF 网页面板**的内部最大帧率
  - 用网页内容帧率换游戏整体帧率，看你取舍
  - 默认 60 帧
- 用我们自制的 SourceScheme.res 优化了老旧的 VGUI 主题
- 把 Debug / 控制台字体换成了 [PT Mono](https://fonts.google.com/specimen/PT+Mono)，跨平台显示更统一、更易读
  - 这对 Proton 特别重要——原版环境下 Lucida Console 缺失，用那些字体的文字要么小得看不清要么直接崩
  - 如果不喜欢主题或字体改动，运行工具时加上 `--no-sourcescheme` 参数就能禁用

### 游戏内网页浏览器（Chromium Embedded Framework，简称 CEF）
- 升级 CEF 到 137.0.10（Chromium 137.0.7151.69）
- 开启[专有音视频解码器](https://www.chromium.org/audio-video)支持，比如 H.264（MP4）和 AAC
- 开启 [Widevine](https://www.widevine.com) 支持（但 [VMP 不行](https://github.com/solsticegamestudios/GModPatchTool/issues/100)，所以 Netflix 之类的暂时还是没法看……）
- 开启软件 WebGL
- 启用部分 GPU 加速
- 提升纹理更新性能
- 禁用硬件媒体键控制媒体播放
- 重新开启站点隔离（安全特性，有些站点没它没法正常跑）

### Linux 专属
- 可能修好 Steam 界面 / MangoHud 等不工作的问题
  - 在 GMod 启动选项里填 `GMOD_ENABLE_LD_PRELOAD=1 %command%` 试试！
  - 默认关闭，因为强行开启可能直接让 GMod 崩溃
- 设置 `mesa_glthread=true`，让 Mesa 驱动下的 OpenGL 性能更好
- 设置 `ulimit -n $(ulimit -Hn)`，解决打开 / 挂载太多文件（比如海量 addon、Lua 自动刷新等）时的问题
- 在 `hl2.sh` 里添加了一些被注释掉的 export 命令，方便多显卡用户（尤其是笔记本）快速指定 GMod 用哪张显卡
  - 具体为什么默认不开启，见 [#188](https://github.com/solsticegamestudios/GModPatchTool/issues/188)

# ❓ 玩家：怎么安装 / 使用
去 **[最新发布页](https://github.com/solsticegamestudios/GModPatchTool/releases)** 下载，然后运行程序就行。

想要更详细的教程？戳 https://solsticegamestudios.com/fixmedia/

# 👩‍💻 开发者：怎么用 / 怎么检测
让玩家按上面的“玩家”教程操作即可。注意这个补丁**只影响客户端**！

**如何检测 CEF 是否打过补丁：** 看看我们的 [Lua 检测示例](examples/detection_example.lua)。

> [!WARNING]
> 我们的 CEF 版本开启了**站点隔离**，这意味着**调用 JavaScript 相关的 DHTML 函数时必须注意时机！**
>
> 如果你在页面**开始加载之前**调用 [DHTML.AddFunction](https://wiki.facepunch.com/gmod/DHTML:AddFunction)、[DHTML.QueueJavascript](https://wiki.facepunch.com/gmod/DHTML:QueueJavascript) 或 [DHTML.RunJavascript](https://wiki.facepunch.com/gmod/Panel:RunJavascript)，是**不会生效的**！请确保在 [HTML.OnBeginLoadingDocument](https://wiki.facepunch.com/gmod/HTML:OnBeginLoadingDocument) 或更晚的时机调用。
>
> 站点隔离会在页面导航时销毁 JavaScript 状态，就跟正经浏览器一个样。
>
> 本工具包含一个针对 mainmenu.lua 的补丁，用于解决 GMod 自身没按正确方式处理的问题，但**这属于破坏性改动**——任何没正确处理好 HTML 面板 JS 状态的插件都可能炸。

**想深入研究？** 可以看看 [我们 fork 的 gmod-html](https://github.com/solsticegamestudios/gmod-html) 和 [我们的 CEF 构建脚本](cef_build)。

# 📢 需要帮助 / 联系我们
* 阅读常见问题：https://solsticegamestudios.com/fixmedia/faq/
* Discord：https://solsticegamestudios.com/discord/
* 邮箱：contact@solsticegamestudios.com

# 💖 帮我们一把
这个项目是开源的，对 Garry's Mod 社区完全免费。

**如果你喜欢我们做的事，[请考虑打赏几块钱](https://solsticegamestudios.com/donate/)！** 我们的工作 100% 靠用户赞助支持！