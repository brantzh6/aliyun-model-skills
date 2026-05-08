---
name: aliyun-visual
description: 阿里云百炼视觉模型全栈 API — 图像生成（千问/万相/Z-Image）+ 视频生成（Wan 2.7），覆盖文生图、图像编辑、组图、文生视频、图生视频、首尾帧、参考生视频全场景。2026-05 更新：HappyHorse 已废弃，视频模型全面升级为 Wan 2.7 系列。
category: mlops
requires:
  - DASHSCOPE_API_KEY
---

# 阿里云百炼视觉模型全栈 (aliyun-visual)

> 覆盖阿里云百炼所有视觉生成模型的 API 调用指南，包括图像和视频两大类。

---

## 📋 总览：阿里云视觉模型家族

```
┌──────────────────────────────────────────────────────────────┐
│                    阿里云百炼视觉模型                          │
├────────────────────┬─────────────────────────────────────────┤
│    🖼️ 图像生成      │              🎬 视频生成                  │
├────────────────────┼─────────────────────────────────────────┤
│ 千问 Qwen-Image     │ Wan 2.7 (2026-05 最新)                   │
│  ├ 2.0-pro (推荐)   │  ├ wan2.7-t2v     文生视频               │
│  ├ 2.0 (加速版)     │  ├ wan2.7-i2v     图生视频               │
│  ├ max (真实感)     │  ├ wan2.7-r2v     参考生视频             │
│  └ plus (艺术风格)  │  └ wan2.6-r2v-flash 轻量版              │
│                    │                                         │
│ 万相 Wan 2.7       │ ⚠️ HappyHorse 1.0 已废弃                 │
│  ├ image-pro (4K)  │  happyhorse-1.0-t2v/i2v/r2v/video-edit  │
│  └ image (快速版)   │  → 全面迁移至 Wan 2.7 系列               │
│                    │                                         │
│ Z-Image            │ 🆕 Wan 2.7 新能力                        │
│  └ z-image 文生图   │  ├ 有声视频（自动配音/自定义音频）        │
│                    │  ├ 多镜头叙事（镜头切换+主体一致）         │
│                    │  ├ 视频续写（首帧视频续写+尾帧控制）       │
│                    │  └ 音色定制（为主体指定独立音色）          │
└────────────────────┴─────────────────────────────────────────┘
```

---

## 🖼️ 一、图像生成

### 1.1 模型选型对比

| 模型系列 | 模型名 | 最大分辨率 | 特色能力 | 组图 | 图像编辑 | 文字渲染 |
|---------|--------|-----------|---------|------|---------|---------|
| **千问 2.0-pro** ⭐ | qwen-image-2.0-pro | 2048×2048 | 文字渲染、真实质感、语义遵循 | ✅ 1-6张 | ✅ 单独API | ⭐⭐⭐ |
| **千问 2.0** | qwen-image-2.0 | 2048×2048 | 加速版，兼顾效果与速度 | ✅ 1-6张 | ✅ 单独API | ⭐⭐ |
| **万相 2.7-pro** ⭐ | wan2.7-image-pro | 4K(文生图)/2K(编辑) | 专业版，交互式编辑，思考模式 | ✅ 最多12张 | ✅ 内置 | ⭐ |
| **万相 2.7** | wan2.7-image | 2K | 快速版 | ✅ 最多12张 | ✅ 内置 | - |
| **千问 max** | qwen-image-max | 1664×928 | 真实感强，AI合成痕迹低 | ❌ | ✅ 单独API | - |
| **千问 plus** | qwen-image-plus | 1664×928 | 多样化艺术风格 | ❌ | ✅ 单独API | ⭐⭐ |
| **Z-Image** | z-image | 多种比例 | 轻量快速 | ❌ | ❌ | ⭐ |

> ⚠️ **地域隔离**：北京和新加坡地域拥有独立的 API Key 与请求地址，不可混用。
> ⚠️ **URL/结果有效期**：所有图像和视频 URL 均仅保留 **24 小时**，请及时下载。

---

### 1.2 千问图像 (Qwen-Image)

