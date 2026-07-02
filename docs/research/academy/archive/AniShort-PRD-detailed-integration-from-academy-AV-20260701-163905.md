# AniShort PRD 详细整合版：Academy P0 AV 深度审查补强

- 文档时间戳：20260701-163905
- 证据批次：20260701-134944
- 基于原文档：`work/AniShort-like-PRD-technical-design.md`
- 证据来源：Academy P0 12 个教程视频，2 秒抽帧、Whisper medium 转写、逐步骤 review、PPT 教程包
- 用途：作为原 PRD 的详细合并候选，不是简单差异摘要

## 0. 与原 PRD 的关系

原 PRD 已经具备完整产品骨架、节点矩阵和技术设计，但 Academy P0 视频暴露出若干真实工作流细节需要补强：

| 原 PRD 章节 | 当前问题 | 本文补强方式 |
| --- | --- | --- |
| 3 核心创作流程 | 标准流仍偏概括，没有把新版“剧本优化/分段 -> 主体提取 -> 主体生成”作为强前置 | 细化标准流、自由式流、快速流、Seedance 流、3D/全景流 |
| 4 项目与画布管理 | 缺少版本历史、资源管理器、个人项目申请审批、画布锁行为细节 | 增加版本、资源、协作锁、主体库手动维护规则 |
| 5 无限画布交互 | 隐藏连线、节点列表隐藏/定位、右键节点库、快捷键缺少验收级说明 | 增加可见性、性能、端口隐藏规则 |
| 6 节点库设计 | 节点矩阵已有，但 Academy 新版流程中的子能力、状态字段、证据映射不足 | 新增节点证据矩阵和字段级补强 |
| 7 节点详细设计 | 剧本分段、主体提取、主体生成、分镜、视频、全景、3D 需更贴近教程流程 | 增加 UI、输入输出、状态机、错误态、数据结构 |
| 8 3D 导演台 | 有 3D 工作台设计，但缺少测量缩放、捕捉、设置轴心、应用归零 | 增加完整 3D 教程流程和验收字段 |
| 11 主体库 | 主体库规则偏概括，缺少手动入库、命名限制、替换主体和存储边界 | 增加主体库作为一致性资产的完整生命周期 |
| 12 资源备份 | 历史素材、提示词查看、定位节点、从历史创建节点需细化 | 增加资源管理器规格 |
| 15 模型与任务系统 | 视频中出现 Seedance2、DeepSeek V4、图像/视频模型选择逻辑 | 增加模型使用策略和待 Chrome/CDP 复核项 |
| 21 权限/团队 | 原 PRD 需明确个人/团队积分、项目权限、素材存储归属 | 增加个人/团队业务规则 |
| 24/30/32 测试与对齐 | 需要把“逻辑不清必须回原站”写成硬规则 | 增加证据优先机制和 PRD/WBS 对齐机制 |

## 1. 证据优先开发规则

### 1.1 强制规则

开发过程中若 PRD、WBS、设计稿或实现出现以下任一情况，必须使用 Chrome 插件和 CDP 检查 AniShort 原站当前实现，再开发：

- 节点选项不完整。
- 节点命令栏顺序不确定。
- 模型列表、推荐模型、积分价格不确定。
- 全局风格、隐藏 prompt、系统 prompt 是否可见不确定。
- 页面状态、按钮启用条件、错误提示不确定。
- 个人/团队权限、积分、存储归属不确定。
- Academy 视频和原站当前页面表现不一致。

### 1.2 证据记录格式

每次复核需创建新时间戳证据文件，至少包含：

```md
# 原站复核记录

- 时间：
- 页面 URL：
- 触发入口：
- 使用工具：Chrome / CDP / Network / Console / DOM Snapshot
- 复核问题：
- 原站观察：
- 截图/录屏路径：
- 接口/DOM/状态证据：
- 对 PRD 的影响：
- 对 WBS/测试的影响：
- 是否需要更新 nodeId 矩阵：
```

## 2. 核心创作流程详细修订

### 2.1 标准短剧生产流

标准短剧生产流必须按以下主链路设计：

```text
剧本节点
  -> 剧本优化/分段节点
  -> 主体提取节点
  -> 主体生成节点
  -> 主体库
  -> 分镜生成节点
  -> 九宫格/四宫格分镜图
  -> 视频生成节点
  -> 剪辑预览
  -> 成片审阅
```

主链路规则：

