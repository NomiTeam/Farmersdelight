# CE Wiki 与厨锅经验核对验证（2026-10-09）

对应工作区[当前状态与经验语义报告](../../docs/CEWiki对照与项目状态-2026-10-09.md)。本轮修改厨锅经验触发、持久化和移除结算，以及配置 skill / 双语 Wiki；没有重新移植内容缺口或接入睡眠。构建使用 Java 21、默认 CE 26.9.1 / Paper 1.21.5，下限保留 CE 26.8.2。

## 构建与回归

| 模式 | suites | JUnit | 失败 / 错误 / 跳过 |
| --- | ---: | ---: | --- |
| FD 默认发行 clean build | 173 | 905 | 0 / 0 / 0 |
| FD debug clean build | 176 | 916 | 0 / 0 / 0 |

Gradle 严格串行。debug JAR 和 XML 已单独保存，最后再次默认 clean build 恢复发行产物及 BuildFlags。两种模式不重复累加测试数；附属本轮未改业务，不将上轮 212 项当作新构建结果。

CookingPotExperienceSettlementTest 共 8 项，本轮新增 4 项：手持容器取待盛装食物不领取，装碗转移不领取，经验 CompoundTag 往返后只领取一次，以及缺少旧数据清除陈旧内存台账。保留空槽/无效槽不结算、累计批次一次领取、自动化不清台账、并发领取不重复、非法配方经验不污染有效奖励等原有覆盖。

修复前“手持碗从待盛装槽领取经验”已复现，失败 XML 在个人临时目录。持久化回归使用真实 CE CompoundTag 与新厨锅实例验证经验 payload，未伪装成完整 CE 区块 codec / 世界重启测试；controller save/load 接线以及移除前结账顺序由编译和调用链核对验证。破坏、爆炸、CE 重载和客户端 Shift 仍需实机。

新增代码使用现有 CE NBT 和 Bukkit ExperienceOrb / Paper-Folia 调度；没有新增 26.10 静态依赖或弃用 API 回退。所有 Java 类型先 import 再用。

## 配置、文档与 skill

FD `python tools/run_all_checks.py --quiet` 本轮 10/10 通过：CE 注释、meal icons、语言、API 边界、路径、重复键、方块 state 占用、客户端键、工具自测及验证矩阵。

skill `craftengine-config` 按 creator 的 quick_validate 验证通过；Windows 执行使用 `python -X utf8`，没有更改验证器或禁用检查。速查内 5 段 YAML 全部可解析且零重复键，这不替代 CE 真实加载。

README、状态报告、本记录与 skill 的相对文件链接，以及玩家/服主 Wiki 全量相对文件链接检查通过。各受影响 Git 仓 diff --check 通过。VD 五项原有文件 SHA256 与基线一致，无暂存或修改；CE Wiki clone 保持 073d3d8d 且无修改。

## 五版 CE 符号

最终 10 个产物（FD 发行/API/debug、BBQD、BAC、CAB、ED、模板、ExpandedDelight、VD core）对照 CE 26.8.2、26.9、26.9.1、26.9.2、26.10-SNAPSHOT。共引用 201 个 CE 类、617 个成员；五版缺类、缺成员和抽象实现缺口均为 0。未改模块复用上轮相同产物；FD 与 debug 使用本轮新产物。

证据在工作区 `scratchpad/fd-cewiki-xp-20261009/`：发行/debug 测试计数、debug XML/JAR、symbol-matrix.json、完整五版日志、产物 SHA256 与链接检查。修复前失败 XML / 业务文件原件在 `C:/Users/HuiDu_OwO/.codex/tmp/fd-cewiki-xp-20261009/`。

## 实际边界

成品槽是领取触发点，金额以原模组累计配方批次为准，不能误称按取出份数分摊。移除只释放所配置的原版经验球，不凭空选玩家发技能。正常持久化与清账已补齐；升级前已经丢失的台账不从剩余成品反推。

没有运行真实 Paper/Folia/五版 CE 服务器、完整停服恢复、客户端或 TPS/MSPT 压测。经验球位置/合并、多配方小数随机分布、原模组配方解锁和 GUI 的完整客户端行为不属于已证明完全等价的范围。VD 背包事务风险尚未修改，睡眠仍是候选。没有推送、部署或更改服务器 JAR。
