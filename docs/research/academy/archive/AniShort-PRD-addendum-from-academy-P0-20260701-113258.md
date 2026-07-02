# PRD 增量补充：来自 Academy P0 视频审计

版本：20260701-113258
基线文档：`work/AniShort-like-PRD-technical-design.md`
证据文档：`work/academy-audit-20260701-113258/AniShort-academy-P0-video-audit-20260701-113258.md`

## 1. 合并建议

本增量建议合并到主 PRD 以下章节：

| 主 PRD 章节 | 需要补充的内容 |
| --- | --- |
| 2. 信息架构 | 增加 Academy/教程中心与功能发布教学入口 |
| 3. 核心创作流程 | 增加基础工作流、最新工作流、自由式工作流三种路径 |
| 6-7. 节点库与节点详细设计 | 补齐剧本分段、主体、分镜、图像、视频、720 全景、3D 导演台细节 |
| 8. 3D 导演台 | 明确独立工作台、角色白模、TransformControls、镜头输出 |
| 11. 主体库 | 明确主体覆盖角色、场景、道具、音色 |
| 15. 模型与任务系统 | 补充图像/视频模型列表、推荐模型、模式限制 |
| 20-21. 权限/计费 | 补充个人版与团队版差异 |
| 24. 质量与测试 | 增加 Academy 工作流验收路径 |

## 2. Academy/教程中心

### 2.1 产品定位

Academy 不是普通帮助页，而是平台内置的工作流学习与功能发布中心。它承担：

- 新用户上手：快速了解一部 AI 短剧制作流程。
- 工作流教学：基础短剧工作流、最新工作流、自由式工作流。
- 功能教学：3D 导演台、全景图、Seedance、批量操作、历史版本等。
- 常见问题：模型失败、人物一致性、全局风格、跨项目主体复用。

### 2.2 信息结构

教程分类来自原站 API：

- 入门
- 全流程教程
- 创作技巧
- 功能教程
- 基础教程
- 新功能
- 常见问题

教程数据字段：

- `id`
- `menu_id`
- `class_id`
- `name`
- `description`
- `preview_image`
- `video_url`
- `video_time`
- `is_good`
- `created_at`
- `status`

### 2.3 功能要求

- 教程列表支持分类筛选、分页、推荐标记。
- 教程详情支持视频播放、返回列表、同分类推荐。
- 后台支持教程新增、排序、启停、分类维护。
- 前端需要支持 CDN URL 转换：源站 OSS 与 CDN 地址互转。

## 3. 三种核心创作路径

### 3.1 基础短剧工作流

适用：标准短剧从 0 到 1 生产。

流程：

1. 创建项目与默认画布。
2. 使用内置基础短剧工作流模板。
3. 输入或上传剧本。
4. 执行剧本整理/分段。
5. 执行主体提取，得到角色、场景、道具。
6. 执行主体生成，产出设定图。
7. 命名并存入主体库。
8. 执行分镜生成，产出 Scene 结构。
9. 执行图像生成，得到分镜图/宫格图。
10. 执行视频生成。
11. 进入剪辑预览与成片审阅。

### 3.2 最新工作流

适用：追求更快出片和更高可控性。

新增要求：

- 节点参数应提供清晰默认值。
- AI 可以辅助改写剧本/提示词，但结果需要可复制回节点。
- 分镜图和视频节点应支持批量生成。
- 节点输出需要保留历史版本，便于回退。

### 3.3 自由式短剧工作流

适用：已有素材、非线性探索、局部重做。

新增要求：

- 支持空白画布右键自由创建节点。
- 支持把任意图像/文本/视频作为参考连入下游。
- 支持局部节点链路执行，不要求全流程从剧本开始。
- 支持对齐、复制、分组、批量下载、批量替换。

## 4. 节点补充规格

### 4.1 剧本节点

新增字段：