| 步骤 | 输入 | 输出 | 用户检查点 | 失败/回退 |
| --- | --- | --- | --- | --- |
| 剧本节点 | 粘贴文本、上传文档、AI 写作 | 原始剧本、分集文本 | 剧本内容、集数、格式 | 返回编辑或重新导入 |
| 剧本优化/分段 | 原始剧本 | 整理稿、审阅稿、分镜段落 | 原样输出是否改动、段落时长 | 修改配置后重跑 |
| 主体提取 | 分段剧本 | 主体资产表 | 名称、分类、是否缺失、是否启用 | 手动增删改主体 |
| 主体生成 | 主体资产表、风格、参考图 | 主体提示词、设定图 | 单主体提示词和图片质量 | 单主体重生成 |
| 主体库 | 主体设定图 | 可复用主体资产 | 分类、命名、别名、入库确认 | 重新命名或取消入库 |
| 分镜生成 | 分段剧本、主体库 | 主体汇总、分镜图提示词、视频提示词 | 主体是否齐全、段落节奏 | 拉取主体或补主体 |
| 分镜图生成 | 分镜提示词、主体图 | 图片节点组 | 九宫格/四宫格画面 | 修改提示词重生成 |
| 视频生成 | 分镜图、单帧、主体、视频提示词 | 视频片段、历史版本 | 比例、模型、时长、声音 | 切模型或重试 |
| 剪辑预览 | 视频片段、音频 | 时间线、预览片 | 剪辑顺序、配音、字幕 | 调整时间线 |
| 成片审阅 | 导出视频 | 审阅版本、通过/驳回状态 | 最终版本 | 回到剪辑或视频节点 |

证据：

- P0-7 `优化情节、智能分段、一键生成提示词`：明确剧本节点、剧本分段、主体提取、主体生成。
- P0-2 `AniShort最新工作流`：新版流程强调主体提取和主体生成拆分。
- P0-9 `分镜制作解析`：分镜输出主体汇总、九宫格图片提示词、视频提示词。

### 2.2 自由式短剧生产流

自由式生产流面向熟练用户，不强制执行全链路。用户可以直接以视频节点需要的输入为中心组织素材：

```text
提示词 / 参考图 / 参考视频 / 主体引用 / 历史素材 / 上传素材
  -> 视频生成节点
  -> 历史版本
  -> 剪辑预览 / 成片审阅
```

设计要求：

- 自由式流必须是一等公民，不应被实现为“标准流异常分支”。
- 视频节点必须能独立读取文本、图片、视频、主体引用。
- 支持从历史素材、资源管理器、上传素材创建新节点。
- 画布过大时，允许复制关键素材到新画布继续生产。

证据：P0-4 `自由式短剧工作流`。

### 2.3 快速入门流

快速入门流用于新手教学和低成本试跑：

```text
剧本/分镜提示词
  -> 九宫格分镜图
  -> 视频生成
```

可选分支：

- 九宫格直接作为视频参考图。
- 九宫格拆成单帧，再逐段生成视频。

证据：P0-5 `AI短剧快速入门`。

### 2.4 Seedance/AnnieVideo 2.0 流

Seedance/AnnieVideo 2.0 流是视频模型推荐路径之一：

```text
文本节点 / 剧本节点
  -> 主体提取
  -> 角色/场景/道具生成
  -> 分镜图
  -> Seedance/AnnieVideo 2.0 视频生成
  -> 成片审阅版本
```

PRD 要求：

- 视频节点支持网格分镜图作为参考输入。
- 视频比例由视频节点参数决定，不应强制继承图片节点比例。
- 视频生成时可再次引用主体图，提升角色一致性。
- 成片审阅应记录多次提交的版本号。

证据：P0-6 `Seedance2.0全流程讲解`、P0-2 `AniShort最新工作流`。

### 2.5 3D/全景支线流

3D 与全景应作为独立能力：

```text
场景图
  -> 全景生成节点
  -> 720 全景图
  -> 当前视角截图 / 四视图 / 十二视图
  -> 图像/视频/3D 导演台
```

```text
3D 世界节点
  -> 点击编辑 3D 导演台
  -> 测量缩放
  -> 设置轴心/应用归零
  -> 摆放角色
  -> 输出截图/相机参数
```

证据：P0-10 `3D导演台调整场景缩放比例与位置`、P0-11 `场景图转换成720全景图`。

## 3. 画布工作区详细补强

### 3.1 工作区结构

画布页面应包含：

| 区域 | 功能 | 状态 |
| --- | --- | --- |
| 顶部导航 | 创作画布、剪辑预览、成片审阅、同步状态、协作头像、保存/锁定 | 当前页、已同步/同步中/失败 |
| 左侧画布列表 | 多画布列表、搜索画布、创建画布、版本历史入口 | 画布数量、当前画布、权限 |
| 左侧节点列表 | 节点搜索、节点定位、隐藏/显示、节点数量 | 节点可见性、搜索结果 |
| 中央画布 | 无限画布、节点、连线、分组、右键菜单 | 缩放、选择模式、拖拽模式 |
| 底部浮动工具条 | 节点库、主体库、资源备份、全局风格、显示控制 | 开启/关闭状态 |
| 节点/右侧面板 | 节点输入、命令栏、模型选择、任务状态 | 空态、编辑态、执行态、错误态 |

### 3.2 版本历史

功能要求：

- 画布自动生成历史版本，Academy 教程提到约半小时保存一次。
- 版本历史入口在画布列表区域附近。
- 用户可选择某个版本并恢复。
- 恢复范围必须包含节点、连线、分组、资源引用、节点可见性、视口状态。

