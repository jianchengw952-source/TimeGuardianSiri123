# TimeGuardianSiri123 项目会话记忆文件 (SESSION_MEMORY.md)

> **生成时间**：2026-10-25 22:50:00 (本地时间)  
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

2. **最新应用的 5 大核心底层稳定性与逻辑修复**：
   - **【修改点 1】修复跨进程配置缓存覆盖 Bug**：
     - 在 `reloadGeminiPrefsCache(void)` 中，调整 `globalShared` 遍历逻辑，仅对未定义键进行兜底赋值，且将 `newestDict`（最新磁盘文件）作为最终最高优先级覆盖，杜绝白名单与坐标被旧缓存冲刷掉。
   - **【修改点 2】消除 `_internal` 裸结构体内存强写风险与关联对象安全标记**：
     - 废除无锁/有锁集合维护指针地址，改为基于 `objc_setAssociatedObject` 的零递归、生命周期自动清理的打标机制（`kAGAppCreatedCustomLocationKey` 与 `kAGHardwareCleanLocationKey`）。
     - 禁用 arm64/arm64e 内存对齐敏感的裸结构体强写（`agSanitizeCLLocationInternalStruct` 变更为无害占位，所有防检测指标改由安全 Hook 提供）。
   - **【修改点 3】纯 C 零递归定位状态源 `AGSpoofedTelemetry` 与考勤隐蔽预设兜底**：
     - 引入 `AGSpoofedTelemetry` 纳秒级轻量结构体与 0.90s 状态缓存（`sTelemetryCacheLock`），解除 `constructRealisticSpoofedLocation` 重复调用引发的递归与计算开销。
     - `enableAttendanceStealthPreset` 增加白名单自动补全（若白名单模式未配置应用，自动填充钉钉、企微、飞书、微信、美团、高德、百度等）。
   - **【修改点 4】重写 `%group StealthLocationClientHooks` 杜绝 CLLocation 递归与完善距离/地图拦截**：
     - `CLLocation` 内部所有属性 Hook 检测 `agIsAppCustomLocationPtr` 与 `agIsCleanLocationPtr`，直接返回 `%orig`，杜绝调用栈无限循环爆栈。
     - 新增底层 `-distanceFromLocation:` 与 `-getDistanceFrom:` 拦截，根除钉钉/美团直接读取私有结构体计算考勤打卡距离的穿帮破绽。
     - 新增 `%hook MKUserLocation` 拦截系统地图原生定位蓝点。
     - 使用 `Method` 指针集合 `sSwizzledMethodsSet` 精确去重，杜绝继承链多次包装代理回调。
   - **【修改点 5】修复 `hooked_ag_dladdr` 死锁闪退**：
     - 移除在 `dladdr` 内部调用 `dlsym` 的高危锁死逻辑；改为在 `agInstallDladdrStealthHookIfNeeded` 的 `dispatch_once` 中一次性预缓存 `sCachedCleanCLDlInfo`，Hook 内仅做原子级结构体值拷贝，零锁开销、零死锁风险。

---

## 二、当前项目及工作目录

| 目录类型 | 绝对路径 | 说明 |
| :--- | :--- | :--- |
| **本地工作目录** | `C:\Users\57132\.gemini\antigravity\scratch\TimeGuardianSiri123` | 当前活跃的 Git 仓库根目录，包含完整源码与构建配置 |
| **D盘归档目录** | `D:\天使siri测试版` | 完整备份目录，包含 `.git` 库、源码及最新双版本 `.deb` |
| **远端 Git 仓库** | `https://github.com/jianchengw952-source/TimeGuardianSiri123.git` | GitHub 远程仓库（分支：`main`） |

---

## 三、关键文件和路径

### 1. 核心工程源码文件
* **[Tweak.x](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Tweak.x)** (18,335 行)
  - 核心修改已完全应用
* **[layout/DEBIAN/postinst](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/layout/DEBIAN/postinst)** (362 字节)
* **[.github/workflows/build.yml](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/.github/workflows/build.yml)** (1,757 字节)
* **[AngelCameraHelper.m](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/AngelCameraHelper.m)** (17,191 字节)
* **[Makefile](file:///C:/Users/57132/.gemini/antigravity/scratch/TimeGuardianSiri123/Makefile)** (1,414 字节)

### 2. 外部镜像与归档文件（四向字节级完全对齐）
* **[主修实时翻译.txt](file:///D:/主修实时翻译.txt)**
* **[主修实时翻译_2.txt](file:///D:/主修实时翻译_2.txt)**
* **[D:\天使siri测试版\Tweak.x](file:///D:/天使siri测试版/Tweak.x)**
* **[D:\定位与防检测底部代码.txt](file:///D:/定位与防检测底部代码.txt)** (790 行，提取用于大模型调试)
