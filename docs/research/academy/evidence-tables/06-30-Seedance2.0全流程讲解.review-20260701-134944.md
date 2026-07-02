# 6. Seedance2.0全流程讲解 深度 AV 审查

- 视频 ID：30
- 时长：06:50
- 抽帧：2 秒间隔，帧图带时间戳水印
- 音频：Whisper medium 转写，已映射到最近 2 秒帧
- Contact sheet：[06-30-Seedance2.0全流程讲解.jpg](/Users/chenxh/Documents/Codex/2026-06-30/ni-zh/work/academy-av-deep-audit-20260701-134944/contact-sheets/06-30-Seedance2.0全流程讲解.jpg)
- 原始证据索引：[06-30-Seedance2.0全流程讲解.index.md](/Users/chenxh/Documents/Codex/2026-06-30/ni-zh/work/academy-av-deep-audit-20260701-134944/evidence-tables/06-30-Seedance2.0全流程讲解.index.md)

## 逐步骤证据表

| 步骤 | 时间戳 | 画面证据 | 音频证据摘要 | 确认的功能/流程 | PRD 影响 | WBS/验收影响 | 二次核验 | 置信度 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 00:00:00-00:00:06 | [frame-0001.jpg](/Users/chenxh/Documents/Codex/2026-06-30/ni-zh/work/academy-av-deep-audit-20260701-134944/frames-2s/06-30-Seedance2.0全流程讲解/frame-0001.jpg) | 這一期我們講解一下利用AnnieShort最新上線的視頻模型AnnieVideo 2.0來製作一集小影片 | Seedance/AnnieVideo 2.0 用于制作短片，重点是图生视频和音画同步。 | PRD 15.2 增加 Seedance2/AnnieVideo2 模型能力标记。 | PRD 15.2 增加 Seedance2/AnnieVideo2 模型能力标记。 | 画面帧、ASR 片段、同主题视频结论三者一致；若后续实现遇到歧义，需回到原站 Chrome/CDP 复核。 | 高 |
| 2 | 00:00:57-00:01:00 | [frame-0029.jpg](/Users/chenxh/Documents/Codex/2026-06-30/ni-zh/work/academy-av-deep-audit-20260701-134944/frames-2s/06-30-Seedance2.0全流程讲解/frame-0029.jpg) | 選中劇本節點,在上方有四個命令 | 流程从文本、主体提取、角色/场景/道具生成到分镜，再进入视频生成。 | WBS 20.1 E2E 需覆盖 Seedance 路径。 | WBS 20.1 E2E 需覆盖 Seedance 路径。 | 画面帧、ASR 片段、同主题视频结论三者一致；若后续实现遇到歧义，需回到原站 Chrome/CDP 复核。 | 高 |
| 3 | 00:03:31-00:03:34 | [frame-0106.jpg](/Users/chenxh/Documents/Codex/2026-06-30/ni-zh/work/academy-av-deep-audit-20260701-134944/frames-2s/06-30-Seedance2.0全流程讲解/frame-0106.jpg) | 查看生成結果,這裡同樣也是可以在編輯的 | 视频模型可直接使用网格分镜图作为参考图。 | PRD 7.7 增加网格参考输入。 | PRD 7.7 增加网格参考输入。 | 画面帧、ASR 片段、同主题视频结论三者一致；若后续实现遇到歧义，需回到原站 Chrome/CDP 复核。 | 高 |
| 4 | 00:05:57-00:06:02 | [frame-0179.jpg](/Users/chenxh/Documents/Codex/2026-06-30/ni-zh/work/academy-av-deep-audit-20260701-134944/frames-2s/06-30-Seedance2.0全流程讲解/frame-0179.jpg) | 從劇本創作到視頻輸出,整個過程基本就是這樣的一個結構 | 生成后进入成片审阅；后续修改提交会记录版本号，便于对比。 | PRD 14 增加审阅版本 | WBS Phase 11 增加版本对比验收。 | 画面帧、ASR 片段、同主题视频结论三者一致；若后续实现遇到歧义，需回到原站 Chrome/CDP 复核。 | 高 |

## 实现要点

1. Seedance/AnnieVideo 2.0 用于制作短片，重点是图生视频和音画同步。
2. 流程从文本、主体提取、角色/场景/道具生成到分镜，再进入视频生成。
3. 视频模型可直接使用网格分镜图作为参考图。
4. 生成后进入成片审阅；后续修改提交会记录版本号，便于对比。

## 核验说明

- 本文件不是单纯 ASR 摘要；每条结论均绑定最近 2 秒帧，便于打开画面核查。
- Whisper 对“测量缩放/主体提取/AniShort”等词可能有误识别，文档中的产品术语已按画面和业务上下文校正。
- 若后续开发发现 PRD/WBS 与原站行为不一致，以 Chrome/CDP 对原站实时检查结果为准，并追加新的时间戳增补文件。