数据字段：

```ts
type CanvasVersion = {
  id: string;
  canvasId: string;
  projectId: string;
  versionNo: number;
  createdAt: string;
  createdBy: string;
  reason: "auto" | "manual" | "restore" | "import";
  snapshotRef: string;
  nodeCount: number;
  edgeCount: number;
  resourceCount: number;
};
```

### 3.3 节点列表与隐藏

功能要求：

- 支持搜索节点。
- 支持点击节点定位到画布位置。
- 支持隐藏/显示节点或节点区域。
- 隐藏节点用于大画布性能优化。
- 节点隐藏后，其连接端口也隐藏；要编辑端口和连线需恢复显示。

数据字段：

```ts
type NodeVisibilityState = {
  nodeId: string;
  hidden: boolean;
  hiddenBy?: string;
  hiddenAt?: string;
  reason?: "manual" | "performance" | "group";
};
```

### 3.4 连线显示控制

功能要求：

- 底部工具条提供隐藏/显示连线开关。
- 连线隐藏不删除引用关系。
- 连线多时隐藏应明显改善画布可读性和交互性能。
- 重新显示后连线仍可选中、编辑、删除。

验收基线：

- 1000 节点、3000 连线下，隐藏连线后拖动画布不卡死。
- 保存刷新后连线显示状态按用户设置恢复。

### 3.5 节点库入口

节点库入口：

- 底部添加节点按钮。
- 画布空白处右键菜单。
- 快捷键，例如文本节点数字 1。
- 剧本、主体、分镜等节点命令栏派生创建。

创建规则：

- 不同入口创建同一 nodeId，默认数据必须一致。
- 从上游命令创建时，应自动带入上游引用。
- 从图片节点点击全景，应创建全景节点并自动引用图片。

### 3.6 资源管理器/资源备份

资源管理器功能：

| 功能 | 说明 |
| --- | --- |
| 当前画布素材 | 展示当前画布所有图片、视频、音频 |
| 其他画布素材 | 可切换查看同项目其他画布 |
| 全屏查看 | 图片/视频可全屏预览 |
| 缩略图滑块 | 控制素材缩略图尺寸 |
| 历史素材 | 保留同一节点多次生成的历史版本 |
| 查看提示词 | 可查看并复制当时生成提示词 |
| 定位节点 | 从资源反向定位原画布节点 |
| 创建新节点 | 从历史素材创建新节点复用 |

数据结构：

```ts
type ResourceHistoryItem = {
  id: string;
  projectId: string;
  canvasId: string;
  nodeId: string;
  resourceType: "image" | "video" | "audio" | "document" | "panorama" | "model3d";
  url: string;
  thumbnailUrl?: string;
  prompt?: string;
  modelId?: string;
  params?: Record<string, unknown>;
  generationTaskId?: string;
  versionNo: number;
  createdAt: string;
  createdBy: string;
};
```

## 4. 节点规格矩阵补强

本节不是替换原 PRD 6.3，而是补充 Academy P0 证据中需要升级的节点字段、状态和命令。

| nodeId | 需要补强的真实功能 | 新增字段/状态 | 命令/入口 | 证据 |
| --- | --- | --- | --- | --- |
| `input-node` | 剧本导入、AI 创作、多集拆分，命令进入剧本分段/主体/分镜 | `episodeSplitState`、`rawScriptVersion`、`sourceType` | 剧本分段、主体提取、分镜生成 | P0-7 |
| `story-optimize-node` | 剧本整理、剧本审阅、分镜段落；原样输出、AI 改写；智能/固定分段；时长校验 | `subMode`、`rewriteMode`、`segmentMode`、`targetDurationSec`、`actualDurationSec`、`durationWarnings` | 剧本整理、剧本审阅、分镜段落、全屏查看 | P0-2、P0-7 |
| `subject-extract-lite-node` | 新版主体提取节点，输出主体资产表，和主体库匹配已有/缺失主体 | `subjectRows`、`existsInLibrary`、`enabled`、`nameValidation`、`libraryMatchId` | 主体提取、主体库校验、主体生成 | P0-2、P0-7 |
| `setting-gen-node` | 只对资产表主体生成提示词/设定图，已有主体过滤，单主体重生成，上传素材替换主体 | `promptBySubjectId`、`generationBySubjectId`、`replacementAssetId`、`libraryRefreshState` | 角色生成、场景生成、道具生成、入主体库、重生成 | P0-2、P0-7、P0-8 |
| `storyboard-gen-node` | 输出主体汇总、九宫格/四宫格图片提示词、视频提示词，拉取主体，缺失主体补充，批量生成图并分组 | `gridType`、`segmentCount`、`durationSec`、`subjectPullState`、`missingSubjects`、`generatedGroupId` | 拉取主体、批量生成分镜图、停止批量、全屏查看 | P0-9 |
| `output-node` | 分镜图生成、历史提示词、作为视频首帧/参考图、全景命令入口 | `historyVersions`、`sourceStoryboardId`、`panoramaCommandAvailable` | 生成图片、全景、入主体库、查看历史 | P0-3、P0-9、P0-11 |
| `video-node` | Seedance2/AnnieVideo2、九宫格参考图、单帧参考、主体再次引用、比例独立控制、版本历史 | `gridImageRefs`、`subjectRefs`、`aspectRatio`、`reviewVersionId`、`audioSync` | 生成视频、提示词预检测、批量生成、进入剪辑 | P0-2、P0-5、P0-6 |
| `panorama-gen-node` | 参考图生成/文本生成，prompt 可空，当前视角截图，四视图/十二视图，自动分组 | `mode`、`promptOptional`、`viewerState`、`captureMode`、`captureGroupId` | 全景生成、全景预览、当前视角、四视图、十二视图 | P0-11 |
| `scene3d-director-node` | 3D 导演台独立入口、测量缩放、捕捉、设置轴心、应用、归零、应用归零 | `worldScale`、`measurementLine`、`snapEnabled`、`originTransform`、`applyZeroState` | 编辑 3D 导演台、测量缩放、设置轴心、应用归零 | P0-10 |
| `style-node` | 手写风格提示词、参考图反推、设全局风格、提示词可见/复制/编辑 | `stylePrompt`、`reverseStyleFromImage`、`globalStyleId`、`promptVisibility` | 立即创建、反推风格、设为全局 | P0-3 |
| `group-node` | 九宫格分镜图自动组、全景视图输出自动组、整组执行 | `groupType`、`memberNodeIds`、`autoCreatedBy`、`batchState` | 自动打组、整组执行、取消分组 | P0-9、P0-11 |

