# Test Report: Ontario G Test Road Profile / Phase 3 Newmarket 教学走廊

**日期**: 2026-08-16
**命令**: `npm run check:release`
**分支 / Commit**: `ontario-g-test/main @ 28c8b5e`
**结果**: PASS

## 1. Summary

- 覆盖的行为: 停车场出口、地方双向路、信号路口、左转袋形车道、城市主干道、弯曲入口匝道、三车道主线、出口/返回结构；Exam、完整 Guided Practice、六类典型场景、小地图和 Coach facts。
- 本次结果: 六类场景绑定同一稳定 RouteGraph；画面可在 1/2/3 条同向车道、turn/merge/exit lane 间确定性切换；Exam 与 Practice 对同 seed 使用同一路况。
- 是否阻塞继续推进: 否。

## 2. Run History

| 时间 | Commit | 结果 | 备注 |
|---|---|---|---|
| 2026-08-16 | `28c8b5e` | PASS | Chromium/WebKit 的模式、Coach、MiniMap、无障碍与危险后继续流程通过。 |

## 3. Failure Details

无未解决失败。

## 4. Analysis

- 观察: 小地图、Canvas、Engine 和 Guidance 共用 profile/route 状态；Coach 不修改 RoadPosition。
- 初步判断: 道路变化均服务路口、车道位置、汇入和驶出训练，没有随机增减车道或为每个场景复制一条不一致道路。
- 需要 RCA 吗: 否。

## 5. Evidence

- 内容版本: `1.2.0`；RoadProfile 版本: `1.0.0`。
- 可访问摘要会朗读当前道路类型、同向车道数和车道角色。
- 静态源码审计未发现 `fetch`、XHR、WebSocket、Google Maps、OSM、Mapbox 或 Leaflet 运行时调用。