```ts
type ScriptNodeData = {
  originalText: string;
  optimizedText?: string;
  confirmedText?: string;
  activeVersionId?: string;
  history: Array<{
    id: string;
    source: "manual" | "assistant" | "script-segment" | "import";
    text: string;
    createdAt: number;
  }>;
};
```

新增交互：

- 全屏编辑。
- 回填 AI 输出。
- 历史版本切换。
- 复制为下游提示词。
- 长文本保存状态和字数统计。

### 4.2 剧本分段/优化节点

新增字段：

```ts
type ScriptSegmentNodeData = {
  segmentMode: "smart" | "fixed";
  fixedSegmentDurationSeconds?: number;
  segmentDurationRange?: { min: 1; max: 15 };
  optimizePlot: boolean;
  extractSubjectPrompts: boolean;
  preserveDialogue: boolean;
  preserveNarration: boolean;
  output: {
    segments: ScriptSegment[];
    optimizedScript?: string;
    subjectPromptDraft?: SubjectPromptDraft;
  };
};
```

关键规则：

- 智能分段必须支持 1-15 秒段落。
- 固定分段允许指定每段秒数，例如 15 秒。
- 输出要能被主体提取和分镜生成消费。
- 如果用户修改分段结果，下游应标记为需刷新。

### 4.3 主体提取节点

新增字段：

```ts
type SubjectExtractOutput = {
  summary: string;
  characters: SubjectCandidate[];
  scenes: SubjectCandidate[];
  props: SubjectCandidate[];
  voices?: VoiceCandidate[];
  assetLibraryStatus: Record<string, "exists" | "missing" | "conflict">;
};
```

关键规则：

- 同一角色多个名字要合并为同一主体。
- 角色、场景、道具都必须进入主体候选表。
- 输出必须包含主体库状态，不允许只返回列表。

### 4.4 主体生成节点

新增字段：

```ts
type SubjectGenerateNodeData = {
  subjectType: "character" | "scene" | "prop" | "voice";
  generationMode: "single" | "multiView" | "threeView" | "styleReference";
  nameRequired: boolean;
  outputAssets: AssetRef[];
  storeToLibraryStatus?: "idle" | "stored" | "failed";
};
```

关键规则：

- 角色默认多视图。
- 道具默认三视图或多视图。
- 场景可继续转换为 720 全景图。
- 主体生成结果必须支持命名和入库。

### 4.5 分镜生成节点

新增字段：

```ts
type StoryboardScene = {
  sceneNo: string;
  durationSeconds: number;
  gridType: "2x2" | "3x3";
  shotCount: 4 | 9;
  cast: string[];
  scene: string;
  props: string[];
  storySummary: string;
  imageGridPrompt: string;
  videoPrompt: string;
  narration?: string;
  negativePrompt: string;
  avSyncPrompt: string;
};
```

关键规则：

- 约 10 秒以下优先四宫格，较长段落优先九宫格。
- 输出必须有资产汇总表，标记主体库状态。
- 视频提示词与图像宫格提示词要分开保存。
- 支持批量生成分镜图。

### 4.6 图像生成节点

新增能力：

- 参考图输入。
- 主体库引用。
- 历史结果。
- 全屏预览。
- 全景生成动作。
- 图像调色、打光生成、局部重绘作为扩展动作。

推荐默认模型：

- `gemini-3-pro-image-preview` / Banana Pro

模型清单：

- Banana Pro
- Banana 2.0
- Seedream 5.0
- Seedream 4.5
- Midjourney-v7
- Midjourney-v8
- GPT-image2-low
- GPT-image2-medium
- GPT-image2-high

### 4.7 视频生成节点

新增能力：

- 文本生成。
- 首帧/尾帧。
- 参考图。
- 参考视频。
- 视频编辑。
- 动作迁移。
- 批量生成。
- 视频超分。
- 字幕/水印擦除。

推荐默认模型：