## 5. 剧本节点详细补强

### 5.1 输入来源

| 来源 | 行为 | 必填字段 |
| --- | --- | --- |
| 粘贴剧本 | 直接进入编辑区 | `rawText` |
| 上传文档 | 解析文本后进入编辑区 | `fileId`、`parsedText` |
| AI 创作 | 根据用户目标生成剧本 | `prompt`、`modelId` |
| 多集拆分 | 从长文本拆分集数 | `episodeRules`、`episodeList` |

### 5.2 命令栏

剧本节点上方命令栏按从左到右执行，命令应带有依赖状态：

| 命令 | 前置条件 | 结果 |
| --- | --- | --- |
| 剧本分段 | 存在剧本文本 | 创建或更新 `story-optimize-node` |
| 主体提取 | 剧本分段完成，或允许直接从剧本提取 | 创建 `subject-extract-lite-node` |
| 主体生成 | 主体提取完成 | 创建/更新 `setting-gen-node` |
| 分镜生成 | 剧本分段完成 | 创建 `storyboard-gen-node` |

### 5.3 状态机

```ts
type ScriptNodeState =
  | "empty"
  | "editing"
  | "importing"
  | "ready"
  | "splittingEpisodes"
  | "error";
```

## 6. 剧本分段节点详细补强

### 6.1 子能力

| 子能力 | 功能 | 输出 | 用户可编辑 |
| --- | --- | --- | --- |
| 剧本整理 | 清理格式、梳理多集文本、规范角色对白和旁白 | `cleanScript` | 是 |
| 剧本审阅 | 检查情节、钩子、角色逻辑、节奏和冲突 | `reviewNotes`、`optimizedScript` | 是 |
| 分镜段落 | 拆成适合生成图片/视频的段落 | `storySegments` | 是 |

### 6.2 输出模式

| 模式 | 行为 | 验收 |
| --- | --- | --- |
| 原样输出 | 不改动原剧本，只做结构化承载 | 输出文本与输入文本一致 |
| AI 改写 | 允许润色、增强情节、优化节奏 | 保留原文/优化稿对照 |
| 原文输出 | 保留原文本格式输出 | 不丢失换行和对白 |
| 2 分钟短剧节奏 | 以 2 分钟短剧为目标重排节奏 | 段落时长合理 |

### 6.3 分段模式

| 模式 | 参数 | 行为 |
| --- | --- | --- |
| 智能分段 | 目标总时长、段落时长区间 | 按剧情密度、对白/旁白量自动拆分 |
| 固定分段 | 分段数、目标总时长 | 按固定段数拆分 |

时长规则：

- 段落建议时长 1-15 秒。
- 显示目标时长、实际估算时长、偏差。
- 偏差过大时给出 `tooShort` / `tooLong` warning。

### 6.4 数据结构

```ts
type StoryOptimizeNodeData = {
  rawScriptRef: string;
  subMode: "clean" | "review" | "storySegments";
  rewriteMode: "original" | "aiRewrite" | "rawOutput" | "twoMinuteShort";
  segmentMode: "smart" | "fixed";
  targetTotalDurationSec?: number;
  targetSegmentDurationRange?: [number, number];
  fixedSegmentCount?: number;
  cleanScript?: string;
  reviewNotes?: string;
  optimizedScript?: string;
  storySegments: StorySegment[];
  durationWarnings: DurationWarning[];
};

type StorySegment = {
  id: string;
  order: number;
  title: string;
  text: string;
  dialogueText?: string;
  narrationText?: string;
  estimatedDurationSec: number;
  involvedSubjects: string[];
};
```

