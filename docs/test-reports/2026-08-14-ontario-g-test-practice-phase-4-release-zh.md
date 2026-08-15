# Test Report: Ontario G Test Guided Practice / Phase 4 1.1.0 发布验收

**日期**: 2026-08-15
**命令**: `npm run check:release`、`git diff --check`
**分支 / Commit**: `ontario-g-test/main @ 6ebd9b4`
**结果**: PASS

## 1. Summary

- 覆盖的行为: lint、内容契约、单元/组件、生产构建、Chromium/WebKit、手机、axe、旧 Exam 回归、Pages deployment、公开 Guided Practice/Exam smoke。
- 本次结果: 1.1.0 已部署至 <https://nieyy.github.io/ontario-g-test/>；P0/P1 为 0。
- 是否阻塞继续推进: 否。

## 2. Run History

| 时间 | Commit | 结果 | 备注 |
|---|---|---|---|
| 2026-08-15 | `3546f67` | PASS（后发现隔离缺陷） | Actions/Pages 成功；公开 smoke 发现新 Exam 可能继承旧 Practice checkpoint。 |
| 2026-08-15 | `6ebd9b4` | PASS | 修复、34 个 E2E 全绿、重新部署并公开复测。 |

## 3. Failure Details

**已解决错误签名**: 浏览器存在中断 Guided Practice checkpoint 时，从 Home 新建 Exam 可能读取该 checkpoint，导致 mode/runtime/Coach 状态混入。

**修复**: `startDraft()` 在解析并启动任何新 run 前删除旧 checkpoint；Resume 仍走独立入口。新增 Chromium/WebKit 回归用例。

## 4. Analysis

- 观察: 最终 release gate 为 39 个单元/组件测试 + 34 个 E2E；Chromium/WebKit 全绿；axe serious/critical=0。
- 初步判断: 公开 Pages 的 Practice 入口、六类选择、full route、Coach 和 Exam 隔离均正常；页面控制台无错误/警告。
- 需要 RCA 吗: 否；问题在 Owner/public smoke gate 内被发现并闭环，无数据删除或远程影响。

## 5. Evidence

- 最终 workflow: <https://github.com/nieyy/ontario-g-test/actions/runs/31865601410>，verify 2m23s、deploy 9s，结论 success。
- 公开版本: `App v1.1.0 · Content v1.1.0`。
- 公开 Guided Practice: full route 可启动、六个 focused scene 入口、Coach 首步为 Right mirror/E、控制台无错误。
- 公开 Exam: `EXAM MODE` 可见，Coach DOM 数量 0；带旧 checkpoint 的新建 Exam 复测通过。
- 非阻塞注释: GitHub 官方 actions 发出 Node 20 deprecated/runner 强制 Node 24 警告；不影响 verify/deploy，后续可随 actions 主版本升级处理。
