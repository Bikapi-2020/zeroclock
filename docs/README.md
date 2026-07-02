# ZeroClock Documentation Index

本目录承接 Codex 线程 `019f16f7-680f-7100-976d-56b04d62050e` 的 AniShort 类 AI 短剧协作平台规划、Academy 视频审查、PRD/WBS 和教程产物。

## 推荐开发主文档

后续开发优先以这两份重整理实施版为准：

- [PRD 重整理实施版](./product/prd/AniShort-PRD-REORGANIZED-IMPLEMENTATION-20260701-171019.md)
- [WBS 重整理实施版](./product/wbs/AniShort-WBS-REORGANIZED-IMPLEMENTATION-20260701-171019.md)

这两份文档已经将原始 PRD/WBS、节点规格矩阵、Academy P0 AV 深度审查内容合并到对应章节，并新增 `PRD/WBS/nodeId/Phase` 对齐表。

## 开发实施计划

- [开发实施计划](./DEVELOPMENT-IMPLEMENTATION-PLAN.md)

该计划用于当前仓库的实际落地顺序，按 Milestone、Phase、任务、测试和验收门禁组织。

## Academy 研究与证据

- [Academy P0 交付索引](./research/academy/AniShort-academy-P0-deliverables-index-20260701-134944.md)
- [Academy P0 AV 深度审查总报告](./research/academy/AniShort-academy-P0-AV-deep-audit-20260701-134944.md)
- [覆盖与 QA 摘要](./research/academy/coverage-qa-summary-20260701-134944.md)
- [逐视频 review 与证据表](./research/academy/evidence-tables/)

说明：2.5GB 的抽帧、音频和中间媒体证据未迁入仓库，避免仓库膨胀。原始证据源仍保留在本机：

`/Users/chenxh/Documents/Codex/2026-06-30/ni-zh/work/academy-av-deep-audit-20260701-134944`

## PPT 教程

6 个教程 PPT 位于：

- [00 工作流总览](./tutorials/ppt/AniShort-00-workflow-overview-tutorial-20260701-134944.pptx)
- [01 画布与节点库](./tutorials/ppt/AniShort-01-canvas-node-library-tutorial-20260701-134944.pptx)
- [02 剧本主体分镜](./tutorials/ppt/AniShort-02-script-subject-storyboard-tutorial-20260701-134944.pptx)
- [03 图像视频 Seedance](./tutorials/ppt/AniShort-03-image-video-seedance-tutorial-20260701-134944.pptx)
- [04 3D 与全景](./tutorials/ppt/AniShort-04-3d-panorama-tutorial-20260701-134944.pptx)
- [05 团队个人协作](./tutorials/ppt/AniShort-05-team-personal-collaboration-tutorial-20260701-134944.pptx)

早期单文件教程 PPT 保留在 [tutorials/archive](./tutorials/archive/)。

## 历史文档

历史版本保留用于审计和差异追溯，不建议作为当前开发主依据：

- [原始 PRD/WBS 历史版](./product/archive/)
- [Academy 增补、详细整合、完整合并历史版](./research/academy/archive/)

## 强制开发门禁

当 PRD、接口、交互、节点选项、模型参数、状态流转或错误处理不清晰时，必须先使用 Chrome/CDP 复核原站真实实现，并记录：

`原站观察 -> 我方实现决策 -> 未确认风险`

不得依靠猜测补齐业务逻辑。