## 7. 主体提取节点详细补强

### 7.1 目标

主体提取节点的目标是冻结主体资产表，不负责生成设定图。它解决的是：

- 多集角色命名不稳定。
- 分镜中主体找不到。
- 分镜主体和主体库对不上。
- 主体生成重复或遗漏。

### 7.2 主体资产表字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `id` | string | 主体行 ID |
| `name` | string | 主体名称 |
| `category` | role/scene/prop | 角色、场景、道具 |
| `description` | string | 剧中描述 |
| `visibleTraits` | string | 可见外观/空间/道具特征 |
| `sourceSegmentIds` | string[] | 来源段落 |
| `existsInLibrary` | boolean | 是否已在主体库 |
| `librarySubjectId` | string? | 匹配主体库 ID |
| `enabled` | boolean | 是否进入后续生成 |
| `needsGeneration` | boolean | 是否需要生成设定图 |
| `nameValidation` | object | 命名校验结果 |
| `notes` | string | 用户备注 |

### 7.3 用户操作

- 增加主体。
- 删除主体。
- 修改名称。
- 修改分类。
- 启用/禁用主体。
- 选择主体库匹配项。
- 标记不需要生成。
- 进入主体生成节点。

### 7.4 主体库匹配

匹配逻辑：

1. 精确名称匹配。
2. 别名匹配。
3. 同分类近似名称提示。
4. 分类冲突时提示用户处理。

处理方式：

- 使用已有主体。
- 作为新主体。
- 合并到已有主体。
- 修改名称后重新匹配。

## 8. 主体生成节点详细补强

### 8.1 目标

主体生成节点基于主体资产表生成主体提示词和设定图。

它不应重新做主体提取，除非用户明确点击“重新读取资产表”或上游变更。

### 8.2 主体生成流程

```text
读取主体资产表
  -> 过滤 disabled 主体
  -> 过滤已有主体库且无需重生成的主体
  -> 生成主体提示词
  -> 用户检查/单主体重生成
  -> 生成设定图
  -> 用户手动分类入库
```

### 8.3 分类生成入口

| 入口 | 输入 | 输出 | 错误态 |
| --- | --- | --- | --- |
| 角色生成 | role 主体 | 角色设定图 | 无角色时禁用 |
| 场景生成 | scene 主体 | 场景设定图 | 无场景时禁用 |
| 道具生成 | prop 主体 | 道具设定图 | 无道具时禁用或提示跳过 |

### 8.4 单主体重生成

单主体重生成适用场景：

- 提示词质量差。
- 图片不符合设定。
- 主体分类修正后重新生成。
- 用户上传素材替换主体后，需要刷新下游。

数据结构：

```ts
type SubjectGenerationRecord = {
  subjectRowId: string;
  prompt: string;
  promptModelId: string;
  imageModelId?: string;
  status: "idle" | "prompting" | "promptReady" | "generatingImage" | "ready" | "failed";
  imageAssetId?: string;
  errorMessage?: string;
  retryCount: number;
  updatedAt: string;
};
```

### 8.5 入主体库规则

- 设定图生成后不自动入库。
- 用户必须选择分类并确认。
- 名称不能有空格和特殊字符。
- 分类必须与主体资产表一致，或用户显式确认修改。
- 入库后画布节点保留原显示，主体库改名不回写既有画布节点。

## 9. 主体库详细补强

### 9.1 主体库定位

主体库是跨集、跨分镜、跨视频的一致性资产中心。

### 9.2 主体类型

| 类型 | 用途 | 可用下游 |
| --- | --- | --- |
| 角色 | 人物形象一致性 | 分镜、图像、视频、3D 角色 |
| 场景 | 空间和背景一致性 | 分镜、图像、全景、3D 世界 |
| 道具 | 关键物件一致性 | 分镜、图像、视频 |

### 9.3 校验规则

| 规则 | 说明 |
| --- | --- |
| 名称不能为空 | 空名称无法被分镜引用 |
| 不能包含空格 | Academy 明确提醒不能有空格 |
| 不能包含特殊符号 | 避免 prompt 和引用解析失败 |
| 分类必须正确 | 不同分类可用功能不同 |
| 同名同分类需去重 | 避免多集主体混乱 |

### 9.4 主体替换

用户可以上传素材替换主体：

```text
上传素材
  -> 选择替换主体
  -> 更新主体库
  -> 下游节点提示主体库已更新
  -> 用户可重新执行分镜/图像/视频
```

实现要求：

- 替换不应删除旧版本，应记录版本历史。
- 下游节点显示主体库更新提示。
- 用户决定是否刷新下游引用。

## 10. 分镜生成节点详细补强

