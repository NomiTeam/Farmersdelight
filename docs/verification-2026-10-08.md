# 本地构建与测试验证（2026-10-08）

来源：FD HEAD `6bc0adf`，本地源码未作业务修改；Java 21 编译目标，CraftEngine 默认依赖 26.9.1。日期代表本地验收，不代表这些版本已在服务器运行。

执行于 FarmersDelight 仓库：

```text
.\gradlew.bat build --offline --no-daemon --console=plain
.\gradlew.bat test --rerun-tasks --offline --no-daemon --console=plain
python tools/run_all_checks.py --quiet
```

- 两次 Gradle 命令均成功；第二次明确重跑 test，避免把 UP-TO-DATE 误记为重新执行。
- 从 `build/test-results/test/TEST-*.xml` 汇总：164 suites、865 tests、0 failures、0 errors、0 skipped。
- 总校验入口 10/10：8 个只读契约检查、工具自测、验证矩阵。文档整理后再次运行同一入口，结果仍为 10/10。
- 生产编译有 7 处弃用警告；构建成功并不代表跨区域交互时序已验证。

工作区其余模块随后串行构建：BBQD 25、BAC 99、CAB 64、ED 11、模板 3 项 JUnit，均无失败/错误/跳过；与 FD 合计 1067 项。VD core 的 `checkFoodRules`、`checkCraftMirror`、`checkBehaviorWithdraw` 已执行通过，属于 JavaExec 断言检查。

原始附属构建日志在 `C:/Users/HuiDu_OwO/.codex/tmp/fd-quality-audit-20261008/`，并归档于工作区文档清理备份的 `audit-evidence/`。XML 是可重建产物，本记录保存汇总与命令。

未启动服务端、未进行真人客户端冒烟、未进行负载测试，未重建 VD 的 NMS/launcher、UAAPI 或所有 CE 版本矩阵。本记录不为这些场景提供“通过”证据。

输出核对：整理后最终使用上方标准 Python 命令，完整 10/10 且无 reader thread 异常。另一次仅对父进程加 -X utf8 的尝试出现父子编码不一致，未把该次有异常的输出用作最终验收。

## 容器修复后的验证

本次验证针对修改后的 Java 实现，未复用首次审查的测试数量。FD 执行 `gradlew clean build --console=plain`；最后一次 GUI 内核调整后再执行 `gradlew build --console=plain`。BBQD、BAC、CAB、ED、模板串行 build，CAB 最终追加拖拽生命周期和保留 Inventory 引用的回归后再次 build。

| 模块 | suites | tests | failures / errors / skipped |
| --- | ---: | ---: | --- |
| FD | 165 | 869 | 0 / 0 / 0 |
| BBQD | 6 | 25 | 0 / 0 / 0 |
| BAC | 19 | 99 | 0 / 0 / 0 |
| CAB | 16 | 72 | 0 / 0 / 0 |
| ED | 3 | 11 | 0 / 0 / 0 |
| 模板 | 2 | 3 | 0 / 0 / 0 |
| 合计 | 211 | 1079 | 0 / 0 / 0 |

VD core 重新执行 `gradlew build --rerun-tasks --console=plain`，三项 JavaExec 断言通过；五个原有用户修改文件均与首次审查 SHA256 一致。未构建 NMS/launcher/UAAPI。

新增回归：`ContainerGuiBaselineTest` 验证独立 view、基线 clone、连续成功提交及持久 kernel 快照回绘；CAB `NativeHopperContainerTest` 验证原地增量、当前 store 更新、提取及取消回滚、方向过滤和燃料容量；`ContainerClickTransactionTest` 验证取出/放入数量守恒、双击及部分放置拒绝无效鱼饵，以及延迟拖拽前关闭 view 或更换光标时零提交。`ContainerCommitRegionTest` 以会抛错的 Inventory 证明非 owner 提交不访问 view；两个区域派发测试的失效/拒绝任务路径均为零 store 写入。

FD 的配方按钮改为下一 tick 切换视图；FD/CAB 接受的编辑同步提交，跨 owner 拒绝并关闭。删除原 CAB pending/临时快照实现及其旧测试，用新的实际行为测试替代。独立 Wiki 的中英 API 和服主漏斗说明已同步；相对链接检查零断链。FD 最终 `python tools/run_all_checks.py --quiet` 为 10/10，Wiki 相对链接检查为 0 断链。

构建日志、JUnit 汇总和后续 CE 符号检查保存在仓库外 `C:/Users/HuiDu_OwO/.codex/tmp/fd-container-fix-20261008/`。参考 CE 26.9.2 源码，编译基线仍是 26.9.1。未启动 Paper/Folia 服务端；红石、真实 InventoryMoveItemEvent/保护插件组合、世界卸载/重启与双玩家操作仍须实机验证。

CE 符号审计：对最终 FD/CAB JAR 检查 CE 26.8.2、26.9、26.9.1，三版均为 missing classes = 0、missing members = 0、abstract gaps = 0。使用 Gradle 缓存中的三个对应 Maven 制品显式传入检查器；这不等同于启动三版服务端，也未声称验证了 CE 26.9.2 正式发行 JAR。