**端点：**
- 北京：`POST https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation`
- 新加坡：`POST https://dashscope-intl.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation`

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation' \
--header 'Content-Type: application/json' \
--header "Authorization: Bearer $DASHSCOPE_API_KEY" \
--data '{
    "model": "qwen-image-2.0-pro",
    "input": {
        "messages": [
            {
                "role": "user",
                "content": [
                    {"text": "一间有着精致窗户的花店，漂亮的木质门，摆放着花朵"}
                ]
            }
        ]
    },
    "parameters": {
        "negative_prompt": "低分辨率，低画质，肢体畸形，手指畸形，画面过饱和，蜡像感，人脸无细节，过度光滑，画面具有AI感。构图混乱。文字模糊，扭曲。",
        "prompt_extend": true,
        "watermark": false,
        "size": "2048*2048"
    }
}'
```

**参数说明（qwen-image-2.0 系列）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| model | string | ✅ | `qwen-image-2.0-pro` / `qwen-image-2.0` |
| messages[].content[].text | string | ✅ | 正向提示词，最长 800 字符 |
| negative_prompt | string | ❌ | 反向提示词，最长 500 字符 |
| size | string | ❌ | 分辨率 `宽*高`，总像素 512×512 ~ 2048×2048，默认 2048×2048 |
| n | integer | ❌ | 生成数量 1-6（2.0系列），默认 1 |
| prompt_extend | bool | ❌ | 智能改写，默认 true |
| watermark | bool | ❌ | "Qwen-Image" 水印，默认 false |
| seed | integer | ❌ | [0, 2147483647] |

**推荐分辨率（2.0 系列）：**
| 比例 | 分辨率 |
|------|--------|
| 16:9 | 2688×1536 |
| 9:16 | 1536×2688 |
| 1:1 | 2048×2048（默认） |
| 4:3 | 2368×1728 |
| 3:4 | 1728×2368 |

> 💡 **图像编辑**：千问的图像编辑请使用 `千问-图像编辑` API（独立接口），非本文生图接口。

---

### 1.3 万相 2.7 图像生成与编辑 (Wan 2.7 Image)

**端点：**
- 同步：`POST https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation`
- 异步：`POST https://dashscope.aliyuncs.com/api/v1/services/aigc/image-generation/generation`（需 `X-DashScope-Async: enable`）

#### 场景一：文生图

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation' \
--header 'Content-Type: application/json' \
--header "Authorization: Bearer $DASHSCOPE_API_KEY" \
--data '{
    "model": "wan2.7-image-pro",
    "input": {
        "messages": [
            {
                "role": "user",
                "content": [
                    {"text": "一间有着精致窗户的花店，漂亮的木质门，摆放着花朵"}
                ]
            }
        ]
    },
    "parameters": {
        "size": "2K",
        "n": 1,
        "watermark": false,
        "thinking_mode": true
    }
}'
```

#### 场景二：图像编辑（多图输入 + 文本指令）

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation' \
--header 'Content-Type: application/json' \
--header "Authorization: Bearer $DASHSCOPE_API_KEY" \
--data '{
    "model": "wan2.7-image-pro",
    "input": {
        "messages": [
            {
                "role": "user",
                "content": [
                    {"text": "把图2的涂鸦喷绘在图1的汽车上"},
                    {"image": "https://example.com/car.webp"},
                    {"image": "https://example.com/paint.webp"}
                ]
            }
        ]
    },
    "parameters": {
        "size": "2K",
        "n": 1,
        "watermark": false
    }
}'
```

#### 场景三：交互式编辑（框选区域编辑）

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation' \
--header 'Content-Type: application/json' \
--header "Authorization: Bearer $DASHSCOPE_API_KEY" \
--data '{
    "model": "wan2.7-image-pro",
    "input": {
        "messages": [
            {
                "role": "user",
                "content": [
                    {"text": "将框选区域替换为一只橘猫"},
                    {"image": "https://example.com/room.jpg"}
                ]
            }
        ]
    },
    "parameters": {
        "size": "2K",
        "n": 1,
        "watermark": false,
        "bbox_list": [
            [[100, 100, 400, 400]]
        ]
    }
}'
```

#### 场景四：组图生成（`enable_sequential: true`）

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation' \
--header 'Content-Type: application/json' \
--header "Authorization: Bearer $DASHSCOPE_API_KEY" \
--data '{
    "model": "wan2.7-image-pro",
    "input": {
        "messages": [
            {
                "role": "user",
                "content": [
                    {"text": "电影感组图，记录同一只流浪橘猫，特征必须前后一致。第一张：春天，橘猫穿梭在盛开的樱花树下；第二张：夏天，橘猫在老街的树荫下乘凉；第三张：秋天，橘猫踩在满地的金色落叶上；第四张：冬天，橘猫在雪地上走留下足迹。"}
                ]
            }
        ]
    },
    "parameters": {
        "enable_sequential": true,
        "n": 4,
        "size": "2K",
        "watermark": false
    }
}'
```