### 10.1 参数

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| `segmentMode` | smart/fixed | 分段方式 |
| `gridType` | fourGrid/nineGrid/storyBoard | 四宫格、九宫格、剧情故事版 |
| `targetDurationSec` | number | 该段脚本最终影片时长 |
| `segmentCount` | number | 拆成几个宫格图 |
| `perGridDurationSec` | number | 每个宫格对应时长 |
| `subjectSource` | library/canvas/both | 主体来源 |

典型换算：

- 2 分钟 / 8 段 = 每个九宫格 15 秒。

### 10.2 输出结构

分镜生成输出至少包含：

1. 主体汇总表。
2. 九宫格/四宫格图片提示词。
3. 视频提示词。

建议结构：

```ts
type StoryboardGenerationOutput = {
  subjectSummary: StoryboardSubjectSummary[];
  gridPrompts: GridPrompt[];
  videoPrompts: VideoPrompt[];
  warnings: StoryboardWarning[];
};

type GridPrompt = {
  id: string;
  gridType: "fourGrid" | "nineGrid";
  segmentOrder: number;
  durationSec: number;
  prompt: string;
  cells: GridCellPrompt[];
};

type GridCellPrompt = {
  cellIndex: number;
  sceneDescription: string;
  subjects: string[];
  camera: string;
  action: string;
};
```

### 10.3 主体拉取

分镜图生成前，需要点击“主体库拉取主体”：

- 根据主体汇总表匹配主体库。
- 将主体设定图放到画布中。
- 如果缺失主体，显示缺失列表。
- 可自动补充生成缺失主体。

### 10.4 批量生成分镜图

行为：

- 根据 `gridPrompts` 创建多个 `output-node`。
- 自动连接主体设定图。
- 自动打组为 `group-node`，名称如“九宫格分镜组”。
- 每个图片节点保留自身提示词、模型、历史版本。

错误处理：

- 单个图片失败不影响其他图片节点。
- 批量停止后未提交项不继续扣费。
- 失败项可重试。

## 11. 图像生成节点详细补强

### 11.1 Academy 新增要求

- 支持分镜图节点由分镜生成节点批量创建。
- 支持历史记录查看提示词、定位节点、从历史创建节点。
- 支持全景命令：从图片节点创建全景生成节点。
- 支持作为视频节点首帧、尾帧、参考图。
- 支持主体库入库入口，用于角色、场景、道具设定图。

### 11.2 历史版本

```ts
type ImageGenerationHistory = {
  id: string;
  nodeId: string;
  imageAssetId: string;
  prompt: string;
  negativePrompt?: string;
  modelId: string;
  params: Record<string, unknown>;
  sourceRefs: string[];
  createdAt: string;
  cost?: number;
  selected: boolean;
};
```

## 12. 视频生成节点详细补强

### 12.1 输入模式

| 输入模式 | 说明 |
| --- | --- |
| 文生视频 | 只用视频提示词生成 |
| 图生视频 | 使用首帧、尾帧或参考图 |
| 九宫格参考 | 使用九宫格分镜图作为参考 |
| 单帧逐段 | 从九宫格拆帧后逐段生成 |
| 主体引用 | 引用角色/场景/道具主体图 |
| 音画同步 | 模型支持时读取音频或文本节奏 |

### 12.2 Seedance/AnnieVideo 2.0

PRD 要求：

- 模型能力配置中标记 `supportsGridReference`。
- 标记是否支持音画同步。
- 标记是否支持主体引用。
- 标记可用比例和分辨率。

```ts
type VideoModelCapability = {
  modelId: string;
  displayName: string;
  provider: string;
  supportsTextToVideo: boolean;
  supportsImageToVideo: boolean;
  supportsFirstLastFrame: boolean;
  supportsGridReference: boolean;
  supportsSubjectReference: boolean;
  supportsAudioSync: boolean;
  aspectRatios: string[];
  resolutions: string[];
  recommendedFor: string[];
};
```

### 12.3 版本历史与成片审阅

- 每次生成视频都记录历史版本。
- 进入成片审阅时应生成审阅版本。
- 修改后再次提交应递增版本号。
- 用户可以比较不同版本。

## 13. 全景生成节点详细补强

### 13.1 创建入口

| 入口 | 行为 |
| --- | --- |
| 图片节点上方“全景”命令 | 创建 `panorama-gen-node`，自动携带当前图片作为参考图 |
| 底部节点库 | 创建空全景节点 |

### 13.2 模式

| 模式 | 输入 | Prompt 是否必填 |
| --- | --- | --- |
| 参考图生成 | 参考图、分辨率、可选 prompt | 否 |
| 文本生成 | prompt、分辨率 | 是，除非模型支持空提示 |

Academy 证据显示参考图生成可不写 prompt 直接生成。

### 13.3 结果预览

结果预览功能：

- 双击结果全屏。
- 进入全景模式。
- 鼠标左键拖动查看不同角度。
- 当前视角截图为普通图片。
- 自动生成四视图。
- 自动生成十二视图。
- 视图输出自动分组。

数据结构：

