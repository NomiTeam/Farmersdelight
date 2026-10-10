# CE 新快照与重载竞争验证（2026-10-10）

范围：统一附属通知、保护 CE 资源读取至分片发布结束、编辑器复用同一发布窗口、冷启动和诊断重置时机。工作区 [评估与部署记录](../../docs/测试服复验与CE新快照评估-2026-10-10.md)保存新 API 候选、UI 配置范围和第三方环境问题。

## 构建与回归

Java 21、Paper API 1.21.5、默认 CE 26.9.1；Gradle 全部串行，本轮只重建 FD。

| 最终构建 | suites | tests | failures / errors / skipped |
| --- | ---: | ---: | --- |
| `clean build -PdebugTools=true --offline` | 180 | 931 | 0 / 0 / 0 |
| `clean build --offline`，恢复发行输出 | 177 | 919 | 0 / 0 / 0 |

新增 `CraftEngineResourceLeaseTest` 七项：旧能力回退、一次探测与互斥、异常释放、未知错误、真实编译目标 coordinator、跨注册片持有、取消释放。既有配方接线断言更新实际调用名；缓存代次断言按各 readiness 方法判断，不再依赖整个文件方法顺序。中间构建曾因旧接线断言失败，最终 clean build 已全部通过；没有隐藏或跳过 JUnit。普通 jar 任务的 SKIPPED 属禁用任务，与 JUnit skipped 不同。

- 最终部署 debug SHA256：`37d665f8a5633d7a7b213ef360103e963fcd55d95058f070ec42172e9a275382`。
- 最终发行 SHA256：`ad01bade3a6719f4c7a70ccc913a641804ea6ba0e6ec711372ef483d0546f0d1`；生成的 `BuildFlags.DEBUG_TOOLS = false`。
- 公共 CE 可选能力仅初始化反射，新增生产反射预算为真实四处；已有静态插件查找预算保持 52，旧版独有类型没有静态绑定。
- 十步校验 10/10；VD 五项原有用户修改 SHA256 未变，模板原有注释改动未纳入提交。

## CE 符号与公开能力

检查 14 个实际产物，201 个 CE 类、623 个成员，在 CE 26.8.2、26.9、26.9.1、26.9.2、26.10 两个快照构建均为零缺类、零缺成员、零抽象缺口。26.10 分别为 d50b4c90 与 9743c3b0；共五个版本、六组输入。其它附属沿用最近已验证的产物，不宣称本轮重新执行其 JUnit。

符号检查不覆盖字符串反射；可选 lease 已用编译目标 coordinator 单测和 9743c3b0 实机验证，26.8.2 缺能力回退有单测。26.8.2 未运行测试服，不能保证它对 API 发起的竞争重载具备新版原子互斥。

## 实机复验

测试路径 `C:/Users/HuiDu_OwO/Desktop/test`，Java 25.0.3。两平台使用同一最终 debug JAR 和新 CE 快照，七个部署 JAR 的最终 SHA256 与 manifest 一致。部署前完成原 JAR 和相关插件数据/配置备份，替换均在停服之后。

| 平台 | 实际构建 | 通过的操作 |
| --- | --- | --- |
| Folia | 26.2-7-ver/26.2.x@14b7fee | 启动、FD recipes/config/all、重复命令冷却、CE config 与 FD 双向竞争、正常保存停服 |
| Paper | 26.2-121-main@a2a42c5 | 同上，另执行 `save-all flush` |

最终两段日志自身插件异常告警为零，排除唯一预期的 FD debug 包提示。原回归日志已复现“引用了不存在的物品”及零条 BBQD 烧烤配方；最终同类警告和空发布均为零。CE 忙碌提示属于公开互斥规则，重试后成功，不能误当为加载失败。Folia 不提供此服的 `save-all flush` 指令，验证正常 stop 保存全部世界与玩家。日志原本追加在同一文件，证据按各自启动边界分割，避免混入另一平台的错误。

CE 849 物品、146 方块、5 家具；FD 启动内容摘要 78 厨锅/131 砧板，BBQD 烧烤与串制各 10。FD、四个附属、VD、CE 均启用，VD 26.x NMS 层加载成功。服务器最后已停止。控制台命令返回的排队耗时不是分片发布总耗时或 MSPT。

## 证据与限制

工作区 `.codex/tmp/ce-snapshot2-test-20261010/`：

- `debug-final-test-results/`、`release-final-test-results/` 与 `*-final-tests.json`。
- `five-version-symbol-matrix.log`、`symbol-matrix.json`、`check-matrix.py`。
- `folia-baseline-complete.log`、各阶段失败日志、`folia-final-complete.log`、`paper-final-complete.log`。
- `*-final-rcon-commands.json`、`deployment-manifest.json`、`verify-smoke.py`、`smoke-results.json`。
- `signature-diff.json`、`changed-classes.json`、官方 9743c3b0 的 `LithiumInventoryUtils.java`、`PackSelection.java` 和 javap 输出。

CustomFishing 不支持 MC 26.2；当前 MMOItems/MythicLib/Multiverse/WorldEdit 不支持 Folia，WorldGuard 缺 WorldEdit。它们的问题已与部署前日志或插件清单核对，本轮不修改第三方 JAR，也不宣称完整插件组合通过。

没有真人 GUI、漏斗、便签、经验或多玩家压力验收，未运行 Leaf/Lithium。没有推送或部署到生产服。
