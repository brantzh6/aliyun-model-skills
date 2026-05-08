---
name: aliyun-bailian-doc-research
description: 阿里云百炼文档研究与更新方法，包括文档抓取、URL 变更应对、知识提取、输出文档生成。
homepage: https://help.aliyun.com/zh/model-studio
metadata:
  {
    "openclaw":
      {
        "emoji": "📚",
        "requires": { "tools": ["web_fetch"] },
        "install": [],
      },
  }
---

# 阿里云百炼文档研究 — 抓取与更新指南

**版本**: v4.3
**更新时间**: 2026-05-08
**适用场景**: 抓取阿里云百炼平台最新文档，生成结构化的本地 SKILL.md 文档

---

## 🎯 目标

从官方文档抓取最新信息，生成 `output/{类别}/SKILL.md` 文件。

---

## 🔗 根目录与备用发现机制

> **核心原则：404 是常态。官方文档 URL 经常变化，但根目录永远不会变。**

### 根目录（不变）

| 根目录 | 说明 |
|--------|------|
| `https://help.aliyun.com/` | 阿里云帮助中心根目录 |
| `https://www.aliyun.com/` | 阿里云官网首页 |
| `https://help.aliyun.com/zh/model-studio/` | 百炼文档首页 |

### URL 变更应对策略

当已知文档 URL 返回 404 时，**按以下顺序尝试**：

#### 策略 1：访问上级目录

```
已知 URL: https://help.aliyun.com/zh/model-studio/qwen-tts
→ 404 时访问: https://help.aliyun.com/zh/model-studio/
→ 在页面中搜索新链接
```

#### 策略 2：访问文档首页

```
访问: https://help.aliyun.com/zh/model-studio/
→ 这是百炼文档的入口页面，包含所有子文档的导航链接
→ 从中提取最新的各能力文档 URL
```

#### 策略 3：搜索阿里云官网

```
访问: https://www.aliyun.com/
→ 使用站内搜索："百炼" 或 "model-studio" 或具体功能名称
→ 或直接访问: https://www.aliyun.com/search?keywords=百炼+语音合成
```

#### 策略 4：搜索帮助中心

```
访问: https://help.aliyun.com/
→ 使用帮助中心搜索功能
→ 或在浏览器中使用: site:help.aliyun.com {关键词}
```

### 已知有效入口页面

| 页面 | URL | 说明 |
|------|-----|------|
| 百炼文档首页 | `https://help.aliyun.com/zh/model-studio/` | ⭐ 最重要入口 |
| 模型列表 | `https://help.aliyun.com/zh/model-studio/models` | 所有模型规格 |
| 首次调用 | `https://help.aliyun.com/zh/model-studio/first-api-call-to-qwen` | 快速入门 |
| API Key | `https://help.aliyun.com/zh/model-studio/get-api-key` | 获取密钥 |
| 限流 | `https://help.aliyun.com/zh/model-studio/rate-limit` | 限流说明 |
| 地域选择 | `https://help.aliyun.com/zh/model-studio/regions/` | 地域和服务部署范围 |
| 模型计费 | `https://help.aliyun.com/zh/model-studio/model-pricing` | 模型调用计费 |
| Token Plan | `https://help.aliyun.com/zh/model-studio/token-plan-overview` | 🆕 团队版Token Plan |
| Coding Plan | `https://help.aliyun.com/zh/model-studio/coding-plan` | 🆕 Coding Plan |

**这些入口页面几乎不会变化**，即使子文档 URL 变了，从入口页面总能找到新链接。

---

## 📁 输出目录结构

```
output/
├── INDEX.md                              ← 总索引
├── language-models/                      ← 语言模型
├── video-generation/                     ← 视频生成
├── image-generation/                     ← 图像生成
├── tts/                                  ← 语音合成
├── asr/                                  ← 语音识别
├── embedding/                            ← 向量模型
├── vision/                               ← 视觉理解
├── tool-calls/                           ← 工具调用
├── batch/                                ← Batch 批量
├── rate-limit/                           ← 限流
└── error-codes/                          ← 错误码
```

---

## 📋 抓取流程

### 第 0 步：确认入口（每次必做）

```python
# 先访问百炼文档首页，确认导航结构未变
web_fetch(url="https://help.aliyun.com/zh/model-studio/", maxChars=20000)
```

