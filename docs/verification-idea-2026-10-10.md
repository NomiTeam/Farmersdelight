# IDEA 检查修复验证（2026-10-10）

范围：FD 的空性契约、可变值读取、拆餐包装、内部冗余状态、日志和测试修复，以及工作区文档入口整理。详情见工作区 [代码质量记录](../../docs/IDEA检查修复与代码质量-2026-10-10.md)和[六条注意事项](../../docs/开发注意事项.md)。本轮没有重建或修改附属业务代码。

## 构建与 JUnit

Java 21，Paper 1.21.5，默认 CE 26.9.1；所有 Gradle 命令串行执行。

| 构建 | suites | tests | failures / errors / skipped |
| --- | ---: | ---: | --- |
| FD `clean build -PdebugTools=true` | 179 | 924 | 0 / 0 / 0 |
| FD `clean build --offline`，恢复发行输出 | 176 | 912 | 0 / 0 / 0 |
| 最后整理新增导入位置及行尾后 `build --offline` | 176 | 912 | 0 / 0 / 0 |
| 静态查找预算下调后，单独验证 StaticServiceLocatorBudgetTest | 1 | 2 | 0 / 0 / 0 |

首次在线构建已下载修补依赖。末次只调整新增导入位置及新增行的行尾，API 和 plain JAR 的 SHA256 与 clean build 一致；发行测试重新执行。两次 debug clean build 均通过，表中记录最终一次。没有弃用警告。Gradle 禁用的普通 jar 任务显示 SKIPPED，不是 JUnit 跳过；实际 XML 的 skipped 为零。

沿用厨锅经验结算、库存并发、手持消耗和控制器范围回归；shutdown 断言已加强为比较前后关闭次数，两项文件枚举测试增加流清理。静态插件查找实测 52 处，将原上限 54 降为 52，并单独验证两项护栏；全量 XML 已提前归档，单项运行不覆盖全量证据。未增加仅复述实现的测试。

## 依赖与产物

- `dependencyInsight --configuration testRuntimeClasspath --dependency log4j-api --offline` 确认 Paper 带入的 2.24.1 被固定为 2.25.5。
- `dependencies --configuration runtimeClasspath --offline` 仅含 AntiGriefLib 和 bStats；Log4j 不在发行运行依赖图。
- FD 发行 shadow、plain、API 及归档 debug JAR 均无 `org/apache/logging/log4j/` 类；修补不替换服务器自己的日志库。
- 默认生成的两份 BuildFlags 已恢复 `DEBUG_TOOLS = false`；debug JAR 和对应 XML 先归档，再恢复默认构建。
- CE 26.8.2 / 26.9 / 26.9.1 / 26.9.2 / 26.10-SNAPSHOT 检查 14 个产物，201 个 CE 类、621 个成员引用，缺失类、成员和抽象缺口均为零。其中 FD runtime/API/debug 为本轮产物，另外 11 个为上一轮既有产物，不代表本轮重建过附属。

## 静态与文档核对

`python tools/run_all_checks.py --quiet` 10/10 通过，覆盖语言键、API 边界、配置路径、YAML 重复键、方块状态占用、客户端键、生成配置、脚本自测和验证矩阵。

69 个源码/构建文件的原件逐文件备份；未修改行的原字节及行尾比较无差异，`git diff --check` 通过。VD 五项原有用户文件的 SHA256 未变。工作区 README、维护说明、文档索引、注意事项和本轮报告共核查 45 项本地链接，零断链；历史报告保留原路径，不复制 Wiki 使用文档。

证据位于工作区 `.codex/tmp/idea-audit-20261010/`：构建日志、debug/release/final 测试汇总与 XML、依赖图、产物 SHA256、五版符号矩阵、原始 IDEA 分类清单、字节及用户文件保护检查。

本轮未重新运行 IDEA 全量检查，也未启动 Paper/Folia 服务端、执行真人重启/压力验收、推送或部署。IDEA 导出的大量拼写、框架入口、公共 API 和风格提示保留处理依据，不宣称全部提示归零。
