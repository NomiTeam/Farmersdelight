# 实机与构建验证矩阵

本文件把"平台 × CraftEngine 版本 × 验证项"的结论集中在一处，每条都注明**证据来源**（测试全名 / 文档路径 / 实机记录）与**日期**。

口径：

- `结论` 只允许 `通过` / `未验证` / `不适用` 三种。
- `通过` 必须给出可核查的证据：`test:<测试类全名>`（该类必须存在于 `src/test/java`）或 `doc:<仓库内相对路径>`（该文件必须存在）。实机结论若只有口头记录，先记到仓库内 `docs/` 后再引用。测试行的 Paper/Folia 列表示目标契约，不代表本轮启动了对应服务端。
- 没有验证过的一律写 `未验证`，证据栏写 `-` 或 `manual:<待做事项>`，并由 `tools/check_verification_matrix.py` 汇总成清单。

| 平台 | 版本 | CE 版本 | 验证项 | 结论 | 证据来源 | 日期 |
| --- | --- | --- | --- | --- | --- | --- |
| Paper | 26.2 (Purpur) | 26.10-SNAPSHOT | 12 类 debug 放置、功能激活、附属配方与撤销；代理管理员、真实 CE 世界 71 项断言 | 通过 | doc:docs/verification-debug-targets-2026-10-11.md | 2026-10-11 |
| Folia | 26.2 | 26.10-SNAPSHOT | 新 debug 附属场景的真人跨区、保护和多人压力 | 未验证 | manual:复测 T28/T29，配合 spark | 2026-10-11 |
| Paper | 1.21.5 | 26.9.1 | CE 展示登记、隐藏更新、保留恢复、清表重登记、旧回调隔离与关闭竞态 | 通过 | test:com.huidu.farmersdelight.visual.ProxyDisplayCullableTest | 2026-10-10 |
| Folia | 1.21.5 | 26.9.1 | CE 剔除回调只使用 CE 网络与缓存，无 Bukkit 世界/容器读取 | 通过 | test:com.huidu.farmersdelight.visual.ProxyDisplayCullableTest | 2026-10-10 |
| Paper | 26.2 | 26.10-SNAPSHOT | 新接入 CE 遮挡的双人客户端与真实负载 | 未验证 | manual:按工作区手工清单 T34 复测遮挡、恢复、退出和卸载 | 2026-10-10 |
| Folia | 26.2 | 26.10-SNAPSHOT | 新接入 CE 遮挡的双人跨区客户端与真实负载 | 未验证 | manual:按工作区手工清单 T34 复测跨区玩家、遮挡和卸载 | 2026-10-10 |
| Paper | 1.21.5 | 26.9.1 | 分片注册只从轮次末尾发布（多来源共一轮、超预算全量可匹配） | 通过 | test:com.huidu.farmersdelight.recipe.RecipeRegistrationDriverTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 第一次装载（> 预算也）整批内联发布：启动时 manager 条数不为 0 | 通过 | test:com.huidu.farmersdelight.recipe.ColdRecipeLoadTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 分片预算：文件 ≤ 预算时不 arm 任务、超预算按预算推进 | 通过 | test:com.huidu.farmersdelight.recipe.RecipeRegistrationBudgetTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 预热与内容摘要在配方发布之后才跑（R1 契约，含趟代次守卫与失败跳过） | 通过 | test:com.huidu.farmersdelight.recipe.RecipePublicationWiringTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 发布失败时回滚 pot + 砧板 + 派生索引，保留上一版目录 | 通过 | test:com.huidu.farmersdelight.recipe.RecipePublicationRollbackIntegrationTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 命令与编辑器同时接受裸 id 与带命名空间 id | 通过 | test:com.huidu.farmersdelight.recipe.RecipeIdWiringTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 编辑器写 YAML：保留其余部分逐字节、原子替换、失败不半写、同文件不交错 | 通过 | test:com.huidu.farmersdelight.util.yaml.YamlFileTransactionTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 编辑器写盘离 owner 线程、完成回 owner、满队列显式失败、停机记录在途 | 通过 | test:com.huidu.farmersdelight.gui.editor.EditorWriteQueueTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 配方解析：同内容代次复用、换代与换内容都不复用、有界 FIFO、并发安全 | 通过 | test:com.huidu.farmersdelight.recipe.ParsedRecipeCacheTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 解析缓存接线：两 manager 的解析都经缓存、协调器三趟推进内容代次 | 通过 | test:com.huidu.farmersdelight.recipe.RecipeParseCacheWiringTest | 2026-10-07 |
| Folia | 1.21.5 | 26.9.1 | 跨 region 邻块写入前判定归属，不越界写 | 通过 | test:com.huidu.farmersdelight.block.behavior.FoliaNeighborWriteTest | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 容器修复后的整树构建（165 suites / 869 tests / 0 failures / 0 errors / 0 skipped） | 通过 | doc:docs/verification-2026-10-08.md | 2026-10-08 |
| Paper | 1.21.5 | 26.9.1 | 启动实机：内容摘要行的厨锅/砧板数字与 `recipes/*.yml` 条数一致 | 未验证 | manual:业主测试服重启后核对那一行 | 2026-10-07 |
| Folia | 1.21.5 | 26.9.1 | 编辑器实机：保存一条配方后手写注释仍在原处 | 未验证 | - | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 停服实机：仍有在途编辑时出现 `recipe_saves_pending` 告警 | 未验证 | - | 2026-10-07 |
| Paper | 1.21.5 | 26.9.2 | 与 26.9.1 同口径的构建与测试 | 未验证 | - | 2026-10-07 |
| Paper | 1.21.5 | 26.9.1 | 配方原料 null / 空图标不执行 clone，正常图标独立复制（离线） | 通过 | test:com.huidu.farmersdelight.gui.RecipeIngredientDisplayTest | 2026-10-10 |
| Paper | 1.21.5 | 26.9.1 | 未采样不创建 Sample，holder 关闭快照，异常路径只记录一次（离线） | 通过 | test:com.huidu.farmersdelight.api.util.PerformanceProbeTest | 2026-10-10 |
| Folia | 1.21.5 | 26.9.1 | ScheduledTask 官方取消状态与 NOOP 委托（离线，非实机调度） | 通过 | test:com.huidu.farmersdelight.util.scheduler.ScheduledTaskAdapterTest | 2026-10-10 |
| Paper | 1.21.5 | 26.8.2～26.10 | FD 发行 912 / debug 924 项测试、清理与 14 产物 CE 符号检查 | 通过 | doc:docs/verification-debug-cleanup-2026-10-10.md | 2026-10-10 |
| Paper | 26.2-121 | 26.10-9743c3b0 | 测试服启动、竞争重载、save-all flush 与正常停服（无玩家） | 通过 | doc:docs/verification-ce-snapshot2-2026-10-10.md | 2026-10-10 |
| Folia | 26.2-7 | 26.10-9743c3b0 | 测试服启动、竞争重载与正常世界保存停服（无玩家） | 通过 | doc:docs/verification-ce-snapshot2-2026-10-10.md | 2026-10-10 |
| Paper | 1.21.5 | 26.9.1 | 公开资源 lease：旧能力回退、分片持有、失败/取消释放（离线） | 通过 | test:com.huidu.farmersdelight.CraftEngineResourceLeaseTest | 2026-10-10 |
| Paper | 1.21.5 | 26.9.1 | debug 撤销 6874 条分片中查询历史不压缩索引，发行935/debug948项零失败错误跳过 | 通过 | doc:docs/verification-note-undo-2026-10-11.md | 2026-10-11 |
| Purpur | 26.2-2622 | 26.10-9743c3b0 | FD/CAB 最新产物启动、19项便签真实物品数据断言及正常停服（无玩家） | 通过 | doc:docs/便签撤销修复与上游核对-2026-10-11.md | 2026-10-11 |
| Paper/Folia | 1.21.5～26.2 | 26.8.2～26.10 | 私密便签编辑/克隆、真实CE含水蟹笼和大批次撤销的玩家交互 | 未验证 | manual:手工清单T06/T07/T18/T28 | 2026-10-11 |
