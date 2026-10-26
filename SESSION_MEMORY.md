# TimeGuardianSiri123 项目会话记忆文件 (SESSION_MEMORY.md)

> **生成时间**：2026-10-26 02:40:00 (本地时间)  
> **会话标识**：`02de72ea-60df-4738-8977-4a4ebaf78b2f`  
> **用途说明**：供用户切换 Google Antigravity 账号或开启新会话后，直接读取本文件无缝继承当前项目的完整开发上下文。

---

## 一、当前任务目标

1. **项目核心定位**：
   - 开发与维护 iOS 15.0 ~ 16.5+ 越狱环境下的全功能插件 **TimeGuardianSiri123**（包名：`com.custom.timeguardiansiri123`，版本号：`6.0.0`）。
   - **双越狱架构原生无缝兼容**：同时支持 **Dopamine (Rootless `/var/jb`)** 与 **Relaxin (RootHide 随机根目录 `/var/containers/Bundle/Application/.jbroot-*`)**。
   - 包含四大核心体系：
     1. **全硬件级底层定位伪装**：直接拦截并改写 `CLDaemonLocation` 私有结构体，支持真实运动轨迹模拟、卡尔曼滤波平滑漂移、多进程内核级状态广播（通过 `notifyd` 纳秒级共享内存同步）。
     2. **零闪退「日常纯定位 / 原生 IMP 动态还原」双模分级内核**：在关闭虚拟定位时，通过原生 IMP 动态还原引擎将全部 40+ 个底层 ObjC 方法 100% 原地还原为系统原生实现，彻底解决“关闭时提示插件问题”；仅在【虚拟定位开启 + 防检测开启】时才挂载底层 C 函数 Hook。
     3. **天使守护 Siri 悬浮工作台**：全局浮动交互 UI、多主题液态镜面玻璃渲染、双麦克风语音监听与 Edge TTS 语音合成。
     4. **独立相机辅助工具**：`AngelCameraHelper` 独立守护进程与虚拟视频流注入。

2. **最新应用的 4 大深度去特征化与反检测动态引擎重构（Commit `2a74320`）**：
   - **【修改点 1】补充顶部反风控宏并移除暴露在 Mach-O 属性表中的 CLSimulationManager 类型声明**：
     - 追加 `simManager -> spatialBridgeObj`、`_simManager -> _spatialBridgeObj`、`isDaemonSimRunning -> isSpatialBridgeActive`、`onSimulationTick -> onSpatialSyncTick`、`ssidInputField -> primaryTagInputField`、`bssidInputField -> secondaryTagInputField`、`customWiFiSwitchChanged -> spatialTagSwitchChanged` 宏定义；
     - 彻底清除静态明文 `@interface CLSimulationManager` 与 `@interface CLLocationSourceInformation`，替换为运行时 XOR 解码器 `agDecodeObfuscatedCStr` 与 `agDecodeSelector`；
     - 属性表中将 `CLSimulationManager *simManager` 声明为 `id simManager`，消除 Mach-O 属性签名类型特征。
   - **【修改点 2】彻底停止向系统 locationd 写入模拟位置（消除 locationd 缓存污染）**：
     - 重写 `- (void)pushSimulatedLocationToDaemonWithCourse:speed:forceRestart:`，不再向 `locationd` 注入任何模拟坐标，仅在首次或关闭时利用 XOR 混淆选择器将历史残留模拟状态彻底清零并 flush，杜绝 `locationd` 缓存被标记为 `isSimulatedBySoftware=YES`。
   - **【修改点 3】消除 agCreateHardwareGradeLocation 中的明文 initWithSoftwareSimulationState: 选择器**：
     - 在构建硬件级 CLLocation 时，通过 XOR 动态解码 `kEncSrcCls`、`kEncInitSrc` 与 `kEncInitLoc` 并结合 `objc_msgSend` 动态调用，彻底从二进制中消除明文方法特征。
   - **【修改点 4】CLLocationManager 统一进程内回调 & 敏感防检测 Hook 改为无符号动态挂载**：
     - `startUpdatingLocation` 与 `requestLocation` 无论全局模式还是白名单模式，统一在当前进程直接以真实委托方式回调虚拟位置，解除对 `CLSimulationManager` 的依赖；
     - 将 `isSimulatedBySoftware`、`isProducedByAccessory`、`_isSimulated`、`isSimulated`、`sourceInformation`、`type`、`clientLocation` 从静态 `%hook` 中移除，转为纯 C 函数；在进程运行时通过 XOR 解码选择器动态查找 Method 并替换/还原 IMP，绝不在 Mach-O 的 `__objc_methname` 表中留下任何敏感选择器明文。

---

## 二、当前项目及工作目录

