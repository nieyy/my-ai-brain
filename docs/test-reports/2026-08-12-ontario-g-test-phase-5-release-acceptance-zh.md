# Ontario G Test Phase 5 发布验收记录

- 日期：2026-08-12
- 发布提交：`cdfe13d2fb8fcb92a39db7d366cd01295dfd7e76`
- GitHub Actions：`31661064222`（success）
- 公开站点：<https://nieyy.github.io/ontario-g-test/>
- 版本：`1.0.0`
- 结论：发布与自动化 smoke 通过；Owner 15–20 分钟正式体验和 `v1.0.0` tag 待完成

## Release gate

- `npm run check:release`：通过。
- 内容校验：1/1；Vitest：8/8；生产构建：通过。
- Playwright Chromium/WebKit：8/8，覆盖完整加速路线、危险分支、报告、checkpoint 恢复、JSON 导出、手机触控与 axe 检查。
- GitHub Actions Linux verify：通过；Pages deploy：通过。
- 公开站点短 smoke：标题和 `Content v1.0.0` 正确；Newmarket → briefing → pause/resume → 危险后继续 → 16:00 报告 → 历史均正常；console error 为 0。

## 待 Owner gate

Owner 仍需在公开站点按真实时间完成一次 15–20 分钟 Mac 体验，并在手机完成入口与核心触控检查。确认后可在该已验证提交创建 `v1.0.0` tag；在此之前不以自动加速 smoke 代替 Owner 结论。