**参数说明（Wan 2.7 Image）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| model | string | ✅ | `wan2.7-image-pro` / `wan2.7-image` |
| messages[].content[].text | string | ✅ | 提示词，最长 5000 字符 |
| messages[].content[].image | string | ❌ | 图像 URL / Base64 / 本地 file:// 路径 |
| size | string | ❌ | `1K`/`2K`（默认）/`4K`（仅 wan2.7-image-pro 文生图）；或自定义 `宽*高` |
| n | integer | ❌ | 非组图：1-4（默认1）；组图：1-12（默认12） |
| enable_sequential | bool | ❌ | 组图模式，默认 false |
| thinking_mode | bool | ❌ | 思考模式（仅文生图+非组图），默认 true |
| bbox_list | array | ❌ | 交互式编辑框选区域，单图最多 2 个框 |
| color_palette | array | ❌ | 自定义颜色主题，3-10 种颜色（hex + ratio） |
| watermark | bool | ❌ | "AI生成" 水印，默认 false |
| seed | integer | ❌ | [0, 2147483647] |

**分辨率规格：**

| 模型 | 场景 | 最大分辨率 | 像素范围 |
|------|------|-----------|---------|
| wan2.7-image-pro | 文生图 | 4K (4096×4096) | 768×768 ~ 4096×4096 |
| wan2.7-image-pro | 编辑/组图 | 2K (2048×2048) | 768×768 ~ 2048×2048 |
| wan2.7-image | 全部 | 2K (2048×2048) | 768×768 ~ 2048×2048 |

**图像输入限制：**
- 格式：JPEG/JPG/PNG（不支持透明）/BMP/WEBP
- 分辨率：宽高 [240, 8000] 像素，宽高比 [1:8, 8:1]
- 大小：≤ 20MB
- 数量：0-9 张

---

### 1.4 Z-Image 文生图

**端点：** `POST https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation`

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation' \
--header 'Content-Type: application/json' \
--header "Authorization: Bearer $DASHSCOPE_API_KEY" \
--data '{
    "model": "z-image",
    "input": {
        "messages": [
            {
                "role": "user",
                "content": [
                    {"text": "一只坐着的橘黄色的猫，表情愉悦，活泼可爱，逼真准确"}
                ]
            }
        ]
    },
    "parameters": {
        "size": "1024*1024",
        "n": 1,
        "watermark": false
    }
}'
```

**Z-Image 特点：** 轻量模型，生成速度快，适合快速预览和草稿。

---

### 1.5 图像生图 Prompt 指南

**万相 2.7 文生图 Prompt 公式：**
```
[主体描述] + [环境/场景] + [光影] + [构图/视角] + [风格/质感] + [情绪/氛围]

示例：
一间有着精致窗户的花店，漂亮的木质门，摆放着花朵，
温暖的阳光从窗户洒入室内，在木地板上投下斑驳光影，
前景是盛开的玫瑰和向日葵，背景有绿色藤蔓植物，
水彩手绘风格，色彩明亮温暖，氛围宁静治愈
```

**千问图像文字渲染 Prompt 要点：**
- 使用引号明确标注要渲染的文字内容
- 指定文字位置、字体风格、颜色
- 复杂排版时描述整体布局（如对联、牌匾、霓虹灯等）

---

## 👁️ 二、视频理解（Qwen-VL）

> Qwen-VL 多模态模型支持 **图像理解 + 视频理解**，可直接传入视频文件进行内容分析。

### 2.1 模型选型

| 模型 | 能力 | 说明 |
|------|------|------|
| **qwen-vl-max** ⭐ | 图像理解 + 视频理解 | 最强理解能力，适合复杂分析任务 |
| **qwen-vl-plus** | 图像理解 + 视频理解 | 均衡版，速度快 |
| **qwen-vl-ocr** | 文字提取 | 专注 OCR 场景 |

**端点：** `POST https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation`

> ⚠️ **必须用标准端点 `dashscope.aliyuncs.com`，不能用 `coding.dashscope.aliyuncs.com`**（coding 端点不支持 VL 模型）。

### 2.2 视频理解 API 调用

```python
import base64, json, httpx

