# GModPatchTool <sub>_原名 GModCEFCodecFix_</sub>

![GModPatchTool](GModPatchToolLogo.png)

***GModPatchTool** 补上 Facepunch [没干的事](https://github.com/Facepunch/gmod-html/pull/8)！*

**!! 由 Solstice Game Studios 制作 !!（[solsticegamestudios.com](https://solsticegamestudios.com)）**


**!! 由 Solstice Game Studios 制作 !!（[solsticegamestudios.com](https://solsticegamestudios.com)）**


**!! 由 Solstice Game Studios 制作 !!（[solsticegamestudios.com](https://solsticegamestudios.com)）**

我自己只是**提供了翻译和镜像源维护**（随心维护

# 🛠️ 我们修补的内容
### 全平台通用
- 修复 macOS 和 Linux 上各种启动/主菜单消失的问题
- 为 GMod 添加 `-chromium_fps_max` 启动选项
  - 可限制所有 CEF 网页面板的内部最大帧率
  - 适当降低网页内容帧率，可能提升游戏帧率
  - 默认值为 60
- 为 GMod 添加 `-chromium_remote_debugging_port` 启动选项
  - 可选功能：在指定端口启动 CEF 远程调试服务（Chrome DevTools），方便检查游戏内 HTML 面板
  - 默认关闭；在 1024 到 65535 之间设置端口号即可启用，例如 `-chromium_remote_debugging_port 9222`
- 为 GMod 添加 `-chromium_mute_audio` 启动选项
  - 可选功能：静音该游戏实例中所有 CEF 网页面板的音频
  - 适合同时运行两个 GMod 实例（`-multirun`），避免声音重叠
- 用自定义的 SourceScheme.res 改进经典 VGUI 主题
- 将调试/控制台字体替换为 [PT Mono](https://fonts.google.com/specimen/PT+Mono)，提升跨平台的一致性和可读性
  - 这对 Proton 尤为重要——原生字体（Lucida Console）在这些平台上显示不全或过小
  - 如果不喜欢主题或字体修改，运行工具时加 `--no-sourcescheme` 参数即可禁用

### 游戏内浏览器（[Chromium Embedded Framework，简称 CEF](https://en.wikipedia.org/wiki/Chromium_Embedded_Framework)）
- 将 CEF 升级到 137.0.19（Chromium 137.0.7151.121）
- 启用[专有音视频编码格式](https://www.chromium.org/audio-video)支持，如 H.264（MP4）和 AAC
- 启用 [Widevine](https://www.widevine.com) 支持（但[没有 VMP](https://github.com/solsticegamestudios/GModPatchTool/issues/100)，所以 Netflix 等服务目前仍无法使用……）
- 启用软件 WebGL
- 启用部分 GPU 加速
- 改进纹理更新的性能
- 禁用硬件媒体键对媒体的控制
- 重新启用站点隔离（安全特性，部分网站依赖此功能才能正常运行）

### Linux
- 可修复 Steam 覆盖层、MangoHud 等不工作的问题
  - 在 GMod 启动选项中加入 `GMOD_ENABLE_LD_PRELOAD=1 %command%` 试试！
  - 默认关闭，因为有时反而会导致 GMod 崩溃
- 设置 `mesa_glthread=true`，配合 Mesa 驱动提升 OpenGL 性能
- 设置 `ulimit -n $(ulimit -Hn)`，修复大量文件打开/挂载的问题（比如很多插件、Lua 自动刷新等）
- 在 `hl2.sh` 中添加各类带注释的环境变量导出，方便多 GPU 用户（通常是笔记本）快速指定 GMod 使用正确的显卡
  - 见 [#188](https://github.com/solsticegamestudios/GModPatchTool/issues/188) 了解我们没有默认启用的原因

### macOS
- 在 Apple Silicon 上提前预热已修补库的 [Rosetta](https://en.wikipedia.org/wiki/Rosetta_(software)) 翻译，避免它们拖慢 GMod 的首次启动
  - 运行工具时加 `--skip-rosetta-prewarm` 参数即可跳过此步骤

# ❓ 玩家：如何安装/使用
下载 **[最新版](https://github.com/solsticegamestudios/GModPatchTool/releases)**，然后运行即可。

需要更详细的教程？请查看 https://solsticegamestudios.com/fixmedia/

# ⚙️ 命令行选项
正常修补不需要任何参数——直接运行工具就行。但如果你想拥有更多控制权：

| 选项 | 作用 |
| --- | --- |
| `-l`, `--launch-gmod` | 修补完成后启动 Garry's Mod |
| `-s`, `--skip-exit-prompt` | 跳过退出时的"按 Enter 键退出..."提示 |
| `--steam-path <路径>` | 指定 Steam 安装路径（不是 Steam 库路径） |
| `--no-sourcescheme` | 不应用 SourceScheme（VGUI 主题）更改 |
| `--skip-clear-chromiumcache` | 跳过删除 GarrysMod 目录下的 ChromiumCache/ChromiumCacheMultirun/chromium.log |
| `--skip-rosetta-prewarm` | 跳过在 Apple Silicon 上预热已修补库的 Rosetta 翻译（仅 macOS） |
| `--disable-cache` | 强制重新下载所有补丁文件，退出时清空 GModPatchTool 缓存目录 |
| `--no-system-proxy` | 不使用系统代理进行 HTTP 请求——如果工具在代理/VPN 后无法下载文件可以试试这个 |
| `--ignore-gmod-running` | 即使 Garry's Mod 正在运行也应用补丁（可能导致问题！） |

如果修补失败，工具会返回非零退出码，方便脚本化集成。

# 👩‍💻 开发者：使用与检测
让玩家直接参考上面"玩家"部分的说明即可。此补丁仅影响客户端！

**如何检测已修补的 CEF：** 请查看我们的 [Lua 检测示例](examples/detection_example.lua)。

> [!WARNING]
> 我们的 CEF 编译版本启用了站点隔离，这意味着 **调用 JavaScript 相关的 DHTML 函数时一定要注意时机！**
>
> 如果在页面开始加载之前调用 [DHTML.AddFunction](https://wiki.facepunch.com/gmod/DHTML:AddFunction)、[DHTML.QueueJavascript](https://wiki.facepunch.com/gmod/DHTML:QueueJavascript) 或 [DHTML.RunJavascript](https://wiki.facepunch.com/gmod/Panel:RunJavascript)，它们**不会生效**！请务必在 [HTML.OnBeginLoadingDocument](https://wiki.facepunch.com/gmod/HTML:OnBeginLoadingDocument) 或更晚的时候再调用。
>
> 站点隔离会在导航时清除 JavaScript 状态，就像真正的浏览器那样。
>
> 本工具包含一个针对 mainmenu.lua 的补丁，解决了 GMod 自身没有使用正确方式调用的问题，但 **对于任何没有妥善处理 HTML 面板状态的插件来说，这是一个破坏性变更**。

**想深入了解？** 查看[我们的 gmod-html 分支](https://github.com/solsticegamestudios/gmod-html)和 [CEF 构建脚本](cef_build)。

# 📢 需要帮助 / 联系我们
* 阅读常见问题：https://solsticegamestudios.com/fixmedia/faq/
* Discord：https://solsticegamestudios.com/discord/
* 邮箱：contact@solsticegamestudios.com

# 💖 资助我们
本项目是开源的，免费提供给 Garry's Mod 社区使用。

**如果你喜欢我们的工作，不妨 [给我们打赏几美元](https://solsticegamestudios.com/donate/)！** 我们的一切工作 100% 来自工具用户的资助。
