# 问题修复与附属复查验证（2026-10-09）

对应工作区报告：[修复与附属复查](../../docs/问题修复与附属复查-2026-10-09.md)。本轮是修复前 15 项审查建议后的验证，不沿用历史测试计数。Gradle 构建串行执行，Java 21，默认 CE 26.9.1 / Paper 1.21.5 编译下限。

## 完整构建与测试

| 模块 | suites | JUnit tests | 失败 / 错误 / 跳过 |
| --- | ---: | ---: | --- |
| FarmersDelight | 173 | 901 | 0 / 0 / 0 |
| BrewinAndChewin | 20 | 101 | 0 / 0 / 0 |
| BarbequesDelight | 5 | 18 | 0 / 0 / 0 |
| CrabbersDelight | 18 | 78 | 0 / 0 / 0 |
| EndsDelight | 3 | 11 | 0 / 0 / 0 |
| FDAddonTemplate | 3 | 4 | 0 / 0 / 0 |
| FD debug（替代发行模式） | 176 | 912 | 0 / 0 / 0 |

发行与五个附属/模板合计 1113 项 JUnit；debug 是另一种编译模式，不与发行测试数重复累加。各模块 clean build 均通过。FD、BAC、CAB、ED 的最终编译输出没有弃用 API 警告；API 更新后的 BBQD、模板完整构建也已通过。默认 FD 发行产物已经恢复，debug JAR 另存证据目录。

ExpandedDelight 在 Reference 中没有 Git，也没有 JUnit 来源；其 clean build 成功不作为测试数。构建路径、CE core 依赖、Paper 清单下限已修复，原三项构建配置有备份。VD core 使用缓存 Gradle 8.14.2 clean build，现有三个 JavaExec 断言检查通过：food rules、CraftItemStack mirror、三个层级的 behavior withdrawal。它们不是 JUnit；不要将无 JUnit 来源算作零跳过 JUnit。误用 FD 的 Gradle 9 wrapper 时 VD 的 test 因未发现 JUnit 失败，改用兼容的 Gradle 8 完整重跑，未修改用户构建文件或关闭测试。

## 回归与检查

覆盖列表在事务锁内按当前文件合并；坏 YAML 不被覆盖；厨锅/砧板读者只见完整一代；草稿隔离、限额及旧回调身份；controller 替换后的撤销保护；便签相同 ID 不同文本保护；启动恢复预算和模板草稿复制均有针对性验证。CE 交互接入回归核对负坐标、局部命中位置和无命中/空气分支，保留既有手持事件与策略测试。

`python tools/run_all_checks.py --quiet` 10/10 全通过，六个模块 JUnit XML 门禁均通过。检查涵盖语言、资源、API 边界、重复 YAML 键、显式方块状态占用、CE 配置注释与工具自测。Wiki 全量相对链接和根入口/最新报告链接检查通过，Git diff --check 通过。

## CE 五版符号

10 个产物（FD 发行/API/debug、BBQD、BAC、CAB、ED、模板、ExpandedDelight、VD core）检查 CE 26.8.2、26.9、26.9.1、26.9.2、26.10-SNAPSHOT。实际引用 201 个 CE 类、617 个成员；五版缺类、缺成员、抽象实现缺口均为 0。交互 helper 没有引入 CE 26.10 专属静态调用；版本、产物列表和完整日志见证据目录。

VD 五项原有用户文件的 SHA256 与基线全部一致；没有暂存或提交这些修改。保护 provider 在 26.8.2 与后续版本之间存在返回类型重定位差异，未直接改为依赖 CE getter。

## 证据与运行边界

工作区证据目录 `scratchpad/fd-fixes-addons-20261009/` 保存最终发行计数、debug XML/JAR、五版符号日志、产物 SHA256 与文档链接结果。较早构建的记录及 ExpandedDelight 配置原件备份位于 `C:/Users/HuiDu_OwO/.codex/tmp/fd-fixes-addons-20261009/`。上轮便签持久化的修复前失败 XML 仍在 `fd-experience-audit-20261009/`；不把它说成本轮新跑的红灯测试。

未运行真实 Paper/Folia/CE 五版服务器、客户端、完整停服恢复或多人 TPS/MSPT 压测。部分 GUI/调度生命周期通过源码契约检查；CE 原版交互表、实际保护插件与区域边界仍需服务器验收。VD 背包编辑的新事务风险仅复查记录，业务未修改；NMS/launcher/UAAPI 未全量构建。未推送、部署或启动测试服。
