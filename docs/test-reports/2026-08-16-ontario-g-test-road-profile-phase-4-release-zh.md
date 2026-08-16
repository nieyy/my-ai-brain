# Test Report: Ontario G Test Road Profile / Phase 4 1.2.0 发布验收

**日期**: 2026-08-16
**命令**: `npm run check:release`、flag-off/flag-on production build、`git diff --check`、公开浏览器 smoke test
**分支 / Commit**: `ontario-g-test/main @ 28c8b5e`
**结果**: PASS

## 1. Summary

- 覆盖的行为: lint、内容校验、typecheck/build、69 个 unit/component tests、Chromium/WebKit 40 个 E2E、桌面/手机横屏关键帧、axe、v2/v3 checkpoint、1.1 rollback build、GitHub Pages 和公开交互。
- 本次结果: 1.2.0 已发布至 <https://nieyy.github.io/ontario-g-test/>；P0/P1 为 0。
- 是否阻塞继续推进: 否。

## 2. Run History

| 时间 | Commit | 结果 | 备注 |
|---|---|---|---|
| 2026-08-16 | `28c8b5e` | PASS | 本地 release gate 全绿；Pages verify 2m25s、deploy 14s；公开 smoke 通过。 |

## 3. Failure Details

无未解决产品失败。GitHub Actions 给出官方 actions Node 20 deprecated、runner 强制 Node 24 的非阻塞警告；不影响本次 verify/deploy。

## 4. Analysis

- 观察: 关闭 `VITE_NEWMARKET_ROAD_PROFILE_ENABLED` 的兼容构建成功，随后重新以 `true` 构建并发布；公开站点显示 App/Content `1.2.0` 与 Road profile `1.0.0`。
- 初步判断: 公开站点 base path、静态资源、动态道路、键盘/触控、手机横屏、checkpoint 恢复与免责声明正常；浏览器控制台无错误或警告。
- 需要 RCA 吗: 否。

## 5. Evidence

- Workflow: <https://github.com/nieyy/ontario-g-test/actions/runs/31958045712>，结论 success。
- 公开 RoadProfile smoke: `left-turn-pocket / pocket-through` 可见，交通灯可见；换道后为 `pocket-left-turn`、lane offset `-1.80`、steering `0.0`。
- 手机横屏: Canvas、Accelerate、道路结构和 Newmarket-inspired 小地图可访问。
- 存档: 公开页面刷新后 `Resume interrupted drive` 可见，v3 checkpoint 恢复入口正常。
- 许可/网络: 页面明确 authored approximation 与非官方路线；无地图 API、地图瓦片、后台或分析请求。