```ts
type PanoramaNodeData = {
  mode: "referenceImage" | "text";
  referenceImageId?: string;
  prompt?: string;
  resolution: string;
  panoramaAssetId?: string;
  viewerState?: {
    yaw: number;
    pitch: number;
    fov: number;
  };
  captures: PanoramaCapture[];
};

type PanoramaCapture = {
  id: string;
  mode: "currentView" | "fourViews" | "twelveViews";
  assetIds: string[];
  groupNodeId?: string;
};
```

## 14. 3D 导演台详细补强

### 14.1 入口与布局

3D 导演台由 `scene3d-director-node` 中间按钮“点击编辑 3D 导演台”进入，是独立全屏编辑器。

页面布局：

| 区域 | 功能 |
| --- | --- |
| 中央 3D 视口 | 查看和操作 3D 场景 |
| 左侧对象/3D 世界 | 选择 3D 世界、角色、对象 |
| 底部资源条 | 角色/人物资源，点击添加到场景 |
| 右侧属性面板 | 位置、旋转、缩放、测量缩放、设置轴心 |
| 顶部工具栏 | 保存、退出、视图、辅助工具 |

### 14.2 测量缩放

流程：

```text
选择左侧 3D 世界
  -> 点击右侧测量缩放
  -> 开启捕捉
  -> 点击已知物体两端
  -> 显示测量长度
  -> 输入真实长度
  -> 应用世界缩放
```

关键规则：

- 不开启捕捉时测量线可能歪斜。
- 测量缩放调整世界比例，不是单独缩放人物。
- 示例：测量船约 0.78m，输入真实 2.5m 后人物比例恢复。

数据结构：

```ts
type MeasurementScaleState = {
  enabled: boolean;
  snapEnabled: boolean;
  measuredLength?: number;
  realWorldLength?: number;
  unit: "m";
  startPoint?: Vector3;
  endPoint?: Vector3;
  scaleApplied?: number;
};
```

### 14.3 设置轴心与应用归零

问题：

- 新建角色默认出现在世界中心。
- 剧情发生在船边/椅子旁时，每次拖拽人物很低效。

流程：

```text
选择 3D 世界
  -> 点击设置轴心
  -> 在剧情发生区域点击地面
  -> 应用
  -> 归零
  -> 新建角色默认出现在该区域
```

快捷流程：

```text
设置轴心
  -> 点击目标区域
  -> 应用归零
```

关键说明：

- 应用归零是“应用 + 归零”的合并按钮。
- 它不是破坏或改变场景本身，而是通过坐标重置调整新角色默认生成位置。

数据结构：

```ts
type DirectorOriginState = {
  pivotSelectionMode: boolean;
  selectedPivotPoint?: Vector3;
  originTransform: {
    position: Vector3;
    rotation: Vector3;
    scale: Vector3;
  };
  applyState: "idle" | "applied" | "zeroed";
  lastApplyZeroAt?: string;
};
```

## 15. 全局风格详细补强

Academy 视频确认：

- 底部工具条可创建风格节点。
- 用户可以手写风格提示词。
- 可以把参考图导入并连接到风格节点，反推风格。
- 可设为全局风格，供图像和视频节点引用。

PRD 要求：

- 全局风格 prompt 必须可查看、复制、编辑。
- 参考图反推生成的风格词应保留来源图片。
- 全局风格注入下游 prompt 时要记录注入版本。
- 若原站有隐藏系统 prompt 或内置风格词，后续实现前必须用 Chrome/CDP 复核。

## 16. 个人版、团队版与协作补强

### 16.1 工作上下文

同一账号可拥有：

- 个人空间。
- 一个或多个团队空间。

```ts
type WorkspaceContext = {
  type: "personal" | "team";
  userId: string;
  teamId?: string;
  role?: "owner" | "admin" | "member";
};
```

### 16.2 积分账户

| 上下文 | 积分来源 | 管理者 | 消耗 |
| --- | --- | --- | --- |
| 个人 | 个人充值 | 用户本人 | 个人项目、个人空间生成 |
| 团队 | 团队充值 | 团队创建者 | 团队项目生成或团队额度 |

规则：

- 个人积分和团队积分分开充值、分开消耗、分开管理。
- 团队创建者可给成员分配积分或周期额度。
- 普通成员不应看到团队充值入口。

### 16.3 项目权限

个人项目：

- 所有者可分享项目链接。
- 接收者必须申请。
- 所有者在协作管理批准后，接收者才能进入。
- 所有者可停止协作。
- 协作者可退出。

团队项目：

- 无外部协作链接。
- 只有团队成员可查看/编辑。
- 团队创建者和成员都能创建团队项目。

### 16.4 素材存储归属

关键规则：

- 图片和视频由谁生成，就存储在谁的个人账号下。
- 即使在团队项目中生成，也按生成者个人账号占用存储。
- 存储超额时，生成和导出可能失败。

数据字段：