| 目录类型 | 绝对路径 | 说明 |
| :--- | :--- | :--- |
| **本地工作目录** | `C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123` | 当前活跃的 Git 仓库根目录，包含完整源码与构建配置 |
| **D盘归档目录** | `D:\天使siri测试版` | 完整备份目录，包含 `.git` 库、源码及最新双版本 `.deb` |
| **E盘归档目录** | `E:\钉钉正常版本，最新防检测，关闭时提示插件问题待调` | 完整项目工程归档，含源码、配置及双版本 `.deb` |
| **E盘单文件归档** | `E:\钉钉正常版本，最新防检测，关闭时提示插件问题待调.txt` | 钉钉可打卡版本完整代码备份 |
| **远端 Git 仓库** | `https://github.com/jianchengw952-source/TimeGuardianSiri123.git` | GitHub 远程仓库（分支：`main`） |
| **当前 Git Commit** | `fe2715b` | `feat(stealth): eliminate CLSimulationManager and CLLocationSourceInformation static metadata, dynamically register sensitive anti-detection hooks` |
| **GitHub Actions 运行记录** | Run #121 (ID: `40246994613`) | 🟢 **Both Jobs Success** (`build (rootless)` & `build (roothide)`) |

---

## 三、当前会话中涉及的关键文件和路径

### 1. 核心工程源码文件
* **[Tweak.x](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Tweak.x)** (18,683 行，989,082 字节)
  - SHA256: `6AB6A3AF62D25A08180186A5F3419E37CCD77E9E0D27BA2232934FFB250B99A3`
* **[layout/DEBIAN/postinst](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/layout/DEBIAN/postinst)** (362 字节)
* **[.github/workflows/build.yml](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/.github/workflows/build.yml)** (1,757 字节)

### 2. 四路同步镜像文件（100% 字节一致）
* [D:\主修实时翻译.txt](file:///D:/主修实时翻译.txt) (989,082 字节，SHA256: `6AB6A3AF62D25A08180186A5F3419E37CCD77E9E0D27BA2232934FFB250B99A3`)
* [D:\主修实时翻译_2.txt](file:///D:/主修实时翻译_2.txt) (989,082 字节，SHA256: `6AB6A3AF62D25A08180186A5F3419E37CCD77E9E0D27BA2232934FFB250B99A3`)
* [D:\天使siri测试版\Tweak.x](file:///D:/天使siri测试版/Tweak.x) (989,082 字节，SHA256: `6AB6A3AF62D25A08180186A5F3419E37CCD77E9E0D27BA2232934FFB250B99A3`)
* [E:\主修实时翻译.txt](file:///E:/主修实时翻译.txt) (989,082 字节，SHA256: `6AB6A3AF62D25A08180186A5F3419E37CCD77E9E0D27BA2232934FFB250B99A3`)
* [D:\定位与防检测底部代码.txt](file:///D:/定位与防检测底部代码.txt) (42,276 字节，SHA256: `661DA351DC6D9A9763AFE461349C7E133BFAAF188430B0866A043F390708BE05`)

### 3. 最新云端编译产物（Run #121 / Commit `fe2715b`）
> **本次更新（纯 C 栈遥测缓存 + 消除关联对象竞争 + 修复 sCleanSource 释放闪退 + 移除全部 dyld/dladdr 钩子）**：
> 1. `AGSpoofedTelemetry` 纯 C 栈与轻量锁重构：移除全部 `kAGBoundTelemetryDataKey` 与 `objc_setAssociatedObject` 动态关联绑定，彻底消除多线程高频读取时的内存竞争与死锁；放宽缓存窗口至 1.80 秒，保证物理运动遥测平滑自洽。
> 2. 修复 `agDyn_CLLoc_srcInfo`：直接调用原实现返回系统原生实例（内部已被动态拦截返回 NO），彻底根除手动分配 `sCleanSource` 在 ARC 下过度释放导致的野指针闪退。
> 3. 安全收敛 C 钩子与系统符号：完全剔除对 `dladdr` 与 `_dyld_get_image_name` 的劫持覆写，防止现代崩溃日志收集框架在栈回溯时遭遇段错误；仅保留 `CNCopyCurrentNetworkInfo` 进行 Wi-Fi 隐藏与伪装。
> 4. 纯 `xz` 压包标准，100% 兼容 Sileo。

* **Dopamine (Rootless arm64)**：
  - 包名：`com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb`
  - 文件大小：`403,080` 字节
  - SHA256: `0EDAFBB29C874FE0C476482EE615D47B6E208352692EB67E1CB2DB3E1433BF27`
  - 归档结构：`debian-binary` (4 字节), `control.tar.xz` (780 字节), `data.tar.xz` (401,928 字节)
  - 分发路径：
    - [D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
    - [D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
    - [packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)

* **Relaxin (RootHide arm64e)**：
  - 包名：`com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb`
  - 文件大小：`264,980` 字节
  - SHA256: `E19386B0DEE4381E88439662813DEFC5615954D655AE306468244C6F637D6835`
  - 归档结构：`debian-binary` (4 字节), `control.tar.xz` (780 字节), `data.tar.xz` (262,176 字节)
  - 分发路径：
    - [D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
    - [D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
    - [packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
