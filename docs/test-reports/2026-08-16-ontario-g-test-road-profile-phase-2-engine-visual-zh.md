# Test Report: Ontario G Test Road Profile / Phase 2 动态车道内核与视觉切片

**日期**: 2026-08-16
**命令**: `npm run check:release`
**分支 / Commit**: `ontario-g-test/main @ 28c8b5e`
**结果**: PASS

## 1. Summary

- 覆盖的行为: RoadPosition、相邻车道、split/merge、左转袋形车道、RoadFrame、统一世界坐标、停止线、信号灯、左右转向符号、方向盘回正、checkpoint v3。
- 本次结果: Engine/Canvas/HUD 使用一致 laneId；实际换入袋形车道后 `pocket-through → pocket-left-turn`，摄像机横移至 lane offset `-1.80 m`，transition 完成后 steering 为 `0.0°`。
- 是否阻塞继续推进: 否。

## 2. Run History

| 时间 | Commit | 结果 | 备注 |
|---|---|---|---|
| 2026-08-16 | `28c8b5e` | PASS | 69 个 unit/component tests；桌面及手机横屏 RoadProfile E2E 通过。 |

## 3. Failure Details

已解决：RoadFrame 信号路口若场景未单独填写灯色，最初不会绘制交通灯。修复为以 `intersection.control === "traffic-signal"` 为结构事实，并以场景灯色覆盖默认红灯。

## 4. Analysis

- 观察: Canvas 仅消费 RoadFrame；道路面、标线、箭头、停止线、横向道路和灯具共用世界坐标。
- 初步判断: 不再通过平移仪表盘或旋转方向盘伪装换道；路口具有 ahead/approaching/decision/crossing/passed 连续状态。
- 需要 RCA 吗: 否；问题在本地发布门禁内发现并闭环。

## 5. Evidence

- 单元测试: `roadModel.test.ts`、`roadFrame.test.ts`、`engine.test.ts`、`storage.test.ts`。
- 关键帧 E2E: `e2e/road-profile.spec.ts`，覆盖袋形左转前后、路口近景与手机横屏。
- 手工浏览器验收: 红灯、左转箭头、袋形车道、换道后的相机位置和方向盘回正均可见。
