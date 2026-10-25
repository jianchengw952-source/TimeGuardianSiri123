# TimeGuardianSiri123 项目会话记忆文件 (SESSION_MEMORY.md)

> **生成时间**：2026-10-26 00:20:00 (本地时间)  
> **会话标识**：`02de72ea-60df-4738-8977-4a4ebaf78b2f`  
> **用途说明**：供用户切换 Google Antigravity 账号或开启新会话后，直接读取本文件无缝继承当前项目的完整开发上下文。

---

## 一、当前任务目标

1. **项目核心定位**：
   - 开发与维护 iOS 15.0 ~ 16.5+ 越狱环境下的全功能插件 **TimeGuardianSiri123**（包名：`com.custom.timeguardiansiri123`，版本号：`6.0.0`）。
   - **双越狱架构原生无缝兼容**：同时支持 **Dopamine (Rootless `/var/jb`)** 与 **Relaxin (RootHide 随机根目录 `/var/containers/Bundle/Application/.jbroot-*`)**。
   - 包含四大核心体系：
     1. **全硬件级底层定位伪装**：直接拦截并改写 `CLDaemonLocation` 私有结构体，支持真实运动轨迹模拟、卡尔曼滤波平滑漂移、多进程内核级状态广播（通过 `notifyd` 纳秒级共享内存同步）。
     2. **零闪退反检测隐身护盾**：针对大厂与考勤 App，彻底剔除破坏性 C 级文件钩子与内联跳转，采用符号重映射去特征、Wi-Fi 5G 蜂窝流量隐身、0.95s 帧锁定快照、1Hz GNSS 硬件采样对齐与真实传感器合成。
     3. **天使守护 Siri 悬浮工作台**：全局浮动交互 UI、多主题液态镜面玻璃渲染、双麦克风语音监听与 Edge TTS 语音合成。
     4. **独立相机辅助工具**：`AngelCameraHelper` 独立守护进程与虚拟视频流注入。

2. **最新应用的 6 大反风控与底层隐身强化**：
   - **【修改点 1】符号去特征化宏（消除 `objc_copyClassList` 类名与方法名扫描特征）**：
     - 在文件头部引入重命名宏，将 `AGLocationSpoofManager`、`AGLocationSpoofSettingsViewController`、`sharedSpoofer`、`isSpoofingActive`、`_isSpoofingActive`、`constructRealisticSpoofedLocation` 等符号全量重命名为 `AGCoreSpatialCoordinator`、`AGSpatialViewportController`、`sharedCoordinator`、`isSpatialActive` 等纯净系统级空间坐标系风格标识符，彻底破除大厂反作弊全包扫描。
   - **【修改点 2】通道伪装 + 全局 Preferences 污染擦除 + 防检测静默 IPC**：
     - 将 `agPublishSpoofStateToKernel` 与 `agReadSpoofStateFromKernel` 的通知通道从 `com.custom.ag.loc.*` 伪装为苹果原生风格 `com.apple.coremedia.spatial.st/x/y/v/p*`。
     - 彻底删除 `reloadGeminiPrefsCache` 中读取 `AG_LocSpoof_GlobalSharedDict` 的逻辑；并在 `saveGeminiPrefs` 中执行一次置空擦除，清除手机 `.GlobalPreferences.plist` 中残留的历史明文字典。
     - `agReportAppLocationHookHitToSpringBoard` 在开启防检测时静默返回，不发起跨进程通知。
   - **【修改点 3】0.95 秒锁帧快照机制（彻底消除微秒级数值跳变与同一实例属性不一致）**：
     - 在 `agGetCurrentSpoofedTelemetry` 中引入 `sLastTelemetryBaseLat/Lon`，在 0.95 秒周期内锁定采样快照。同一时刻或同一个 `CLLocation` 实例连续读取 `coordinate`、`horizontalAccuracy`、`altitude` 时返回完全一致的数值，杜绝因高频属性读取产生的抖动破绽。
   - **【修改点 4】开启「防检测保护」时停用 `CLSimulationManager`**：
     - 在 `pushSimulatedLocationToDaemonWithCourse` 中，当 `shieldOn == YES` 时主动停止 `CLSimulationManager` 并清空模拟点，防止在 `locationd` 中留下软件模拟标志。
   - **【修改点 5】彻底净化 `CLLocation.sourceInformation` 并移除 Block 跳板指针**：
     - `sourceInformation` 在命中伪装时无条件返回干净的非模拟硬件级对象（去除冗余判空）。
     - 将 `agSwizzleLocationDelegateClassIfNeeded` 与 `agHookAMapLocationSDKIfPresent` 改为空操作（No-op），去除 `imp_implementationWithBlock` 产生的 libobjc 跳板指针，让钉钉 Delegate 与高德 SDK 保留原装宿主 IMP，彻底消除跳板指针检测特征并由底层 CoreLocation 统一注入坐标。
   - **【修改点 6】移除 `%ctor` 中破坏系统 C 函数入口的 `MSHookFunction` 内联钩子**：
     - 剔除对 `dladdr` 与 `CNCopyCurrentNetworkInfo` 的 `MSHookFunction` 内联挂钩，彻底消除被阿里聚安全检测出的 `BR X16` 机器码跳转，网络信息交由纯净的 `NEHotspotNetwork` ObjC Hook 处理。

