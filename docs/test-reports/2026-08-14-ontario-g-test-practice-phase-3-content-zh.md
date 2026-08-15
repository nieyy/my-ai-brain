# Test Report: Ontario G Test Guided Practice / Phase 3 六类内容与练习历史

**日期**: 2026-08-15
**命令**: `npm run validate:content`、`npm run test`、`npm run test:e2e`
**分支 / Commit**: `ontario-g-test/main @ 6ebd9b4`
**结果**: PASS

## 1. Summary

- 覆盖的行为: 六类场景、18 variants、full-route Practice、focused Practice、same/next/choose、practice session/round 记录、History mode/scope 标签、exam-first 弱项来源。
- 本次结果: 18 variants 均且仅映射到一个受控 GuidancePlan；Yellow 和 Slow Lead 按 authored variant 分支；content version 更新为 1.1.0。
- 是否阻塞继续推进: 否。

## 2. Run History

| 时间 | Commit | 结果 | 备注 |
|---|---|---|---|
| 2026-08-15 | `3546f67` | PASS | 六类 Guidance、完整路线、next variant、历史语义完成。 |
| 2026-08-15 | `6ebd9b4` | PASS | 完整 regression gate 再次通过。 |

## 3. Failure Details

无未解决失败。

## 4. Analysis

- 观察: 内容 validator 会拒绝漏 plan、重复 coverage、未知 variant、action/highlight 不一致和错误 MSS 顺序。
- 初步判断: 六类/18 variants 均可由 CoachState 推进至完成；same retry 保持同 seed/variant，next retry 稳定轮换且不覆盖上一轮记录。
- 需要 RCA 吗: 否。

## 5. Evidence

- `validate:content`: 1 个 centre、六类场景、8 个分支 GuidancePlan 全部通过。
- 单元测试: 逐一推进 18 variant plan；校验错误 Signal-before-Mirror fixture 会失败。
- E2E: 完整路线入口、六个典型场景入口、危险复盘、same/next retry、History/报告既有回归通过。
- Finding context: Exam 首次危险保持 exam evidence；继续后的新 finding 标为 practice。