```ts
type AssetStorageOwnership = {
  assetId: string;
  projectId: string;
  generatedInContext: WorkspaceContext;
  storageOwnerUserId: string;
  storageQuotaAccount: "personal";
  bytes: number;
};
```

## 17. 模型与任务系统补强

### 17.1 Academy 确认的模型使用

| 场景 | 模型/策略 | 证据 |
| --- | --- | --- |
| 剧本优化/分段 | DeepSeek V4 Flash 被教程/页面多次出现为默认轻量模型 | P0-2、页面截图 |
| 图像生成 | Banana、Seedream、Midjourney、GPT-image 等在原 PRD 已列出，实时列表需复核 | 原站页面 + 原 PRD |
| 视频生成 | Seedance2/AnnieVideo2、可灵、VEO、Sora、Vidu 等 | P0-2、P0-6、原 PRD |
| 主体/分镜提示词 | 文本模型输出结构化表格和 prompt | P0-7、P0-9 |

### 17.2 待 Chrome/CDP 复核的模型项

以下内容高频变化，不应仅以当前文档固化：

- 每个节点实际模型列表。
- 推荐模型标记。
- 默认模型。
- 每个模型积分价格。
- 模型是否支持九宫格参考图。
- 模型是否支持音画同步。
- 模型是否支持主体引用。
- 全局风格隐藏提示词。

## 18. PRD/WBS 对齐字段

每个节点必须在 PRD/WBS 中使用相同 nodeId：

```ts
type NodeSpecAlignment = {
  nodeId: string;
  prdSection: string;
  wbsPhase: string;
  unitTests: string[];
  integrationTests: string[];
  e2eTests: string[];
  evidenceRefs: EvidenceRef[];
};

type EvidenceRef = {
  videoId: number;
  title: string;
  timestamp: string;
  reviewFile: string;
  framePath: string;
};
```

## 19. Academy 证据映射

| 视频 | 主要覆盖 | 应合并章节 |
| --- | --- | --- |
| P0-1 基础短剧工作流 | 标准短剧生产链路 | PRD 3、7、13、14 |
| P0-2 AniShort 最新工作流 | 剧本优化、主体提取/生成拆分、主体库复用、视频主体引用 | PRD 3、7.2、7.3、7.4、7.7、11 |
| P0-3 画布工作区、节点介绍 | 画布、节点库、资源管理器、主体库、协作锁 | PRD 2、4、5、6、11、12 |
| P0-4 自由式短剧工作流 | 自由式视频生产、历史素材、跨画布复制 | PRD 3、4、7.7、12 |
| P0-5 AI 短剧快速入门 | 九宫格到视频、拆帧 | PRD 3、7.5、7.7 |
| P0-6 Seedance2.0 全流程 | Seedance 视频生成、审阅版本 | PRD 7.7、14、15 |
| P0-7 剧本工作流介绍 | 剧本节点、剧本分段、主体提取、主体生成 | PRD 7.1、7.2、7.3、7.4 |
| P0-8 主体介绍 | 主体定义、主体生成、主体库 | PRD 7.3、7.4、11 |
| P0-9 分镜制作解析 | 分镜参数、输出结构、拉取主体、批量分镜图 | PRD 7.5、7.6、7.18 |
| P0-10 3D 导演台 | 测量缩放、捕捉、设置轴心、应用归零 | PRD 7.14、8 |
| P0-11 全景图 | 全景节点、全景预览、当前视角、四/十二视图 | PRD 7.16、12 |
| P0-12 个人/团队区别 | 个人/团队上下文、积分、权限、存储 | PRD 4、12、21 |

## 20. 合并建议

### 20.1 必须合并到原 PRD 的章节

- `3.1 标准短剧生产流`：替换为新版链路。
- `6.3 节点规格矩阵`：补充新增字段和证据列。
- `7.2 剧本分段节点`：补齐子能力、原样输出、分段模式、时长规则。
- `7.3 主体提取节点`：改为资产表定位。
- `7.4 主体生成节点`：改为提示词/设定图生成定位。
- `7.5 分镜生成节点`：补齐三类输出、宫格、主体拉取、批量分组。
- `7.7 视频生成节点`：补齐九宫格参考、主体引用、Seedance2、版本。
- `7.16 图像工具节点`：补齐全景节点细节。
- `8 3D 导演台详细设计`：补齐测量缩放、捕捉、设置轴心、应用归零。
- `11 主体库`：补齐手动入库、命名、分类、替换主体。
- `12 资源备份`：补齐历史提示词、定位节点、从历史创建节点。
- `21 协作/计费/团队`：补齐个人/团队积分和存储归属。
- `32 PRD/WBS 对齐维护规则`：加入 Chrome/CDP 证据优先规则。

### 20.2 仍需原站复核后再固化的内容

- 每个节点的实时模型列表和推荐模型。
- 全局风格默认提示词/隐藏提示词。
- 各模型积分价格。
- 节点库完整子节点分类和快捷键。
- 3D 导演台所有按钮名称、快捷键和属性字段。
- 团队额度分配周期的具体 UI 字段。