---

## 二、当前项目及工作目录

| 目录类型 | 绝对路径 | 说明 |
| :--- | :--- | :--- |
| **本地工作目录** | `C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123` | 当前活跃的 Git 仓库根目录，包含完整源码与构建配置 |
| **D盘归档目录** | `D:\天使siri测试版` | 完整备份目录，包含 `.git` 库、源码及最新双版本 `.deb` |
| **远端 Git 仓库** | `https://github.com/jianchengw952-source/TimeGuardianSiri123.git` | GitHub 远程仓库（分支：`main`） |
| **当前 Git Commit** | `f17822d` | `fix(build): add forward declaration for agIsAntiDetectionShieldActive and define _isSpoofingActive ivar alias` |
| **GitHub Actions 运行记录** | Run #84 (ID: `40210926790`) | 🟢 **Both Jobs Success** (`build (rootless)` & `build (roothide)`) |

---

## 三、当前会话中涉及的关键文件和路径

### 1. 核心工程源码文件
* **[Tweak.x](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Tweak.x)** (18,384 行，971,446 字节)
  - SHA256: `2280316F2F288F3E2DCBB8679796EBEF4715AD2EA85689EB7B3E9192F5FFD6D1`
* **[layout/DEBIAN/postinst](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/layout/DEBIAN/postinst)** (362 字节)
* **[.github/workflows/build.yml](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/.github/workflows/build.yml)** (1,757 字节)
* **[AngelCameraHelper.m](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/AngelCameraHelper.m)** (17,191 字节)
* **[Makefile](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Makefile)** (1,414 字节)

### 2. 外部镜像与归档文件（四向字节级完全对齐）
* **[主修实时翻译.txt](file:///D:/主修实时翻译.txt)**
  - SHA256: `2280316F2F288F3E2DCBB8679796EBEF4715AD2EA85689EB7B3E9192F5FFD6D1`
* **[主修实时翻译_2.txt](file:///D:/主修实时翻译_2.txt)**
  - SHA256: `2280316F2F288F3E2DCBB8679796EBEF4715AD2EA85689EB7B3E9192F5FFD6D1`
* **[D:\天使siri测试版\Tweak.x](file:///D:/天使siri测试版/Tweak.x)**
  - SHA256: `2280316F2F288F3E2DCBB8679796EBEF4715AD2EA85689EB7B3E9192F5FFD6D1`
* **[D:\定位与防检测底部代码.txt](file:///D:/定位与防检测底部代码.txt)** (950 行，36,011 字节)

### 3. 最新编译产出的两套安装包（Run #84）
* **Dopamine 版 (Rootless / arm64)**：
  - 本地路径 1：[D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 本地路径 2：[D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 本地路径 3：[C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123\packages\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 文件大小：`405,234` 字节
  - SHA256：`0B3F38B054E3B7375E3E995BC6D0DF8036C8C187F2D8DFA5077BCE4F1D8662F5`
* **Relaxin 版 (RootHide / arm64e)**：
  - 本地路径 1：[D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 本地路径 2：[D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 本地路径 3：[C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123\packages\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 文件大小：`264,220` 字节
  - SHA256：`F1B41877B60CCB717C48A92DE606AAB84EE9D0EC9FB601C577B6AC21E6FBFCDE`

---

## 四、工作状态分类表

| 状态 | 工作项 | 对应位置 / 备注 |
| :---: | :--- | :--- |
| **已完成** | 6 处反风控与隐身强化核心修改 | `Tweak.x` 全部 6 处修改精准落盘 |
| **已完成** | 源码四向同步与校验和一致性核验 | `Tweak.x`（两处工程库 + 两处 D 盘备份）SHA256 完全对齐 |
| **已完成** | Git 提交与远程推送 | Commit `f17822d` 推送至 `origin/main` |
| **已完成** | GitHub Actions 双矩阵云端构建 | Run #84 (ID: `40210926790`) 🟢 Rootless & Roothide 全部成功构建 |
| **已完成** | 双格式安装包自动下载与多端归档 | Dopamine arm64 版与 Relaxin arm64e 版已同步落盘至 D 盘根目录、`D:\天使siri测试版` 及 packages |
| **待验证** | 越狱真机安装实测 | 用户在真机上验证大厂 App 反风控去特征、0.95s 锁帧稳定性与真实打卡通过率 |