从首页提取当前各能力文档的最新 URL。

### 第 1 步：批量抓取官方文档

使用 `web_fetch` 抓取所有核心文档。如果某个 URL 返回 404，**不要跳过**，执行第 0 步从入口页面找到新 URL。

> ⚠️ **2026-05 起 URL 结构调整**：官方将平铺 URL 改为嵌套结构，例如 `text-generation` → `text-generation-model/`。旧 URL 部分仍可访问，但建议使用新 URL。

```python
# 核心文档（高优先级）— 🆕 使用新 URL 结构
web_fetch(url="https://help.aliyun.com/zh/model-studio/text-generation-model/", maxChars=20000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/vision-model/", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/embedding-rerank-model/", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/rate-limit", maxChars=15000)

# 文本生成子文档
web_fetch(url="https://help.aliyun.com/zh/model-studio/text-generation", maxChars=15000)  # 概述
web_fetch(url="https://help.aliyun.com/zh/model-studio/multi-round-conversation", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/stream", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/deep-thinking", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/qwen-structured-output", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/partial-mode", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/context-cache", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/batch-inference", maxChars=15000)

# 视觉理解（已重组为 vision-model/ 下的多个子页面）
web_fetch(url="https://help.aliyun.com/zh/model-studio/vision-model/", maxChars=20000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/vision", maxChars=15000)              # 图像与视频理解
web_fetch(url="https://help.aliyun.com/zh/model-studio/qwen-vl-ocr", maxChars=15000)       # 文字提取 (OCR)
web_fetch(url="https://help.aliyun.com/zh/model-studio/visual-reasoning", maxChars=15000)   # 视觉推理

# 语音合成（已重组为 tts-model/ 下的多个子页面）
web_fetch(url="https://help.aliyun.com/zh/model-studio/tts-model/", maxChars=20000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/text-to-speech", maxChars=15000)     # 实时语音合成-CosyVoice
web_fetch(url="https://help.aliyun.com/zh/model-studio/qwen-tts-realtime", maxChars=15000)  # 实时语音合成-千问
web_fetch(url="https://help.aliyun.com/zh/model-studio/qwen-tts", maxChars=15000)           # 语音合成-千问

# 语音识别（已重组为 asr-model/ 下的多个子页面）
web_fetch(url="https://help.aliyun.com/zh/model-studio/asr-model/", maxChars=20000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/real-time-speech-recognition", maxChars=15000)  # 实时语音识别
web_fetch(url="https://help.aliyun.com/zh/model-studio/qwen-real-time-speech-recognition", maxChars=15000)  # 实时-千问
web_fetch(url="https://help.aliyun.com/zh/model-studio/recording-file-recognition", maxChars=15000)  # 录音文件识别
web_fetch(url="https://help.aliyun.com/zh/model-studio/qwen-speech-recognition", maxChars=15000)  # 录音文件-千问

# 🆕 语音转语音（新增）
web_fetch(url="https://help.aliyun.com/zh/model-studio/s2s-model/", maxChars=20000)

# 🆕 音乐生成
web_fetch(url="https://help.aliyun.com/zh/model-studio/fun-music", maxChars=15000)

# 🆕 向量与重排序子页面
web_fetch(url="https://help.aliyun.com/zh/model-studio/embedding", maxChars=15000)         # 文本与多模态向量化
web_fetch(url="https://help.aliyun.com/zh/model-studio/rerank", maxChars=15000)            # 重排序

# 图片生成与编辑（已重组为 image-model/ 下的多个子页面）
web_fetch(url="https://help.aliyun.com/zh/model-studio/image-model/", maxChars=20000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/text-to-image", maxChars=15000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/style-repaint", maxChars=15000)           # 🆕 人像风格重绘
web_fetch(url="https://help.aliyun.com/zh/model-studio/image-background-generation", maxChars=15000)  # 🆕 背景生成
web_fetch(url="https://help.aliyun.com/zh/model-studio/image-expansion", maxChars=15000)         # 🆕 画面扩展
web_fetch(url="https://help.aliyun.com/zh/model-studio/virtual-model-generation", maxChars=15000)    # 🆕 虚拟模特
web_fetch(url="https://help.aliyun.com/zh/model-studio/creative-poster-generation-overview", maxChars=15000)  # 🆕 创意海报
web_fetch(url="https://help.aliyun.com/zh/model-studio/vary-region", maxChars=15000)             # 🆕 局部重绘
web_fetch(url="https://help.aliyun.com/zh/model-studio/sketch-to-image", maxChars=15000)         # 🆕 涂鸦作画

# 视频生成与编辑（已重组为 video-generate-edit-model/ 下的多个子页面）
web_fetch(url="https://help.aliyun.com/zh/model-studio/video-generate-edit-model/", maxChars=20000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/text-to-video-guide", maxChars=20000)        # 文生视频 (Wan 2.7)
web_fetch(url="https://help.aliyun.com/zh/model-studio/wan-image-to-video-guide", maxChars=20000)  # 图生视频 (Wan 2.7)
web_fetch(url="https://help.aliyun.com/zh/model-studio/image-to-video-first-and-last-frames-guide", maxChars=20000)  # 首尾帧
web_fetch(url="https://help.aliyun.com/zh/model-studio/video-to-video-guide", maxChars=20000)       # 参考生视频 (Wan 2.7)
# ⚠️ 双导航陷阱：video-generate-edit-model/ (用户指南) 只展示 Wan 2.7 ≠ HappyHorse 已废弃
# 必须通过 API 参考侧边栏 /zh/model-studio/get-api-key 确认 HappyHorse 是否仍在

# 3D模型生成 🆕
web_fetch(url="https://help.aliyun.com/zh/model-studio/tripo-3d-generation-guide", maxChars=15000)

# 🆕 模型调优
web_fetch(url="https://help.aliyun.com/zh/model-studio/wan-video-generation-finetune-guide", maxChars=15000)

# 🆕 客户端接入文档
web_fetch(url="https://help.aliyun.com/zh/model-studio/openclaw", maxChars=10000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/hermes-agent", maxChars=10000)
web_fetch(url="https://help.aliyun.com/zh/model-studio/lingma-agent", maxChars=10000)  # 🆕 Lingma
```

