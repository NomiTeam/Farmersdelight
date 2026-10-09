# 序列化调试与代码清理验证（2026-10-10）

范围：序列化/锁/holder 采样、配方显示空图标修复、官方 Paper/Bukkit API 简化，以及八个自有工程的导入和注释整理。工作区说明见 [审查报告](../../docs/序列化调试与代码清理-2026-10-10.md)。

## 构建与测试

各模块执行 `clean build`；FD 另外执行 `clean build -PdebugTools=true`，归档 debug JAR 和 XML 后恢复默认发行构建。默认 CE 26.9.1、Paper 1.21.5；主线使用 Java 21，VD nms-26 使用 Java 25 toolchain。附属 composite build 与 FD 串行执行。

| 模块 | suites | JUnit | 失败 / 错误 / 跳过 |
| --- | ---: | ---: | --- |
| FD 默认发行 | 176 | 912 | 0 / 0 / 0 |
| FD debug（单独口径） | 179 | 924 | 0 / 0 / 0 |
| BAC | 20 | 101 | 0 / 0 / 0 |
| CAB | 18 | 78 | 0 / 0 / 0 |
| BBQD | 5 | 18 | 0 / 0 / 0 |
| ED | 3 | 11 | 0 / 0 / 0 |
| FDAddonTemplate | 3 | 4 | 0 / 0 / 0 |

六模块发行合计 225 suites / 1124 项 JUnit；debug 的 924 项不能再叠加为独立功能数量。统计读取最终 `TEST-*.xml`，普通 `jar SKIPPED`、无源码任务不算跳过 JUnit。

ExpandedDelight 未改源码，本轮重新构建通过，无 JUnit。VD core、nms-1.21.5、nms-1211、nms-26、launcher 均构建通过；core 的 `checkFoodRules`、`checkCraftMirror`、`checkBehaviorWithdraw` 三项断言通过，没有 JUnit 用例。launcher 自检确认入口仍是 Java 21 class major 65，三个版本的行为类分别保留。过程中曾提前启动 VD nms-26，已在任务执行前中止，待 nms-1211 完成后重新串行构建成功。

全部最终构建日志没有 Java 编译器过时 API 警告。VD core 的 Gradle 8.14.2 仍报告既有的测试框架依赖自动加载、无测试执行弃用提示；其 `core/build.gradle.kts` 是受保护的用户改动，本轮未修改。用户提供的 GitHub CI 片段没有最终失败堆栈，本轮验证的是本地构建，没有触发远端 CI。

## 回归覆盖

- `RecipeIngredientDisplayTest`：null / 空原料返回无真实图标，显示入口据此选择屏障；正常图标的复制不改变原物品。离线 fixture 不依赖服务器 RegistryAccess，不覆盖真实客户端显示。
- `PerformanceProbeTest`：未采样不创建 Sample，holder 始终传入 `false`；Sample 在异常路径关闭且重复关闭仅记录一次。
- `PerformanceMonitorTest`：serialization 组只包括四项新指标，停止后的旧 recorder 不能写入新会话。
- debug `DebugToolsCommandTest`：profile / serialization 补全，保留既有命令与双语帮助测试。
- `ScheduledTaskAdapterTest`：null 任务为 NOOP，五种官方执行状态的取消判断与取消委托。该测试不模拟实际 Folia 调度器。
- 既有 CAB 同区域事务回归覆盖 API-only 类路径下的 holder 调用；新增 API 通过 `PluginAccess` 取得实现，没有新增直接静态服务查找。
- 既有手持烤串行为回归覆盖点击受理/拒绝、双方使用结果、消耗与进度清理；主处理器改为 HIGHEST，MONITOR 观察器保持只读。

没有为纯导入或注释变化新增镜像测试。已有反射预算下调；公开兼容 API 的签名保留，内部转发类移除。

## 校验与产物

FD `python tools/run_all_checks.py --quiet` 10/10 通过。858 个自有 Java 文件的简单名称扫描中，未使用导入、重复导入、注释 HTML/Javadoc 内联标记均为 0；未修改行的原字节与行尾检查为 0。该扫描不是完整 IDE 语义分析。

CE 符号矩阵覆盖以下 14 个本轮产物：FD API、发行、归档 debug；BAC、CAB、BBQD、ED、模板、ExpandedDelight；VD core、三份 NMS、launcher。共引用 201 个 CE 类、621 个成员。

| CE 版本 | 缺类 | 缺成员 | 抽象实现缺口 |
| --- | ---: | ---: | ---: |
| 26.8.2 | 0 | 0 | 0 |
| 26.9 | 0 | 0 | 0 |
| 26.9.1 | 0 | 0 | 0 |
| 26.9.2 | 0 | 0 | 0 |
| 26.10-SNAPSHOT | 0 | 0 | 0 |

这是 JVM 描述符检查，不验证 CE 世界/物品实际行为、VD 的 NMS 跨版本运行或其它插件事件顺序。主线仍编译 CE 26.9.1，没有静态调用 26.10 专有能力。

VD 五项原有用户文件 SHA256 与保护基线一致；仅提交四个文件的未用导入删除。VD 产物包含其既有工作树修改，不能当作未改基线发行包。模板五项注释整理已构建验证，保留到下一次同仓实质改动提交；避免单独增加注释提交。

各修改仓库 `git diff --check`、Wiki 全量相对链接、工作区入口与新文档链接检查通过。证据在工作区 `.codex/tmp/fd-serialization-debug-20261009/`：最终构建日志、XML/计数、产物 SHA256、CE 矩阵、清理与保护哈希记录。既有历史不重写，各实质修改仓库本轮一次本地提交。

## 未覆盖的实机边界

未启动服务端、重启真实存档、进行配方书点击、Folia 多区域调度或 spark/MSPT 压测；未推送、部署或覆盖服务器 JAR。profiling 只观察现有真实执行路径，不主动制造保存负载；P95 使用最多 4096 个最近样本，嵌套指标不能相加。实际性能收益和与其它 HIGHEST 监听器的先后关系仍需实机核对。
