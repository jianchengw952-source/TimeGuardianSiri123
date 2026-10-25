# TimeGuardianSiri123 项目会话记忆文件 (SESSION_MEMORY.md)

> **生成时间**：2026-10-25 23:18:00 (本地时间)  
> **会话标识**：`02de72ea-60df-4738-8977-4a4ebaf78b2f`  
> **用途说明**：供用户切换 Google Antigravity 账号或开启新会话后，直接读取本文件无缝继承当前项目的完整开发上下文。

---

## 一、当前任务目标

1. **项目核心定位**：
   - 开发与维护 iOS 15.0 ~ 16.5+ 越狱环境下的全功能插件 **TimeGuardianSiri123**（包名：`com.custom.timeguardiansiri123`，版本号：`6.0.0`）。
   - **双越狱架构原生无缝兼容**：同时支持 **Dopamine (Rootless `/var/jb`)** 与 **Relaxin (RootHide 随机根目录 `/var/containers/Bundle/Application/.jbroot-*`)**。
   - 包含四大核心体系：
     1. **全硬件级底层定位伪装**：直接拦截并改写 `CLDaemonLocation` 私有结构体，支持真实运动轨迹模拟、卡尔曼滤波平滑漂移、多进程内核级状态广播（通过 `notifyd` 纳秒级共享内存同步）。
     2. **零闪退反检测隐身护盾**：针对大厂与考勤 App，彻底剔除破坏性 C 级文件钩子，采用 `dladdr` 动态内存隐藏、虚拟 Wi-Fi（200 米网格锚点防跳变）、1Hz GNSS 硬件采样对齐与真实传感器合成。
     3. **天使守护 Siri 悬浮工作台**：全局浮动交互 UI、多主题液态镜面玻璃渲染、双麦克风语音监听与 Edge TTS 语音合成。
     4. **独立相机辅助工具**：`AngelCameraHelper` 独立守护进程与虚拟视频流注入。

2. **最新应用的 4 大核心定位与防纠偏精度修复**：
   - **【修改点 1】防止内核旧坐标覆盖 `.plist` 新坐标**：
     - 在 `reloadGeminiPrefsCache(void)` 尾部内核读取时，仅当 merged 中对应键为 nil 时才做兜底赋值，且仅当本地配置经纬度为 0（未设置）且内核坐标非 0 时才赋值，彻底解决设置面板修改新坐标被内核旧缓存冲掉导致不生效的问题。
   - **【修改点 2】消除客户端二次叠加漂移，确保米级精准**：
     - 在 `agGetCurrentSpoofedTelemetry` 中，移除客户端独立的卡尔曼微漂移叠加（SpringBoard 主控端已以 1Hz 频率计算好平滑微漂移并写入内核），客户端直接读取并使用内核坐标，严禁二次叠加漂移导致偏位。
   - **【修改点 3】校准设置面板高德地图选点准星的逆地理编码偏差**：
     - 在 `AGLocationSelectViewController` 的 `mapView:regionDidChangeAnimated:` 中，MapKit 内部 `centerCoordinate` 为火星坐标（GCJ-02），在送入系统逆地理编码 `reverseGeocodeLocation:` 前先经由 `agGCJ02ToWGS84` 转回 WGS-84 真实地理坐标，彻底消除 500 米偏差。
   - **【修改点 4】升级 `%group StealthLocationClientHooks` 根除二次纠偏与增强代理拦截**：
     - 拦截多参数 `initWithCoordinate:...`（5/7/9/10 参数），对第三方地图 SDK（高德、腾讯、MapKit）生成的坐标打标为 `kAGAppCreatedCustomLocationKey`，`-coordinate` 遇到打标对象直接返回 `%orig`，根除 300~600m 二次纠偏偏差。
     - 新增底层 `-rawCoordinate` 与 `-rawCourse` 拦截，杜绝私有属性穿帮。
     - 拦截 `MKUserLocation.location` 与 `MKUserLocation.coordinate`，国内返回 GCJ-02 火星坐标，系统地图蓝点完美对齐。
     - 代理 Swizzle 支持标准类与 `NSProxy` 动态消息转发（`forwardInvocation:`），完美拦截钉钉、企微、RxSwift、Flutter 代理转发。
     - 引入 `#import <objc/message.h>` 安全引用 `_objc_msgForward`。

---

## 二、当前项目及工作目录