### 第 2 步：提取关键信息

从每个页面提取：
1. **模型列表** — 模型名称、特点、支持的功能
2. **快速开始代码** — Python/curl 示例
3. **参数表格** — 关键参数、取值范围、说明
4. **注意事项** — 有效期、SDK版本要求、地域差异
5. **支持的地域** — 北京/新加坡/全球

### 第 3 步：生成 SKILL.md

每个类别的 SKILL.md 标准结构：

```markdown
# {类别名称}

> 更新时间：{日期}
> 来源：{官方URL}

---

## 概述

**Base URL：**
- 北京：`...`
- 新加坡：`...`

---

## 模型矩阵

| 模型 | 特点 | 适用场景 |

---

## 快速开始

### Python
### curl

---

## 关键参数

| 参数 | 说明 | 取值 |

---

## 支持的模型

### 北京地域 / 新加坡地域

---

## 注意事项
```

### 第 4 步：生成 _meta.json

```json
{
  "name": "{类别名}",
  "version": "{日期}",
  "description": "{描述}",
  "last_updated": "{日期}",
  "sources": ["{官方URL}"],
  "files": ["SKILL.md"]
}
```

### 第 5 步：更新 INDEX.md

更新总索引，标注所有来源 URL 和状态。

---

## ⚠️ 注意事项

### 信息真实性

- **只写官方文档中明确提到的内容**
- **不要基于已有知识推测**
- 如果官方文档没有提到某个模型或功能，不要写入

### 404 处理

1. 已知 URL 返回 404 → 访问 `https://help.aliyun.com/zh/model-studio/` 找到新 URL
2. 如果入口页面也变了 → 访问 `https://help.aliyun.com/` 搜索"百炼"
3. 如果搜索也失败 → 访问 `https://www.aliyun.com/` 搜索
4. **根目录 https://help.aliyun.com/ 和 https://www.aliyun.com/ 永远不会变**

### ⚠️ 模型废弃判定 — 双导航陷阱

**百炼文档有两套并行的导航结构，不能从一个推断另一个：**

| 导航类型 | 入口 | 说明 |
|---------|------|------|
| 用户指南（模型） | `/zh/model-studio/what-is-model-studio` | 侧重使用流程、教程、最佳实践 |
| API参考（模型） | `/zh/model-studio/get-api-key` | 侧重接口规范、参数表、代码示例 |