API_KEY = "sk-xxxx"  # 你的 DashScope API Key
video_path = "/path/to/video.mp4"

# 读取视频并 Base64 编码
with open(video_path, 'rb') as f:
    video_b64 = base64.b64encode(f.read()).decode('utf-8')

url = 'https://dashscope.aliyuncs.com/api/v1/services/aigc/multimodal-generation/generation'
headers = {
    'Content-Type': 'application/json',
    'Authorization': f'Bearer {API_KEY}'
}

payload = {
    'model': 'qwen-vl-max',
    'input': {
        'messages': [{
            'role': 'user',
            'content': [
                {'video': f'data:video/mp4;base64,{video_b64}'},
                {'text': '请描述这段视频的内容。'}
            ]
        }]
    },
    'parameters': {'max_tokens': 2000}
}

resp = httpx.post(url, headers=headers, json=payload, timeout=120)
result = resp.json()
```

### 2.3 视频输入方式

| 方式 | 格式 | 说明 |
|------|------|------|
| **Base64** | `data:video/mp4;base64,{base64}` | ✅ 推荐，本地文件用此方式 |
| **公网 URL** | `https://example.com/video.mp4` | ✅ 支持 |
| **file:// 本地路径** | `file:///path/to/video.mp4` | ❌ **不支持**，会返回 InvalidParameter 错误 |

> ⚠️ **关键坑**：`file://` 路径对图像有效，但对视频会报错 `InvalidParameter: The provided URL does not appear to be valid`。视频必须用 Base64 或公网 URL。

### 2.4 视频限制

| 限制 | 值 |
|------|------|
| 最大文件大小 | ~100MB（建议 ≤10MB 以获得更快响应） |
| 支持格式 | MP4 / AVI / MOV / MKV / FLV / WEBM |
| 最大时长 | 取决于文件大小和模型处理时间 |
| 响应时间 | 5MB 视频约 30-40 秒 |

### 2.5 图像理解（同样适用于 VL 模型）

```python
payload = {
    'model': 'qwen-vl-max',
    'input': {
        'messages': [{
            'role': 'user',
            'content': [
                {'image': 'https://example.com/photo.jpg'},  # URL 或 Base64
                {'text': '这张图片里有什么？'}
            ]
        }]
    },
    'parameters': {'max_tokens': 2000}
}
```

> 💡 图像也支持 `file://` 本地路径，但视频不行。

---

## 🎬 三、视频生成（Wan 2.7 系列）

> ⚠️ **2026-05 重大变更**：HappyHorse 1.0 系列已从官方文档移除，全面升级为 Wan 2.7 系列。
> 完整视频创作工作流（剧本/分镜/镜头语言）请参阅 `video-studio` 技能。

### 3.1 模型总览

**通用调用方式**：同步调用 `VideoSynthesis.call()` 或异步调用 `VideoSynthesis.async_call()` + `VideoSynthesis.wait()`

