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
| **当前 Git Commit** | `2a74320` | `feat(stealth): eliminate CLSimulationManager and CLLocationSourceInformation static metadata, dynamically register sensitive anti-detection hooks` |
| **GitHub Actions 运行记录** | Run #94 (ID: `40222039693`) | 🟢 **Both Jobs Success** (`build (rootless)` & `build (roothide)`) |

---

## 三、当前会话中涉及的关键文件和路径

### 1. 核心工程源码文件
* **[Tweak.x](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Tweak.x)** (18,763 行，994,642 字节)
  - SHA256: `62D3DD30FA8FCB0F221F9C2D814F27AA772319467A78E7C9729573E51FC1B544`
* **[layout/DEBIAN/postinst](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/layout/DEBIAN/postinst)** (362 字节)
* **[.github/workflows/build.yml](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/.github/workflows/build.yml)** (1,757 字节)

### 2. 四路同步镜像文件（100% 字节一致）
* [D:\主修实时翻译.txt](file:///D:/主修实时翻译.txt) (994,642 字节，SHA256: `62D3DD30FA8FCB0F221F9C2D814F27AA772319467A78E7C9729573E51FC1B544`)
* [D:\主修实时翻译_2.txt](file:///D:/主修实时翻译_2.txt) (994,642 字节，SHA256: `62D3DD30FA8FCB0F221F9C2D814F27AA772319467A78E7C9729573E51FC1B544`)
* [D:\天使siri测试版\Tweak.x](file:///D:/天使siri测试版/Tweak.x) (994,642 字节，SHA256: `62D3DD30FA8FCB0F221F9C2D814F27AA772319467A78E7C9729573E51FC1B544`)
* [E:\主修实时翻译.txt](file:///E:/主修实时翻译.txt) (994,642 字节，SHA256: `62D3DD30FA8FCB0F221F9C2D814F27AA772319467A78E7C9729573E51FC1B544`)
* [D:\定位与防检测底部代码.txt](file:///D:/定位与防检测底部代码.txt) (47,477 字节，SHA256: `372DDD322AE59C50D9FF25D7819ABD24C4DB982A6F1BA98ABA657B956B790A98`)

### 3. 最新云端编译产物（Run #117 / Commit `168a7c6`）
> **本次更新（实例级快照绑定 + 彻底消除探针上报 + 伪沙盒 Frameworks 路径去敏 + 考勤识别函数）**：
> 1. `AGCLDaemonLocation` 结构体：严格对齐为 0x9C (156 字节)，彻底消除 `_reservedPadding[32]`，杜绝写穿 x8 寄存器栈帧。
> 2. `agSyncDynamicTweakFilterPlist` 优化：白名单模式仅注入桌面、设置、微信及勾选目标软件，彻底杜绝未勾选软件被意外注入。
> 3. 探针与跨进程特征彻底消除：`agPostClientDiagEvent` 与 `agReportAppLocationHookHitToSpringBoard` 全面置空，不产生任何 IPC 行为日志。
> 4. `CLLocation` 实例级不可变快照绑定：通过 `agGetOrBindTelemetryForLocation` 实现与 `kAGBoundTelemetryDataKey` 动态关联绑定，无论钉钉/企微/飞书隔多久重复读取多少次，所有经纬度、海拔、精度、航向与速度属性 100% 恒定自洽且在 `copyWithZone:` 中同步传递。
> 5. 伪沙盒 Frameworks 路径去敏：`_dyld_get_image_name` 为越狱 dylib 动态映射沙盒 `App.app/Frameworks/CoreSpatialKit_%u.framework` 虚拟路径，杜绝与原生 `CoreLocation` 产生 Mach-O 头部冲突。
> 6. Wi-Fi 防护引擎重构：默认模式不凭空捏造假 BSSID，对考勤软件直接屏蔽 Wi-Fi 伪装纯 5G 蜂窝流量打卡，选定 Wi-Fi 模式精准注入指定 SSID/BSSID。
> 7. 纯 `xz` 压包标准，100% 兼容 Sileo。

* **Dopamine (Rootless arm64)**：
  - 包名：`com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb`
  - 文件大小：`406,612` 字节
  - SHA256: `ABA58BEAB39F547DDA83CDF2266E1974DE2D36BA7B377238C4D60E78EA5F9E8D`
  - 归档结构：`debian-binary` (4 字节), `control.tar.xz` (780 字节), `data.tar.xz` (405,464 字节)
  - 分发路径：
    - [D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
    - [D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
    - [packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)

* **Relaxin (RootHide arm64e)**：
  - 包名：`com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb`
  - 文件大小：`265,604` 字节
  - SHA256: `95D7CD75D8936D404B63672A45C12912D40F652A279C82483262E8435BDDB9A6`
  - 归档结构：`debian-binary` (4 字节), `control.tar.xz` (780 字节), `data.tar.xz` (262,808 字节)
  - 分发路径：
    - [D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
    - [D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
    - [packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