- 主流程：Seedance 2.0。
- 快速预览：Seedance 2.0 fast / mini。
- 动作/参考视频：可灵 V3 Omini / 可灵V3 动作迁移。

模式限制：

- 视频编辑模式需要参考视频。
- 动作迁移需要参考图和参考视频。
- 首尾帧模式连接尾帧时必须有首帧。
- 参考视频时长需要按模型规则校验。

## 5. 720 全景图

### 5.1 功能定位

720 全景图用于解决场景一致性和角色站位，是图像节点与 3D 导演台之间的关键能力。

### 5.2 入口

- 图像节点顶部动作栏。
- 右键节点动作菜单。
- 图像节点详情面板。

### 5.3 参数

- 输入图片。
- 生成模型。
- 分辨率。
- 生成数量。
- 是否作为 3D 背景。
- 是否入库为场景主体。

### 5.4 输出

- `panoramicImageUrl`
- `isPanoramaGenerated`
- `panoramicBackgroundEnabled`
- 输出历史。
- 可双击全屏查看。

## 6. 3D 导演台补充

### 6.1 入口

- 3D 导演台节点中间按钮：点击编辑 3D 导演台。
- 图像/全景节点可跳转到 3D 导演台。

### 6.2 页面功能

- Three.js 视口。
- 720 全景/场景背景。
- 人物白模。
- 角色资源库。
- 拖拽角色入场。
- TransformControls：位移、旋转、缩放。
- 相机视角调整。
- 底部镜头缩略条。
- 输出参考图。

### 6.3 数据结构补充

```ts
type DirectorStageData = {
  backgroundAssetId?: string;
  panoramicImageUrl?: string;
  cameraState: CameraState;
  characters: Array<{
    subjectId?: string;
    modelType: "placeholder" | "asset3d" | "billboard";
    transform: Transform;
    label?: string;
  }>;
  sceneObjects: Array<{
    assetId?: string;
    transform: Transform;
  }>;
  shots: Array<{
    id: string;
    name: string;
    cameraState: CameraState;
    thumbnailUrl?: string;
  }>;
};
```

## 7. 个人版与团队版

### 7.1 个人版

- 积分属于个人。
- 项目默认个人可见。
- 协作者需要单独授权或链接加入。
- 个人空间与团队空间分离。

### 7.2 团队版

- 团队创建者充值。
- 创建者给成员分配积分。
- 团队项目成员可共同创作。
- 需要团队空间容量管理。
- 需要团队生成记录、积分数据分析和成员效率统计。

### 7.3 后台与计费

需要补充：

- 团队充值套餐。
- 个人积分充值。
- 成员积分分配。
- 团队空间扩容。
- 模型权限配置。
- 生成记录按成员/项目/模型统计。

## 8. Academy P1 待补充到 PRD 的教程

P0 未完全覆盖的功能，但 Academy 列表中已确认存在，应纳入 P1/功能验收：

- 输出高度可控的参考图，告别视频抽卡。
- Seedance2.0任务ID真伪查验。
- 图片调色，视频调色。
- 生成好的设定图如何替换。
- 如何扩容个人空间与团队空间。
- 个人项目转团队项目。
- Seedance 2.0 核心用法全解析。
- 截取视频的首尾帧、任意帧。
- 高效管理团队，积分数据分析。
- 擦除图片视频上的字幕。
- 提高图片或视频的分辨率。
- SD2.0生成真人形象如何过审。
- 如何让人物的音色始终不变。
- 一个角色多个名字怎么办。
- 剧本分集制作、多画布分工协作。
- 一个角色多种状态（妆造）。
- 图像节点打光生成。
- 画面风格节点。
- 多节点批量操作。
- 自动保存-历史版本。
- 如何找回误删节点和历史版本。
- 出现模型生成失败怎么办。
- 如何自定义全局画面风格。
- 如何保持人物的一致性。
- 如何跨项目复用主体资产。