| 模型 | 模型名 | 时长 | 分辨率 | 核心能力 |
|------|--------|------|--------|---------|
| 文生视频 ⭐ | wan2.7-t2v-2026-04-25 | 2-15s | 720P/1080P | 有声视频、多镜头叙事、声画同步 |
| 文生视频 | wan2.7-t2v | 2-15s | 720P/1080P | 有声视频、多镜头叙事、声画同步 |
| 文生视频 | wan2.6-t2v | 2-15s | 720P/1080P | 有声视频、多镜头叙事、声画同步 |
| 文生视频 | wan2.5-t2v-preview | 5-10s | 480P/720P/1080P | 有声视频、声画同步 |
| 图生视频 ⭐ | wan2.7-i2v-2026-04-25 | 2-15s | 720P/1080P | 首帧生视频、首尾帧、视频续写、有声 |
| 图生视频 | wan2.7-i2v | 2-15s | 720P/1080P | 首帧生视频、首尾帧、视频续写、有声 |
| 参考生视频 ⭐ | wan2.7-r2v | 2-10s | 720P/1080P | 多主体参考、音色定制、多宫格图、有声 |
| 参考生视频 | wan2.6-r2v-flash | 2-10s | 720P/1080P | 单/多角色、多镜头、更快、性价比高 |
| 参考生视频 | wan2.6-r2v | 2-10s | 720P/1080P | 单/多角色、多镜头、声画同步 |
| 无声视频 | wan2.2-t2v-plus | 5s | 480P/1080P | 纯视觉展示、稳定性高 |
| 无声视频 | wanx2.1-t2v-turbo | 5s | 480P/720P | 轻量快速 |

> ⚠️ **SDK 版本要求**：DashScope Python SDK ≥ 1.25.16
> ⚠️ **URL 有效期**：所有视频 URL 仅保留 **24 小时**，请及时下载。
> ⚠️ **地域隔离**：北京和新加坡地域拥有独立的 API Key 与请求地址，不可混用。

---

### 3.2 文生视频 (t2v)

**官方文档**: https://help.aliyun.com/zh/model-studio/text-to-video-guide

```python
from http import HTTPStatus
from dashscope import VideoSynthesis
import dashscope, os

dashscope.base_http_api_url = 'https://dashscope.aliyuncs.com/api/v1'
api_key = os.getenv("DASHSCOPE_API_KEY")

rsp = VideoSynthesis.call(
    api_key=api_key,
    model='wan2.7-t2v-2026-04-25',  # 推荐模型
    prompt='第1个镜头 大远景开篇，镜头从接近地面的低机位开始向前移动...',
    resolution="720P",
    ratio="16:9",
    duration=15,
    prompt_extend=True,
    watermark=False
)
if rsp.status_code == HTTPStatus.OK:
    print("video_url:", rsp.output.video_url)
```

**核心能力**：
- **多镜头叙事**：wan2.7/2.6 支持，prompt 中用自然语言描述镜头结构（如 `第1个镜头[0-2秒]...`）
- **有声视频**：默认自动配音，或传入 `audio_url` 实现声画同步
- **无声视频**：wan2.2 及以下版本默认无声

---

### 3.3 图生视频 (i2v)

**官方文档**: https://help.aliyun.com/zh/model-studio/wan-image-to-video-guide

支持三种任务：首帧生视频、首尾帧生视频、视频续写

```python
from dashscope import VideoSynthesis
import dashscope, os

dashscope.base_http_api_url = 'https://dashscope.aliyuncs.com/api/v1'
api_key = os.getenv("DASHSCOPE_API_KEY")

# 场景一：首帧生视频
media = [{"type": "first_frame", "url": "https://example.com/first.jpg"}]

# 场景二：首尾帧生视频
media = [
    {"type": "first_frame", "url": "https://example.com/first.jpg"},
    {"type": "last_frame", "url": "https://example.com/last.jpg"}
]

# 场景三：视频续写（首视频+尾帧）
media = [
    {"type": "first_clip", "url": "https://example.com/clip.mp4"},
    {"type": "last_frame", "url": "https://example.com/last.jpg"}
]

rsp = VideoSynthesis.call(
    api_key=api_key,
    model="wan2.7-i2v-2026-04-25",
    media=media,
    resolution="720P",
    duration=10,
    prompt="一只猫从画面左侧慢慢走向右侧..."
)
```

---

### 3.4 参考生视频 (r2v)

**官方文档**: https://help.aliyun.com/zh/model-studio/video-reference-api-reference

支持多主体参考 + 音色定制 + 多宫格图

