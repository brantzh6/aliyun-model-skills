# 阿里云百炼视觉模型技能集 (Aliyun Visual Skills)

## 📦 包含技能

### 1. video-studio — AI 视频创作工作室
完整的 AI 视频创作工作流，支持：
- **HappyHorse 1.0** 全系列（t2v / i2v / r2v / video-edit）
- **Wan 2.7** 系列（t2v / i2v / videoedit / r2v-flash）
- 剧本助手、角色管理、分镜脚本、Prompt 工程、镜头语言库

### 2. aliyun-visual — 阿里云百炼视觉模型全栈 API
覆盖所有图像和视频生成模型：
- **图像生成**：千问 Qwen-Image 2.0 / 万相 Wan 2.7 / Z-Image
- **视频生成**：HappyHorse 1.0 / Wan 2.7 / Wan 2.6
- 文生图、图像编辑、组图、交互式编辑、文字渲染
- 文生视频、图生视频、参考生视频、视频编辑

## 📋 模型一览

| 类别 | 模型系列 | 能力 |
|------|---------|------|
| 🖼️ 图像 | 千问 2.0-pro | 文字渲染、真实质感、1-6张组图 |
| 🖼️ 图像 | 万相 2.7-pro | 4K文生图、交互式编辑、思考模式 |
| 🖼️ 图像 | Z-Image | 轻量快速 |
| 🎬 视频 | HappyHorse 1.0 | t2v/i2v/r2v(1-9图)/video-edit |
| 🎬 视频 | Wan 2.7 | t2v(音频驱动)/i2v(首尾帧)/videoedit |

## 🚀 使用方式

将 `SKILL.md` 文件放入 `~/.hermes/skills/<skill-name>/` 目录即可使用。

## 📝 更新日志

### 2026-04-28
- 新增 HappyHorse 1.0 全系列 API 文档
- 新增 aliyun-visual 技能（覆盖所有图像+视频模型）
- 更新 video-studio 技能（加入 HappyHorse + Wan 2.7 对比）
- 补充万相 2.7 图像生成与编辑能力（4K/交互式编辑/组图）
