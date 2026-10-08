# 验证记录：debug 体验、便签与附属标签

日期：2026-10-09；FD 编译目标 CE 26.9.1。修改包括层级放置指令、模拟手持计算负载、公共标签成员归属和 CAB 便签持久化。

## 构建与 JUnit

在各实际仓库内严格串行执行 `gradlew.bat clean build --console=plain`。debug 构建使用 `-PdebugTools=true`，保存产物/XML 后恢复 FD 默认 clean build。

| 构建 | suites | tests | failures / errors / skipped |
| --- | ---: | ---: | --- |
| FD debug | 170 | 900 | 0 / 0 / 0 |
| FD 默认发行 | 168 | 892 | 0 / 0 / 0 |
| CAB | 17 | 76 | 0 / 0 / 0 |
| ED | 3 | 11 | 0 / 0 / 0 |

两次 FD 构建包含重复测试，不将 900 和 892 相加作为独立用例总数。此次未重跑未修改的 BBQD、BAC、模板、VD 或 UAAPI 全量构建；前轮 1102 项是其历史验证记录。

- debug 专用八项：层级命令参数保留、未完成输入、旧命令兼容、补全、双语帮助参数可见；模拟 32 会话 × 16 tick 得到 512 次更新、64 次完成/显示构建，输入数量/耐久未改；隐藏进度抑制重复显示；配方改变时拒绝成功统计。
- CAB 新增四项：物品文本/签名数据序列化恢复、旧家具源物品恢复、已保存编辑优先、空源安全。修复前两项失败，修复后全通过；原失败 XML 保留在证据目录。
- 公共标签测试同时验证 FD 默认映射不再依赖 CAB/ED 成员。九个缺失标签已补齐附属 CE 配置，另外两个原本存在；十步校验覆盖 YAML、语言、API 边界、状态占用与资源契约。

## 静态与兼容验证

`python tools/run_all_checks.py --quiet`：10/10 全通过。当前 7 个运行/API 产物对 CE 26.8.2、26.9、26.9.1、26.9.2、26.10-SNAPSHOT 的缺类、缺成员和抽象缺口均为 0。保存的 FD debug JAR 也独立通过五版检查；新模拟类仅在 debug 构建中存在。

默认运行产物为 `build/libs/farmersdelight-1.0.3.jar`；API 为 `build/libs/farmersdelight-plugin-1.0.3-api.jar`。debug 产物保存在本轮临时证据目录，不与发行 JAR 混放。VD 五项原有用户改动的 SHA256 与原记录全部一致。

证据目录：`C:/Users/HuiDu_OwO/.codex/tmp/fd-experience-audit-20261009/`，包含 `final-evidence.json`、debug 全量 XML/产物、便签修复前失败 XML、原注册清单和分类、debug 五版符号日志。7 个产物的五版日志仍在 `ce-26.10-review-20261009/`。同级 Wiki 全量与根入口/本轮报告共 121 份 Markdown、717 个相对链接，0 断链；Git diff --check 通过。

## 运行边界

没有真实 Paper/Folia 服务端、客户端、第三方来源组合或多玩家压测；没有完整停服恢复和破坏掉落实机测试。手持模拟仅计会话/显示计算，不验证网络包、真实背包、热源、事件、分区域调度或 MSPT。保留的风险与修改建议见工作区审查报告；不把本地绿色测试或符号检查当作完整运行兼容承诺。未推送、未部署。
