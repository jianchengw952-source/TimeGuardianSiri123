# TimeGuardianSiri123 项目会话记忆文件 (SESSION_MEMORY.md)

> **生成时间**：2026-10-26 02:14:00 (本地时间)  
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

2. **最新应用的 5 大防覆盖与原生 IMP 动态还原核心重构（Commit `3e1a07e`）**：
   - **【修改点 1】修复 `reloadGeminiPrefsCache` 反向覆盖漏洞**：
     - 加入 `if (!newestDict)` 严格守卫；
     - 仅当进程无法读取磁盘 plist（如受沙盒限制的第三方 App）时，才用内核寄存器状态填充；SpringBoard 与 Preferences 永远以磁盘最新 plist 为唯一权威源，杜绝定时器旧状态反向覆盖关闭指令。
   - **【修改点 2】守护进程与模拟定时器残留彻底消除**：
     - 开机关闭时向内核写 `isActive=NO` 并延迟 2.0s 强制清空 `locationd` 模拟队列；
     - `onSimulationTick` 检测到内核关闭立即刷新缓存并触发复位；
     - `pushSimulatedLocationToDaemon` 在关闭或非全局模式时无条件清空并停止模拟；
     - `stopAndResetToRealLocation` 末尾在 SpringBoard 内主动触发停止模拟。
   - **【修改点 3】`syncMasterStateFromPrefsAndRestartTimer` 状态刷新**：
     - 关闭分支同步调用 `agPublishSpoofStateToKernel` 刷新寄存器并清除残留。
   - **【修改点 4】严格隔离 `StealthLocationClientHooks` 中的防检测方法**：
     - `sourceInformation`、`type`、`isSimulated`、`_isSimulated` 以及 `clientLocation` 中的模拟标志抹除仅在 `agIsAntiDetectionShieldActive()` 为真时生效，不开防检测绝不伪装，杜绝日常模式的任何不自然特征。
   - **【修改点 5】原生 IMP 保存与动态还原引擎（关闭虚拟定位时 100% 恢复原生函数表）**：
     - 启动时备份全部 40+ 个 CoreLocation/MapKit/CoreMotion 方法的苹果原生 IMP；
     - 关闭虚拟定位时通过 `method_setImplementation` 将每个方法 **100% 原地还原为苹果原生实现**，杜绝大厂反作弊扫描到非原生 IMP；
     - 仅在【虚拟定位开启 + 防检测开启】时，才挂载底层 C 函数（`access` / `stat` / `lstat` / `dladdr` / `_dyld_get_image_name` / `CNCopyCurrentNetworkInfo`）防检测 Hook。

---

## 二、当前项目及工作目录

| 目录类型 | 绝对路径 | 说明 |
| :--- | :--- | :--- |
| **本地工作目录** | `C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123` | 当前活跃的 Git 仓库根目录，包含完整源码与构建配置 |
| **D盘归档目录** | `D:\天使siri测试版` | 完整备份目录，包含 `.git` 库、源码及最新双版本 `.deb` |
| **E盘归档目录** | `E:\钉钉正常版本，最新防检测，关闭时提示插件问题待调` | 完整项目工程归档，含源码、配置及双版本 `.deb` |
| **E盘单文件归档** | `E:\钉钉正常版本，最新防检测，关闭时提示插件问题待调.txt` | 钉钉可打卡版本完整代码备份 |
| **远端 Git 仓库** | `https://github.com/jianchengw952-source/TimeGuardianSiri123.git` | GitHub 远程仓库（分支：`main`） |
| **当前 Git Commit** | `3e1a07e` | `fix(build): eliminate redefinition of moveMode in syncMasterStateFromPrefsAndRestartTimer` |
| **GitHub Actions 运行记录** | Run #92 (ID: `40220032273`) | 🟢 **Both Jobs Success** (`build (rootless)` & `build (roothide)`) |

---

## 三、当前会话中涉及的关键文件和路径

### 1. 核心工程源码文件
* **[Tweak.x](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Tweak.x)** (18,396 行，975,298 字节)
  - SHA256: `4A5AF97928256D4214B886A497E64A4DC457220544EBE2E7CCB0F7A90A0F00E2`
* **[layout/DEBIAN/postinst](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/layout/DEBIAN/postinst)** (362 字节)
* **[.github/workflows/build.yml](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/.github/workflows/build.yml)** (1,757 字节)
* **[AngelCameraHelper.m](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/AngelCameraHelper.m)** (17,191 字节)
* **[Makefile](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Makefile)** (1,414 字节)

### 2. 外部镜像与归档文件（全向字节级完全对齐）
* **[主修实时翻译.txt](file:///D:/主修实时翻译.txt)**
  - SHA256: `4A5AF97928256D4214B886A497E64A4DC457220544EBE2E7CCB0F7A90A0F00E2`
* **[主修实时翻译_2.txt](file:///D:/主修实时翻译_2.txt)**
  - SHA256: `4A5AF97928256D4214B886A497E64A4DC457220544EBE2E7CCB0F7A90A0F00E2`
* **[D:\天使siri测试版\Tweak.x](file:///D:/天使siri测试版/Tweak.x)**
  - SHA256: `4A5AF97928256D4214B886A497E64A4DC457220544EBE2E7CCB0F7A90A0F00E2`
* **[E:\主修实时翻译.txt](file:///E:/主修实时翻译.txt)**
  - SHA256: `4A5AF97928256D4214B886A497E64A4DC457220544EBE2E7CCB0F7A90A0F00E2`
* **[D:\定位与防检测底部代码.txt](file:///D:/定位与防检测底部代码.txt)** (868 行，35,320 字节)

### 3. 最新编译产出的两套安装包（Run #92）
* **Dopamine 版 (Rootless / arm64)**：
  - 本地路径 1：[D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 本地路径 2：[D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 本地路径 3：[C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123\packages\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 文件大小：`405,650` 字节
  - SHA256：`E2A275E92AB755034363E81B0CB05A948047CCE26976B3008E66DAA02B5CEBF4`
* **Relaxin 版 (RootHide / arm64e)**：
  - 本地路径 1：[D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 本地路径 2：[D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 本地路径 3：[C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123\packages\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 文件大小：`264,660` 字节
  - SHA256：`976D72B87AD8823B45390369E8AB9CF701196E28929B3D970270EE4296D44982`

---

## 四、工作状态分类表

| 状态 | 工作项 | 对应位置 / 备注 |
| :---: | :--- | :--- |
| **已完成** | 5 处状态防覆盖与原生 IMP 动态还原重构 | `Tweak.x` 全部 5 处修改精准落盘 |
| **已完成** | 源码全向同步与校验和一致性核验 | `Tweak.x`（多端本地备份）SHA256 完全对齐 (`4A5AF9...`) |
| **已完成** | Git 提交与远程推送 | Commit `3e1a07e` 推送至 `origin/main` |
| **已完成** | GitHub Actions 双矩阵云端构建 | Run #92 (ID: `40220032273`) 🟢 Rootless & Roothide 全部成功构建 |
| **已完成** | 双格式安装包自动下载与多端归档 | Dopamine arm64 版与 Relaxin arm64e 版已同步落盘至 D 盘根目录、`D:\天使siri测试版` 及 packages |
| **待验证** | 越狱真机安装实测 | 用户在真机上验证关闭虚拟定位时 100% 恢复原生、无任何插件提示 |
