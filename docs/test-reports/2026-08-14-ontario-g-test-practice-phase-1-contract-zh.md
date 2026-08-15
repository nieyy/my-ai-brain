# Test Report: Ontario G Test Guided Practice / Phase 1 运行契约与迁移

**日期**: 2026-08-15
**命令**: `npm run check:release`
**分支 / Commit**: `ontario-g-test/main @ 6ebd9b4`
**结果**: PASS

## 1. Summary

- 覆盖的行为: `RunConfig`/`ResolvedRunConfig`、Exam/Practice scope、运行时 finding context、AttemptRecord v2、旧 attempt/checkpoint 迁移、Mode Select、Practice Select、弱项证据来源。
- 本次结果: 新契约已从旧 `RunStage` 推断逻辑中拆分；v2 保留旧 reader 所需字段；旧 exam、practice、continued-practice、缺字段记录和 v1 checkpoint 均有测试。
- 是否阻塞继续推进: 否。

## 2. Run History

| 时间 | Commit | 结果 | 备注 |
|---|---|---|---|
| 2026-08-15 | `3546f67` | PASS | Phase 1–4 主实现首次通过本地 release gate。 |
| 2026-08-15 | `6ebd9b4` | PASS | 增加新会话与旧 checkpoint 隔离回归测试。 |

## 3. Failure Details

无未解决失败。公开 smoke 曾发现“新 Exam 可能继承旧 Guided Practice checkpoint”，已由 `6ebd9b4` 修复并加入 Chromium/WebKit E2E。

## 4. Analysis

- 观察: `mode`、`scope`、`runtime context` 和兼容 `runStage` 已各自承担单一语义；full-route Exam/Practice 同 seed 路线深度相等。
- 初步判断: Phase 1 退出标准满足；迁移不删除 IndexedDB，也不把 legacy unknown 当作 exam evidence。
- 需要 RCA 吗: 否；checkpoint 隔离问题在发布验收中发现并当轮修复，影响未扩散到保存数据。

## 5. Evidence

- 单元/组件: 7 个文件、39 个测试全部通过。
- E2E: 新模式入口、back 行为、checkpoint reload、新 Exam 隔离均在 Chromium/WebKit 通过。
- 数据: IndexedDB store/version 未变；v2 record 保留 `runStage` 和旧必填字段。