```python
from dashscope import VideoSynthesis
import dashscope, os

dashscope.base_http_api_url = 'https://dashscope.aliyuncs.com/api/v1'
api_key = os.getenv("DASHSCOPE_API_KEY")

# 多主体参考 + 音色定制
media = [
    {
        "type": "reference_image",
        "url": "https://example.com/girl.jpg",
        "reference_voice": "https://example.com/girl_voice.mp3"  # 为该主体指定音色
    },
    {
        "type": "reference_video",
        "url": "https://example.com/boy.mp4",
        "reference_voice": "https://example.com/boy_voice.mp3"
    },
    {"type": "reference_image", "url": "https://example.com/object.png"},
    {"type": "reference_image", "url": "https://example.com/background.png"}
]

rsp = VideoSynthesis.call(
    api_key=api_key,
    model="wan2.7-r2v",
    media=media,
    resolution="720P",
    ratio="16:9",
    duration=10,
    prompt="视频1抱着图2，在图3的椅子上弹奏一支舒缓的乡村民谣..."
)
```

**指代规则**：
- 使用"图1、图2"指代 reference_image
- 使用"视频1、视频2"指代 reference_video
- 按 media 数组顺序，图和视频分别计数

---

### 3.5 旧 HappyHorse 迁移指南

| 旧模型 | 新模型 | 变更说明 |
|--------|--------|---------|
| happyhorse-1.0-t2v | wan2.7-t2v-2026-04-25 | 支持有声、多镜头 |
| happyhorse-1.0-i2v | wan2.7-i2v-2026-04-25 | 新增首尾帧、续写 |
| happyhorse-1.0-r2v | wan2.7-r2v | 新增音色定制、多宫格图 |
| happyhorse-1.0-video-edit | 暂无对应 | 可用 i2v 视频续写替代 |

**API 调用变更**：
- 旧：`POST /api/v1/services/aigc/video-generation/video-synthesis` + `X-DashScope-Async: enable`
- 新：SDK `VideoSynthesis.call()` / `async_call()`（底层仍异步，但 SDK 封装更简洁）
- curl 调用仍需 `X-DashScope-Async: enable`

### 3.6 图像 → 视频 串联工作流

```
步骤1: 万相 2.7 / 千问图像 → 生成高质量首帧参考图
       模型: wan2.7-image-pro / qwen-image-2.0-pro
       ↓ 保存生成的 PNG 图像

步骤2: Wan 2.7 i2v → 首帧驱动生成视频
       模型: wan2.7-i2v-2026-04-25
       media: [{"type": "first_frame", "url": "步骤1生成的图片URL"}]
       ↓ 获得 MP4 视频（可含音频）

步骤3（可选）: Wan 2.7 i2v → 视频续写
       media: [{"type": "first_clip", "url": "步骤2视频URL"}, {"type": "last_frame", "url": "新尾帧"}]
```

---

## 🔧 四、通用规范

### 4.1 地域对照

| 地域 | Endpoint URL | 说明 |
|------|-------------|------|
| 北京 | https://dashscope.aliyuncs.com/api/v1 | 默认地域 |
| 新加坡 | https://dashscope-intl.aliyuncs.com/api/v1 | 海外地域 |

> ⚠️ API Key、Endpoint URL、模型必须属于同一地域，不可混用。

### 4.2 异步任务管理

```bash
# 查询任务
curl -X GET https://dashscope.aliyuncs.com/api/v1/tasks/{task_id} \
    --header "Authorization: Bearer $DASHSCOPE_API_KEY"

# 任务状态
PENDING → RUNNING → SUCCEEDED / FAILED / CANCELED

# 建议轮询间隔：图像 10s，视频 15s
# 默认 RPS 限制：20
```

### 3.3 图像输入方式（通用）

```python
# 方式一：公网 URL（推荐）
image = "https://example.com/image.jpg"

# 方式二：本地文件
image = "file:///path/to/image.png"

# 方式三：Base64 编码
import base64, mimetypes
def encode_file(file_path):
    mime_type, _ = mimetypes.guess_type(file_path)
    with open(file_path, "rb") as f:
        encoded = base64.b64encode(f.read()).decode("utf-8")
    return f"data:{mime_type};base64,{encoded}"
```

### 3.4 SDK 调用说明

**SDK 最低版本要求：**
- Python SDK ≥ 1.25.15
- Java SDK ≥ 2.22.13

**Python SDK 核心流程：**

```python
import dashscope
from dashscope.aigc.image_generation import ImageGeneration

# 设置地域端点
dashscope.base_http_api_url = "https://dashscope.aliyuncs.com/api/v1"

# 图像生成调用
rsp = ImageGeneration.call(
    model="wan2.7-image-pro",
    messages=[message],
    watermark=False,
    n=1,
    size="2K",
)
```

