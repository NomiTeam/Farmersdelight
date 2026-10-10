# 绯红栅栏掉落修复验证（2026-10-10）

绯红栅栏方块的物品属于 minecraft:crimson_fence，不能沿用 default:loot_table/self 展开为 farmersdelight:crimson_fence。配置使用 CE basic 掉落模板并显式传入物品和 count: 1，保留爆炸存活条件。

- 发行 clean build：177 套 / 919 项 JUnit，零失败、错误、跳过。
- debug clean build：180 套 / 931 项 JUnit，零失败、错误、跳过；已保存 debug 产物供测试服使用，工作区最终恢复发行构建。
- tools/run_all_checks.py --quiet：10/10 通过。
- 六组 CE 符号检查：14 个产物、201 类、623 成员，无缺失或 abstract gap。包含本轮 FD 发行/debug 和 CAB，其它产物沿用既有构建。
- 五个维护模块的直接自身掉落别名检查未找到其它同类配置；没有声称完全展开所有动态模板。

手工生存破坏、含水和栅栏连接方向仍待玩家复验。展示破坏纹理与自发包实体剔除本轮仅完成能力审查，未修改渲染业务。

完整说明见[根报告](../../docs/便签API与掉落修复及展示剔除评估-2026-10-10.md)。本轮证据：C:/Users/HuiDu_OwO/.codex/tmp/cab-book-render-review-20261010。
