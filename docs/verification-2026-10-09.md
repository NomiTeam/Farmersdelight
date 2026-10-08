# 验证记录：物品身份识别性能

日期：2026-10-09。基线 FD `86607d7`，CE 编译目标 26.9.1。物品身份识别、精确 ID 匹配和注册标签查询复用同一 CE Item；识别 MMOItems 旧 ID 无需绑定 provider，构造仍经 CE。

## 构建和行为测试

所有 Gradle 构建串行运行；附属使用原有 API-only composite 通道。各模块执行 `gradlew.bat build --console=plain --rerun-tasks`。FD 最终补充包装异常传播和 custom_data 失败隔离测试后，再执行 `gradlew.bat build --console=plain`，编译、测试及产物生成成功。

| 模块 | JUnit suites | tests | failures / errors / skipped |
| --- | --- | --- | --- |
| FarmersDelight | 168 | 892 | 0 / 0 / 0 |
| BarbequesDelight | 6 | 25 | 0 / 0 / 0 |
| BrewinAndChewin | 19 | 99 | 0 / 0 / 0 |
| CrabbersDelight | 16 | 72 | 0 / 0 / 0 |
| EndsDelight | 3 | 11 | 0 / 0 / 0 |
| FDAddonTemplate | 2 | 3 | 0 / 0 / 0 |
| 合计 | 214 | 1102 | 0 / 0 / 0 |

本轮新增五项回归：CE 身份优先、MMOItems 数据变化及无 provider 场景、2000 锅的包装次数、CE 包装失败传播、legacy 数据失败后其他来源继续识别。原有多冒号/大小写、来源顺序、延迟注册、重载和构造副本回归一并通过。

操作量负载按 2000 × 7 次识别执行，混合外部物品与普通物品，实际断言包装次数为 14000；原调用链在已绑定外部来源时推导为 56000。此处减少 75% 的包装调用，不是总运行时间或全服 TPS 的提升比例。

## 静态校验与产物

- `python tools/run_all_checks.py --quiet`：10/10，包括八项契约检查、工具自测和验证矩阵。
- 最新运行 JAR 与 API JAR 对 CE 26.8.2、26.9、26.9.1 的符号核对均通过。
- 同级 Wiki 共 114 份 Markdown，相对链接 0 断链；新增的性能页中英文导航互通。
- 新增 Java 语句没有未 import 的全限定类调用。已有弃用 API 警告不影响本轮构建；本轮未迁移这些 API。
- VD 五份原有用户修改的 SHA256 与本轮前一致，未修改或提交它们。本轮不重建 VD NMS/launcher。

最终运行 JAR：`build/libs/farmersdelight-1.0.3.jar`，SHA256 `c3671c817b557775db18d96e45b91ff4e11a0def162612ecdffc4418a9fe87cd`。

最终 API JAR：`build/libs/farmersdelight-plugin-1.0.3-api.jar`，SHA256 `78da144e70ca594d90e3c873cb9b86268ddf00fe2befe6975d8e7bff207a4b7e`。

日志、XML 汇总、符号检查和校验结果保存于 `C:/Users/HuiDu_OwO/.codex/tmp/fd-performance-20261009/` 的 `final-evidence.json` 及构建日志。

## 验证边界

没有启动真实 Paper/Folia 服务端、第三方来源组合或客户端，也没有实测 2000 锅 / 200 玩家下的 MSPT、GC、网络与首次 GUI 耗时。以上结果证明本地行为回归、操作量和符号链接兼容；实机性能仍按 Wiki 的采样流程核对。本轮只做本地提交，不推送或部署。