---

## ⚠️ 四、注意事项与常见坑

### 视频生成（Wan 2.7）
1. **HappyHorse 已废弃**：happyhorse-1.0 系列已从官方文档移除，全面迁移至 Wan 2.7
2. **SDK 版本**：必须 ≥ 1.25.16，否则可能报 "url error" 错误
3. **有声视频**：wan2.7/2.6/2.5 默认自动配音，也可传入 `audio_url` 自定义音频
4. **多镜头叙事**：wan2.7 在 prompt 中用自然语言描述镜头（如 `第1个镜头[0-2秒]...`），无需额外参数
5. **视频续写**：使用 `first_clip` + `last_frame` 类型组合
6. **音色定制**：r2v 模型支持为每个主体指定 `reference_voice`
7. **多宫格图**：wan2.7-r2v 支持传入多宫格图（故事板），自动识别多镜头
8. **URL 24 小时过期**：所有结果 URL 请及时下载转存 OSS
9. **跨地域调用失败**：模型、Endpoint、API Key 必须同地域

---

## 📚 五、官方文档参考

| 模块 | 文档链接 | 状态 |
|------|---------|------|
| 图片生成与编辑（总入口） | https://help.aliyun.com/zh/model-studio/image-model/ | 🆕 2026-05 重组 |
| 千问-文生图 | https://help.aliyun.com/zh/model-studio/text-to-image | 有效 |
| 千问-图像编辑 | https://help.aliyun.com/zh/model-studio/qwen-image-edit-api | 有效 |
| 万相-图像生成与编辑2.7 | https://help.aliyun.com/zh/model-studio/wan-image-generation-and-editing-api-reference | 有效 |
| 万相-图像生成与编辑2.6 | https://help.aliyun.com/zh/model-studio/wan-image-generation-api-reference | 有效 |
| Z-Image 文生图 | https://help.aliyun.com/zh/model-studio/z-image-api-reference | 有效 |
| 人像风格重绘 | https://help.aliyun.com/zh/model-studio/style-repaint | 🆕 2026-05 |
| 图像背景生成 | https://help.aliyun.com/zh/model-studio/image-background-generation | 🆕 2026-05 |
| 图像画面扩展 | https://help.aliyun.com/zh/model-studio/image-expansion | 🆕 2026-05 |
| 虚拟模特生成 | https://help.aliyun.com/zh/model-studio/virtual-model-generation | 🆕 2026-05 |
| 创意海报生成 | https://help.aliyun.com/zh/model-studio/creative-poster-generation-overview | 🆕 2026-05 |
| 图像局部重绘 | https://help.aliyun.com/zh/model-studio/vary-region | 🆕 2026-05 |
| 涂鸦作画 | https://help.aliyun.com/zh/model-studio/sketch-to-image | 🆕 2026-05 |
| 视频生成与编辑（总入口） | https://help.aliyun.com/zh/model-studio/video-generate-edit-model/ | 🆕 2026-05 重组 |
| 文生视频 | https://help.aliyun.com/zh/model-studio/text-to-video-guide | 🆕 2026-05 |
| 图生视频 | https://help.aliyun.com/zh/model-studio/wan-image-to-video-guide | 🆕 2026-05 |
| 图生视频-首尾帧 | https://help.aliyun.com/zh/model-studio/image-to-video-first-and-last-frames-guide | 🆕 2026-05 |
| 参考生视频 | https://help.aliyun.com/zh/model-studio/video-to-video-guide | 🆕 2026-05 |
| ~~HappyHorse-文生视频~~ | ~~happyhorse-text-to-video-api-reference~~ | ❌ 已废弃，迁至 Wan 2.7 |
| ~~HappyHorse-图生视频~~ | ~~happyhorse-image-to-video-api-reference~~ | ❌ 已废弃，迁至 Wan 2.7 |
| ~~HappyHorse-参考生视频~~ | ~~happyhorse-reference-to-video-api-reference~~ | ❌ 已废弃，迁至 Wan 2.7 |
| ~~HappyHorse-视频编辑~~ | ~~happyhorse-video-edit-api-reference~~ | ❌ 已废弃，无直接替代 |