**2026-05-08 教训**：`video-generate-edit-model/`（用户指南）页面只展示了 Wan 2.7，但 HappyHorse 的 API 参考文档（`happyhorse-text-to-video-api-reference` 等 4 个页面）完全正常且当天刚更新。**Guide 页面侧重某个模型 ≠ 其他模型已废弃。**

**正确判定流程**：
1. Guide 页面找不到某模型 → **不要立刻判定废弃**
2. 必须访问 API 参考侧边栏（`/zh/model-studio/get-api-key`）确认该模型的 API 文档是否仍存在
3. 必须访问模型列表页（`/zh/model-studio/models`）确认该模型是否仍在列表中
4. 只有 **API 参考页面 + 模型列表** 都确认不存在 → 才能判定废弃

### 备用抓取方式

### 备用抓取方式

当 `web_fetch` 返回内容不完整或被截断时，使用 browser 工具链：
```
browser_navigate(url) → browser_console("document.querySelector('main').innerText")
```
这能获取完整的页面文本内容，适合提取详细的模型参数和代码示例。

### 时效性

- 每个文档标注更新日期和来源 URL
- 模型列表、限流条件等数据经常变化，以官方最新为准

---

## 📊 当前文档状态（2026-05-08）

| 类别 | 文件数 | 状态 | 来源 |
|------|--------|------|------|
| language-models | 8 | ✅ | text-generation-model/, deep-thinking, context-cache, stream, structured-output, partial-mode, batch-inference |
| video-generation | 22 | ✅ | video-generate-edit-model/, Wan 2.7 API, **HappyHorse 1.0 全系列** |
| image-generation | 16 | ✅ | image-model/, **style-repaint, background-generation, image-expansion, virtual-model, creative-poster, vary-region, sketch-to-image** |
| tts | 2 | ✅ | tts-model/ |
| asr | 2 | ✅ | asr-model/ |
| s2s | 2 | ✅ 🆕 | s2s-model/ (Qwen3.5-Omni, Qwen3-Omni, Livetranslate) |
| music | 2 | ✅ 🆕 | fun-music |
| 3d-generation | 2 | ✅ 🆕 | tripo-3d-generation-guide |
| embedding | 4 | ✅ | embedding-rerank-model/, embedding, rerank |
| vision | 4 | ✅ | vision-model/, vision, qwen-vl-ocr, visual-reasoning |
| tool-calls | 2 | ✅ | tool-calls, function-calling |
| batch | 2 | ✅ | batch-inference |
| rate-limit | 2 | ✅ | rate-limit |
| error-codes | 2 | ✅ | error-code |
| finetuning | 1 | ⚠️ 部分 | wan-video-generation-finetune-guide |

**总计：约 74+ 个文件**

### 🆕 v4.1 变更（2026-05-08）

| 变更类型 | 内容 | 说明 |
|---------|------|------|
| URL 重组 | 文本生成/视觉理解/语音/图片/视频 | 平铺 URL → 嵌套结构（*-model/） |
| 🆕 音乐生成 | fun-music | Fun 音乐大模型 |
| 🆕 语音转语音 | s2s-model/ | 全模态 + 语音翻译 |
| 🆕 3D 模型生成 | tripo-3d-generation-guide | Tripo 3D 生成 |
| 🆕 图片编辑 | style-repaint 等 8 个子页面 | 人像风格重绘、背景生成、画面扩展等 |
| 🆕 视频子页面 | text-to-video-guide 等 | 文生视频、图生视频、首尾帧 |
| 🆕 Token Plan | token-plan-overview | 团队版 Token Plan |
| 🆕 Coding Plan | coding-plan | Coding Plan |
| 🆕 Lingma | lingma-agent | 新客户端接入 |
| 🆕 Qwen3.6 系列 | text-generation-model/ | qwen3.6-max-preview, qwen3.6-plus(1M), qwen3.6-flash(1M) |
| 🆕 第三方模型 | text-generation-model/ | deepseek-v4-pro/flash(1M), glm-5.1, kimi-k2.6, MiniMax-M2.5 |
| 🆕 Qwen3.5 系列 | text-generation-model/ | qwen3.5-plus(1M), qwen3.5-flash(1M), 397b/122b/27b/35b MoE |
| 🆕 视觉理解升级 | vision-model/ | Qwen3.6-plus/flash: 1M上下文, 16M像素, 2小时视频 |
| 🆕 TTS CosyVoice v3.5 | tts-model/ | 声音复刻+声音设计+指令控制 |
| 🆕 ASR Qwen3-ASR | asr-model/ | 情感识别, 多语种 |
| 🆕 S2S Qwen3.5-Omni | s2s-model/ | Function Calling + 联网搜索, 29种语言翻译 |
| 🆕 Embedding v4 | embedding-rerank-model/ | text-embedding-v4: 64~2048维 |
| 🆕 多模态 Embedding | embedding-rerank-model/ | qwen3-vl-embedding, tongyi-embedding-vision-plus |
| 🆕 Rerank | embedding-rerank-model/ | qwen3-rerank(文本), qwen3-vl-rerank(多模态) |