| 目录类型 | 绝对路径 | 说明 |
| :--- | :--- | :--- |
| **本地工作目录** | `C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123` | 当前活跃的 Git 仓库根目录，包含完整源码与构建配置 |
| **D盘归档目录** | `D:\天使siri测试版` | 完整备份目录，包含 `.git` 库、源码及最新双版本 `.deb` |
| **远端 Git 仓库** | `https://github.com/jianchengw952-source/TimeGuardianSiri123.git` | GitHub 远程仓库（分支：`main`） |
| **当前 Git Commit** | `1b48cb4` | `fix(loc): prevent kernel coords from overwriting plist, eliminate client secondary drift, calibrate mapView geocode, and support NSProxy forwardInvocation` |
| **GitHub Actions 运行记录** | Run #79 (ID: `40206049255`) | 🟢 **Both Jobs Success** (`build (rootless)` & `build (roothide)`) |

---

## 三、当前会话中涉及的关键文件和路径

### 1. 核心工程源码文件
* **[Tweak.x](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Tweak.x)** (18,449 行，975,364 字节)
  - SHA256: `78D828EC4144A1C091BCA981EB89CE695346AC8EBB7F5EDDADA6F96938F786CA`
* **[layout/DEBIAN/postinst](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/layout/DEBIAN/postinst)** (362 字节)
* **[.github/workflows/build.yml](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/.github/workflows/build.yml)** (1,757 字节)
* **[AngelCameraHelper.m](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/AngelCameraHelper.m)** (17,191 字节)
* **[Makefile](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Makefile)** (1,414 字节)

### 2. 外部镜像与归档文件（四向字节级完全对齐）
* **[主修实时翻译.txt](file:///D:/主修实时翻译.txt)**
  - SHA256: `78D828EC4144A1C091BCA981EB89CE695346AC8EBB7F5EDDADA6F96938F786CA`
* **[主修实时翻译_2.txt](file:///D:/主修实时翻译_2.txt)**
  - SHA256: `78D828EC4144A1C091BCA981EB89CE695346AC8EBB7F5EDDADA6F96938F786CA`
* **[D:\天使siri测试版\Tweak.x](file:///D:/天使siri测试版/Tweak.x)**
  - SHA256: `78D828EC4144A1C091BCA981EB89CE695346AC8EBB7F5EDDADA6F96938F786CA`
* **[D:\定位与防检测底部代码.txt](file:///D:/定位与防检测底部代码.txt)** (923 行，36,603 字节)

### 3. 最新编译产出的两套安装包（Run #79）
* **Dopamine 版 (Rootless / arm64)**：
  - 本地路径 1：[D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 本地路径 2：[D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 本地路径 3：[C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123\packages\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64.deb)
  - 文件大小：`407,658` 字节
  - SHA256：`7BB141E492EDFB74405AF65EFE7550848070253554C9A2A05AEC90E90DCF433A`
* **Relaxin 版 (RootHide / arm64e)**：
  - 本地路径 1：[D:\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 本地路径 2：[D:\天使siri测试版\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///D:/天使siri测试版/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 本地路径 3：[C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123\packages\com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/packages/com.custom.timeguardiansiri123_6.0.0_iphoneos-arm64e.deb)
  - 文件大小：`266,028` 字节
  - SHA256：`DB240405770C7F8E794158F0FCBD02DED62D8FC5463454650DCE13890818E53C`

---

## 四、工作状态分类表

| 状态 | 工作项 | 对应位置 / 备注 |
| :---: | :--- | :--- |
| **已完成** | 4 处底层定位与防二次纠偏核心代码替换 | `Tweak.x` 全部 4 处精确应用 |
| **已完成** | 源码四向同步与校验和一致性核验 | `Tweak.x`（两处工程库 + 两处 D 盘备份）SHA256 完全对齐 |
| **已完成** | Git 提交与远程推送 | Commit `1b48cb4` 推送至 `origin/main` |
| **已完成** | GitHub Actions 双矩阵云端构建 | Run #79 (ID: `40206049255`) 🟢 Rootless & Roothide 全部成功构建 |
| **已完成** | 双格式安装包自动下载与多端归档 | Dopamine arm64 版与 Relaxin arm64e 版已同步落盘至 D 盘根目录、`D:\天使siri测试版` 及 packages |
| **待验证** | 越狱真机安装实测 | 用户在真机上验证选点精准度、二次纠偏根除及 NSProxy 消息拦截稳定性 |
