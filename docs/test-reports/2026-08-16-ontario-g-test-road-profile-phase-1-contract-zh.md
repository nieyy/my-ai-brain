# Test Report: Ontario G Test Road Profile / Phase 1 数据契约与内容门禁

**日期**: 2026-08-16
**命令**: `npm run validate:content`、`npm test`
**分支 / Commit**: `ontario-g-test/main @ 28c8b5e`
**结果**: PASS

## 1. Summary

- 覆盖的行为: `newmarket-road-profile-v1`、RouteGraph、八个 RoadSection、稳定 edge/section/lane ID、来源账本、六类场景 route binding、严格内容验证。
- 本次结果: 合法 profile 通过；重复 ID、断链 traversal、小地图断点、异常中心线、车道重叠、标线空洞、非法 transition/taper、悬空 movement、缺失路口、错误箭头、缺失来源及误标 verified geometry 均被拒绝。
- 是否阻塞继续推进: 否。

## 2. Run History

| 时间 | Commit | 结果 | 备注 |
|---|---|---|---|
| 2026-08-16 | `28c8b5e` | PASS | `Validated 1 centre, six scenario families, 8 guidance plans, and the Newmarket road profile.` |

## 3. Failure Details

无未解决失败。

## 4. Analysis

- 观察: 真实名称仅用于区域语境；路线顺序、车道、限速、路口、匝道及几何均标记为 `authored-approximation`。
- 初步判断: 数据边界与 Locked v1.0 设计一致，没有复制地图几何或宣称官方考试路线。
- 需要 RCA 吗: 否。

## 5. Evidence

- Profile content hash: `newmarket-road-profile-v1-authored-20260816`。
- Validator fixtures: `src/content/roadProfiles/validate.test.ts`。
- 来源账本: `src/content/roadProfiles/sources.ts`。