### 🆕 v4.0 新增文档（2026-04-28）

| 新文档 | URL | 说明 |
|--------|-----|------|
| HappyHorse-文生视频 | happyhorse-text-to-video-api-reference | happyhorse-1.0-t2v |
| HappyHorse-图生视频 | happyhorse-image-to-video-api-reference | happyhorse-1.0-i2v |
| HappyHorse-参考生视频 | happyhorse-reference-to-video-api-reference | happyhorse-1.0-r2v |
| HappyHorse-视频编辑 | happyhorse-video-edit-api-reference | happyhorse-1.0-video-edit |
| 万相-图像生成与编辑2.7 | wan-image-generation-and-editing-api-reference | wan2.7-image-pro/image |
| 千问-文生图 | qwen-image-api | qwen-image-2.0-pro/2.0/max/plus |
| 千问-图像编辑 | qwen-image-edit-api | 独立编辑接口 |
| Z-Image 文生图 | z-image-api-reference | 轻量快速模型 |

---

## 🔧 实战经验（从实际更新中积累）

### 2026-05-08 全面更新发现

1. **双导航陷阱（重要教训）**：`video-generate-edit-model/`（用户指南）只展示 Wan 2.7，但 HappyHorse 的 API 参考文档（4 个页面）全部正常且当天更新。Guide 页面侧重某模型 ≠ 其他模型已废弃。必须通过 API 参考侧边栏和模型列表双重确认。
2. **browser_console 提取完整内容**：`web_fetch` 对阿里云文档经常返回不完整内容。使用 `browser_navigate` + `browser_console("document.querySelector('main').innerText")` 可获取完整页面文本，适合提取详细的模型参数表和代码示例。
3. **Wan 2.7 新增能力**：有声视频（自动配音/自定义音频）、多镜头叙事、视频续写、音色定制、多宫格图。这些在旧 skill 中完全缺失。
4. **SDK 版本要求提升**：从 1.25.15 → 1.25.16，旧版本会报 "url error"。
5. **Qwen3.6 已发布**：最新旗舰系列，qwen3.6-plus 和 qwen3.6-flash 支持 1M 上下文窗口。
6. **第三方模型 API 格式统一**：deepseek-v4-pro/flash、glm-5.1、kimi-k2.6、MiniMax-M2.5 的 API 格式与千问模型一致。
7. **Qwen3.5-Omni 全模态**：支持文本、音频、图片、视频输入，S2S 场景推荐，支持 Function Calling + 联网搜索 + 29 种语言翻译。
8. **TTS 三大系列**：CosyVoice（声音复刻+声音设计+指令控制）、Qwen3-TTS、MiniMax speech。
9. **ASR 两大路线**：Fun-ASR（热词增强）和 Qwen3-ASR（情感识别），Qwen3.5-Omni 支持 Prompt 上下文注入。
10. **Embedding v4**：text-embedding-v4 支持 64~2048 维可调，qwen3-vl-embedding 支持多模态融合向量。
11. **Rerank 新增**：qwen3-rerank（文本，100+ 语言）、qwen3-vl-rerank（多模态，支持文本/图片/视频混合排序）。

### 周更任务最佳实践

1. 先访问文档首页确认导航结构
2. 逐个抓取子页面，404 的标记为废弃
3. 对比本地 skill 的模型列表，识别新增/废弃
4. 更新时用 `patch` 而非全量重写，保留历史上下文
5. 推送前检查 git diff 确认变更范围
