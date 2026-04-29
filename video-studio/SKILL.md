---
name: video-studio
description: AI video generation studio with script writing, storyboarding, character management, and prompt engineering for Alibaba Cloud HappyHorse 1.0 & Wan 2.7.
---

# Video Studio 🎬

完整的 AI 视频创作工作室。从剧本到成片的完整工作流，支持 **HappyHorse 1.0 全系列** 和 **Wan 2.7 系列**。

---

## 🆕 HappyHorse 1.0 全系列（最新）

HappyHorse 1.0 是阿里云百炼最新一代视频模型，支持北京/新加坡地域，异步调用（创建任务 → 轮询获取），task_id 有效期 24 小时。

### HappyHorse 1.0 模型矩阵

| 模型 | 功能 | 模型名 | 时长 | 分辨率 | 特色 |
|------|------|--------|------|--------|------|
| **文生视频** | 文本 → 视频 | `happyhorse-1.0-t2v` | 3-15s | 720P/1080P | 物理真实、运动流畅 |
| **图生视频** | 首帧图 → 视频 | `happyhorse-1.0-i2v` | 3-15s | 720P/1080P | 首帧引导，宽高比自动跟随输入 |
| **参考生视频** | 多参考图 → 视频 | `happyhorse-1.0-r2v` | 3-15s | 720P/1080P | 支持 1-9 张参考图，characterN 指代 |
| **视频编辑** | 视频+指令/参考图 → 视频 | `happyhorse-1.0-video-edit` | 3-15s | 720P/1080P | 风格变换、局部替换、声音控制 |

### HappyHorse 通用调用规范

```
Endpoint: POST https://dashscope.aliyuncs.com/api/v1/services/aigc/video-generation/video-synthesis
Headers: X-DashScope-Async: enable（必须）
查询结果: GET https://dashscope.aliyuncs.com/api/v1/tasks/{task_id}
视频格式: MP4（H.264 编码），24fps
视频 URL 有效期: 24 小时，需及时下载
```

### HappyHorse 与 Wan 2.7 对比

| 维度 | HappyHorse 1.0 | Wan 2.7 |
|------|---------------|---------|
| 最短时长 | 3 秒 | 2 秒 |
| 最长时长 | 15 秒 | 15 秒（videoedit 10 秒） |
| R2V 参考图 | 1-9 张 | 1-5 张（wan2.6-r2v-flash） |
| 视频编辑 | 支持（最长 60s 输入） | 支持（最长 10 秒输出） |
| 音频驱动 | 不支持 | 支持（t2v/i2v） |
| 首尾帧 | 不支持 | 支持（i2v） |
| 视频续写 | 不支持 | 支持（i2v --first-clip） |
| 水印控制 | 支持（watermark 参数） | 支持（watermark 参数） |

## 核心能力

| 模块 | 功能 |
|------|------|
| **📝 剧本助手** | 从想法到完整剧本，自动结构化 |
| **👤 角色管理** | 定义角色、分配 reference、自动编号 |
| **🎞️ 分镜脚本** | 自动生成标准分镜表（镜头/景别/运动/时长/内容） |
| **🎬 视频生成** | t2v / i2v(首帧/首尾帧/续写) / r2v / videoedit 全模式 |
| **🔧 Prompt 工程** | 内置提示词模板、风格库、镜头语言库 |
| **✨ 专业特性** | 1080P、音频同步、首尾帧控制、指令编辑、视频续写 |

---

## 📋 模型选型

### HappyHorse 1.0 全系列（最新一代）

| 模型 | 功能 | 输入 | 分辨率 | 时长 | 首选场景 |
|------|------|------|--------|------|---------|
| **happyhorse-1.0-t2v** ⭐ | 文生视频 | 文本 prompt | 720P/1080P | 3-15s | 从零生成视频，物理真实、运动流畅 |
| **happyhorse-1.0-i2v** ⭐ | 图生视频 | 首帧图 + 文本(可选) | 720P/1080P | 3-15s | 图片动起来，宽高比自动跟随首帧 |
| **happyhorse-1.0-r2v** ⭐ | 参考生视频 | 1-9张参考图 + 文本 | 720P/1080P | 3-15s | 多角色融合，characterN 指代引用 |
| **happyhorse-1.0-video-edit** ⭐ | 视频编辑 | 视频 + 文本指令 + 参考图(可选) | 720P/1080P | 3-15s | 风格变换、局部替换、声音控制 |

### Wan 2.7 系列（传统模型）

| 模型 | 功能 | 输入 | 分辨率 | 时长 | 首选场景 |
|------|------|------|--------|------|---------|
| **wan2.7-t2v** | 文生视频 | 文本 + 音频(可选) | 720P/1080P | 2-15s | 从零生成视频，支持音频驱动 |
| **wan2.7-i2v** | 图生视频 | 首帧/首尾帧/首段视频 | 720P/1080P | 2-15s | 图片动起来、过渡、续写 |
| **wan2.7-videoedit** | 视频编辑 | 视频 + 文本指令 + 参考图(可选) | 720P/1080P | 2-10s | 指令编辑、风格转换、替换 |
| **wan2.6-r2v-flash** | 参考生视频 | 参考图/视频(最多5个) + 文本 | 720P/1080P | 2-10s | 角色复刻表演 |

> ⚠️ **HappyHorse 1.0 使用异步调用**（创建任务 → 轮询获取），必须设置 `X-DashScope-Async: enable` 请求头。
> ⚠️ Wan 2.7 使用 HTTP 调用，DashScope SDK 暂不支持 wan2.7 模型。
> ⚠️ 所有模型最高 **1080P**（非 4K），视频 URL **24 小时**过期。

---

## 📝 剧本写作

### 短剧剧本结构

```
【片名】一句话概括故事
【时长】10s / 15s / ...（单段）或 N 分钟（多段拼接）
【风格】3D卡通 / 写实电影 / 水墨 / 赛博朋克 / ...
【角色】角色1：描述 | 角色2：描述 | ...
【场景】主要场景描述
【分镜】镜头1 → 镜头2 → 镜头3 → ...
```

### 长视频策略（>15 秒）

Wan 2.7 单段最长 15 秒，制作长视频需分镜拆分：

```
3 分钟视频 = 12-18 个镜头 × 10-15 秒/段
流程：剧本 → 分镜 → 逐段生成 → ffmpeg 拼接 → 配乐
角色一致性：每个镜头使用相同的首帧参考图
```

### 分镜脚本格式

每个镜头必须包含以下要素：

```
镜头X [时长X秒]
- 景别：全景 / 中景 / 近景 / 特写
- 角度：平视 / 仰视 / 俯视 / 鸟瞰
- 运动：固定 / 推 / 拉 / 摇 / 移 / 跟 / 升 / 降
- 内容：画面描述（主体 + 动作 + 环境 + 光影 + 情绪）
```

### Prompt 写法公式

**文生视频（t2v）Prompt 公式：**
```
[景别] + [主体] + [动作] + [环境] + [光影] + [氛围/情绪] + [风格]

示例：
近景，一位穿着白色连衣裙的少女站在海边，微风吹起她的长发和裙摆，
夕阳的金色余晖洒在她身上，海面波光粼粼，背景是粉紫色的晚霞，
氛围宁静温柔，电影质感，浅景深
```

**Wan 2.7 多镜头叙事 Prompt（无需 shot_type 参数）：**
```
直接在 prompt 中用时间戳描述分镜：
第1个镜头[0-3秒] 全景：雨夜的纽约街头，霓虹灯闪烁。
第2个镜头[3-6秒] 中景：侦探进入老旧建筑，雨水打湿外套。
第3个镜头[6-9秒] 特写：侦探眼神坚毅专注，远处传来警笛声。
```

**参考生视频（r2v）Prompt 公式（多角色）：**
```
wan2.7: 视频1/图1的角色 + 动作/台词 + 视频2/图2的角色 + 反应 + 环境
wan2.6: character1 + 动作/台词 + character2 + 反应 + 环境
```

---

## 🎞️ 分镜脚本模板

### 标准分镜表

| 镜头 | 时长 | 景别 | 角度 | 运动 | 画面描述 |
|------|------|------|------|------|----------|
| 1 | 3s | 全景 | 俯视 | 缓慢推进 | 城市天际线，黄昏，灯光渐次亮起 |
| 2 | 2s | 中景 | 平视 | 固定 | 主角站在窗前，背影，手中端着咖啡杯 |
| 3 | 3s | 近景 | 侧视 | 缓慢右摇 | 主角转头望向窗外，表情若有所思 |
| 4 | 2s | 特写 | 平视 | 固定 | 咖啡杯中升起的袅袅热气 |
| 5 | 5s | 中景→全景 | 平视 | 缓慢拉远 | 主角放下杯子，走向门口，镜头跟随拉远 |

---

## 🎨 镜头语言库

### 景别定义

| 景别 | 画面范围 | 用途 |
|------|----------|------|
| **大远景** | 环境为主，人物很小 | 交代场景、氛围 |
| **全景** | 人物全身 + 部分环境 | 人物与环境关系 |
| **中景** | 人物膝盖以上 | 叙事主力，对话场景 |
| **近景** | 人物胸部以上 | 表情、情绪 |
| **特写** | 面部/手部/物体细节 | 强调关键元素 |
| **大特写** | 眼睛/嘴唇/微小细节 | 极致情绪表达 |

### 镜头运动

| 运动 | 效果 | 示例 |
|------|------|------|
| **固定** | 稳定、客观 | 对话、静物 |
| **推** | 聚焦、紧张 | 发现、揭示 |
| **拉** | 远离、释然 | 结局、离别 |
| **摇** | 环视、搜索 | 探索、观察 |
| **移** | 跟随、流动 | 行走、奔跑 |
| **跟** | 伴随、沉浸 | 追逐、同行 |
| **升** | 开阔、升华 | 高潮、领悟 |
| **降** | 回归、压抑 | 失落、沉思 |

### 光影风格

| 风格 | 描述 |
|------|------|
| **自然光** | 日光、黄昏、黎明，温暖真实 |
| **电影光** | 三点布光，专业质感 |
| **低照度** | 暗调、霓虹、赛博朋克 |
| **逆光** | 轮廓光、剪影、唯美 |
| **顶光** | 戏剧性、悬疑感 |
| **侧光** | 立体感、质感突出 |

---

## 🎬 HappyHorse 1.0 视频生成

### 通用异步调用流程

HappyHorse 1.0 全部采用异步调用模式：

```
步骤1: 创建任务（POST）
POST https://dashscope.aliyuncs.com/api/v1/services/aigc/video-generation/video-synthesis
Headers: X-DashScope-Async: enable
返回: task_id

步骤2: 轮询获取结果（GET）
GET https://dashscope.aliyuncs.com/api/v1/tasks/{task_id}
返回: video_url（成功后），有效期 24 小时
```

### HappyHorse 文生视频（happyhorse-1.0-t2v）

```bash
# 基础调用
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/video-generation/video-synthesis' \
    -H 'X-DashScope-Async: enable' \
    -H "Authorization: Bearer $DASHSCOPE_API_KEY" \
    -H 'Content-Type: application/json' \
    -d '{
    "model": "happyhorse-1.0-t2v",
    "input": {
        "prompt": "一座由硬纸板和瓶盖搭建的微型城市，在夜晚焕发出生机。一列硬纸板火车缓缓驶过，小灯点缀其间，照亮前路。"
    },
    "parameters": {
        "resolution": "720P",
        "ratio": "16:9",
        "duration": 5
    }
}'

# 查询结果
curl -X GET https://dashscope.aliyuncs.com/api/v1/tasks/{task_id} \
    --header "Authorization: Bearer $DASHSCOPE_API_KEY"
```

**参数说明：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| model | string | ✅ | `happyhorse-1.0-t2v` |
| prompt | string | ✅ | 文本提示词，最长 5000 非中文字符或 2500 中文字符 |
| resolution | string | ❌ | `720P` 或 `1080P`（默认） |
| ratio | string | ❌ | `16:9`（默认）/ `9:16` / `1:1` / `4:3` / `3:4` |
| duration | integer | ❌ | `[3, 15]` 秒，默认 5 |
| watermark | boolean | ❌ | `true`（默认，添加"Happy Horse"水印）/ `false` |
| seed | integer | ❌ | `[0, 2147483647]` |

### HappyHorse 图生视频（happyhorse-1.0-i2v）

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/video-generation/video-synthesis' \
    -H 'X-DashScope-Async: enable' \
    -H "Authorization: Bearer $DASHSCOPE_API_KEY" \
    -H 'Content-Type: application/json' \
    -d '{
    "model": "happyhorse-1.0-i2v",
    "input": {
        "prompt": "一只猫在草地上奔跑",
        "media": [
            {
                "type": "first_frame",
                "url": "https://example.com/first-frame.png"
            }
        ]
    },
    "parameters": {
        "resolution": "720P",
        "duration": 5
    }
}'
```

**关键区别 vs Wan 2.7 i2v：**
- ✅ 只支持首帧（`first_frame`），不支持首尾帧、视频续写
- ✅ 宽高比**自动跟随输入首帧**，不支持 `ratio` 参数
- ✅ 输出分辨率自动缩放到与首帧相近的总像素
- ❌ 不支持音频驱动
- ✅ 图像限制：JPEG/JPG/PNG/WEBP，宽高≥300px，宽高比 1:2.5~2.5:1，≤10MB

**参数说明：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| model | string | ✅ | `happyhorse-1.0-i2v` |
| prompt | string | ❌ | 文本引导词，可选 |
| media[].type | string | ✅ | 固定为 `first_frame` |
| media[].url | string | ✅ | 首帧图 URL |
| resolution | string | ❌ | `720P` 或 `1080P`（默认） |
| duration | integer | ❌ | `[3, 15]` 秒，默认 5 |
| watermark | boolean | ❌ | `true`（默认）/ `false` |
| seed | integer | ❌ | `[0, 2147483647]` |

### HappyHorse 参考生视频（happyhorse-1.0-r2v）

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/video-generation/video-synthesis' \
    -H 'X-DashScope-Async: enable' \
    -H "Authorization: Bearer $DASHSCOPE_API_KEY" \
    -H 'Content-Type: application/json' \
    -d '{
    "model": "happyhorse-1.0-r2v",
    "input": {
        "prompt": "身着红色旗袍的女性character1，镜头先以侧面中景勾勒旗袍修身剪裁与S型曲线，随即切换至低角度仰拍，捕捉她轻抬玉手展开折扇character2时流苏耳坠character3随头部转动轻盈摆动的细节",
        "media": [
            { "type": "reference_image", "url": "https://example.com/girl.jpg" },
            { "type": "reference_image", "url": "https://example.com/fan.jpg" },
            { "type": "reference_image", "url": "https://example.com/earrings.jpg" }
        ]
    },
    "parameters": {
        "resolution": "720P",
        "ratio": "16:9",
        "duration": 5
    }
}'
```

**characterN 指代规则：**
- `character1` 对应 `media[0]`，`character2` 对应 `media[1]`，以此类推
- 支持 **1-9 张**参考图（vs Wan 2.6 最多 5 张）
- 图像要求：JPEG/JPG/PNG/WEBP，短边≥400px（推荐 720P+），≤10MB

**参数说明：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| model | string | ✅ | `happyhorse-1.0-r2v` |
| prompt | string | ✅ | 必须含 characterN 指代 |
| media[].type | string | ✅ | 固定为 `reference_image` |
| media[].url | string | ✅ | 参考图 URL（1-9 张） |
| resolution | string | ❌ | `720P` 或 `1080P`（默认） |
| ratio | string | ❌ | `16:9`（默认）/ `9:16` / `1:1` / `4:3` / `3:4` |
| duration | integer | ❌ | `[3, 15]` 秒，默认 5 |
| watermark | boolean | ❌ | `true`（默认）/ `false` |
| seed | integer | ❌ | `[0, 2147483647]` |

### HappyHorse 视频编辑（happyhorse-1.0-video-edit）

```bash
curl --location 'https://dashscope.aliyuncs.com/api/v1/services/aigc/video-generation/video-synthesis' \
    -H 'X-DashScope-Async: enable' \
    -H "Authorization: Bearer $DASHSCOPE_API_KEY" \
    -H 'Content-Type: application/json' \
    -d '{
    "model": "happyhorse-1.0-video-edit",
    "input": {
        "prompt": "让视频中的马头人身角色穿上图片中的条纹毛衣",
        "media": [
            { "type": "video", "url": "https://example.com/input.mp4" },
            { "type": "reference_image", "url": "https://example.com/sweater.webp" }
        ]
    },
    "parameters": {
        "resolution": "720P"
    }
}'
```

**输入视频限制：**
- 格式：MP4/MOV（建议 H.264 编码）
- 时长：3-60 秒
- 分辨率：长边≤2160px，短边≥320px
- 宽高比：1:2.5~2.5:1
- 文件大小：≤100MB，帧率>8fps

**输出规则：**
- 输入≤15s → 输出与输入时长一致
- 输入>15s → 自动截取前 15 秒作为输出

**声音控制：**
- `auto`（默认）：模型自行控制
- `origin`：保留输入视频原始声音

**参数说明：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| model | string | ✅ | `happyhorse-1.0-video-edit` |
| prompt | string | ✅ | 编辑意图描述 |
| media[].type | string | ✅ | `video`（必传 1 个）/ `reference_image`（可选 0-5 张） |
| resolution | string | ❌ | `720P` 或 `1080P`（默认） |
| watermark | boolean | ❌ | `true`（默认）/ `false` |
| audio_setting | string | ❌ | `auto`（默认）/ `origin` |
| seed | integer | ❌ | `[0, 2147483647]` |

---

## 🎬 Wan 2.7 视频生成

> 💡 **图像前置工作流**：推荐先用 Wan 2.7 图像生成（文生图/组图）创建高质量首帧参考图，再使用 HappyHorse i2v 或 Wan 2.7 i2v 生成视频。
> 完整图像能力请参阅 `aliyun-visual` 技能。

### 模式选择

```
想从零开始生成 → HappyHorse t2v（文生视频）或 Wan 2.7 t2v
有一张图想动起来 → HappyHorse i2v（首帧）或 Wan 2.7 i2v
有首帧和尾帧图 → Wan 2.7 i2v（首尾帧，HappyHorse 不支持）
有已有视频想续写 → Wan 2.7 i2v（视频续写，HappyHorse 不支持）
有参考图复刻角色 → HappyHorse r2v（1-9 张参考图）或 Wan 2.6 r2v-flash
想编辑已有视频 → HappyHorse video-edit（指令+参考图）或 Wan 2.7 videoedit
```

### 文生视频（wan2.7-t2v）

```bash
# 基础用法 - 单镜头
/video --mode t2v "近景，一只布偶猫坐在窗台上，午后阳光洒在它身上"

# 多镜头叙事 - 直接在 prompt 中描述
/video --mode t2v \
  "第1个镜头[0-3秒] 全景：森林深处，阳光透过树冠。
   第2个镜头[3-6秒] 中景：一只布偶猫从草丛中探出头。
   第3个镜头[6-9秒] 特写：猫的眼睛闪着好奇的光。" \
  --duration 9 --resolution 1080P --ratio 16:9

# 带音频
/video --mode t2v "prompt" --audio https://example.com/bgm.mp3 --duration 10

# 反向提示词
/video --mode t2v "一只猫在花园" --negative-prompt "花朵,文字,水印"
```

### 图生视频（wan2.7-i2v）

```bash
# 首帧生视频
/video --mode i2v "prompt" --first first_frame.jpg --duration 10

# 首尾帧生视频（精确控制起止画面）
/video --mode i2v "prompt" --first start.jpg --last end.jpg --duration 10

# 首帧 + 音频驱动
/video --mode i2v "prompt" --first face.jpg --audio voice.mp3 --duration 10

# 视频续写（基于已有视频生成后续）
/video --mode i2v "一个女孩对镜自拍后背着书包出门" \
  --first-clip clip1.mp4 --duration 10

# 首段视频 + 尾帧续写
/video --mode i2v "prompt" \
  --first-clip clip1.mp4 --last end.jpg --duration 10
```

### 参考生视频（wan2.6-r2v-flash）

> ⚠️ Wan 2.7 暂无独立 R2V 模型，当前使用 wan2.6-r2v-flash

**wan2.6 角色引用方式：character1、character2...**

```bash
# 单角色
/video --mode r2v --ref character.mp4 \
  "character1在花园里散步，享受阳光"

# 多角色互动（最多5个角色）
/video --mode r2v \
  --ref girl.mp4 --ref boy.mp4 \
  "character1对character2说：你好！character2笑着回应" \
  --duration 10 --shot-type multi

# 多角色 + 道具 + 背景
/video --mode r2v \
  --ref char1.mp4 --ref char2.mp4 --ref guitar.jpg --ref cafe.jpg \
  "character1抱着character3弹奏，character2坐在对面倾听，背景是character4" \
  --duration 10 --shot-type multi

# 无声视频
/video --mode r2v --ref character.mp4 \
  "character1在跳舞" \
  --duration 5 --shot-type multi --no-audio
```

### 视频编辑（wan2.7-videoedit）

```bash
# 风格转换
/video --mode videoedit "将整个画面转换为黏土风格" \
  --video original.mp4 --duration 5

# 内容替换（参考图）
/video --mode videoedit "将视频中女孩的衣服替换为图片中的衣服" \
  --video original.mp4 --ref-image clothes.png --duration 5

# 运镜调整
/video --mode videoedit "在结尾添加缓慢推进效果" \
  --video original.mp4 --duration 8

# 保留原声
/video --mode videoedit "添加雨天气效果" \
  --video original.mp4 --audio-setting origin --duration 5
```

---

## 📋 完整工作流示例

### 示例：制作一个 10 秒的咖啡厅相遇短片

**步骤 1：剧本**
```
【片名】咖啡厅相遇
【时长】10秒
【风格】日系小清新，温暖柔和
【角色】女孩：黑色长发，白衬衫，20岁 | 男孩：短发，蓝毛衣，20岁
【场景】街角咖啡厅，午后阳光，木质桌椅，窗边绿植
【分镜】
  镜头1[3s] 全景→中景 推：女孩坐在窗边看书，阳光透过玻璃
  镜头2[3s] 中景 固定：男孩推门进来，风铃响起，女孩抬头
  镜头3[4s] 近景 缓慢右摇：男孩走向女孩，两人相视而笑
```

**步骤 2：wan2.7-t2v 一键生成**
```
/video --mode t2v \
  "第1个镜头[0-3秒] 全景推近：女孩坐在咖啡厅窗边看书，午后的阳光透过玻璃洒在她身上。
   第2个镜头[3-6秒] 中景固定：男孩推门进来，风铃响起，女孩抬头看向他。
   第3个镜头[6-10秒] 近景缓慢右摇：男孩走向女孩，两人相视而笑。
   日系小清新风格，温暖柔和的光线，木质桌椅，窗边绿植" \
  --duration 10 --resolution 1080P --ratio 16:9
```

---

## 🔧 参数参考

### wan2.7-t2v

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--mode` | t2v | t2v |
| `--duration` | 时长 [2, 15] 秒 | 5 |
| `--resolution` | 720P / 1080P | 1080P |
| `--ratio` | 16:9 / 9:16 / 1:1 / 4:3 / 3:4 | 16:9 |
| `--audio` | 音频 URL (wav/mp3, 2-30s) | 无（自动配音） |
| `--negative-prompt` | 反向提示词 | 无 |
| `--prompt-extend` | 智能改写 | true |
| `--seed` | 随机种子 | random |
| `--watermark` | AI 水印 | off |

### wan2.7-i2v

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--mode` | i2v | i2v |
| `--first` | 首帧图像 URL | - |
| `--last` | 尾帧图像 URL | - |
| `--first-clip` | 首段视频 URL（续写用） | - |
| `--audio` | 驱动音频 URL (wav/mp3, 2-30s) | 无（自动配音） |
| `--duration` | 时长 [2, 15] 秒 | 5 |
| `--resolution` | 720P / 1080P | 1080P |
| `--negative-prompt` | 反向提示词 | 无 |

### wan2.7-videoedit

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--mode` | videoedit | videoedit |
| `--video` | 待编辑视频 URL | - |
| `--ref-image` | 参考图像 URL（最多3张） | - |
| `--duration` | 时长 [2, 10] 秒 | 输入视频时长 |
| `--resolution` | 720P / 1080P | 1080P |
| `--audio-setting` | auto / origin | auto |

### wan2.6-r2v-flash

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--mode` | r2v | r2v |
| `--ref` | 参考文件 URL（可重复，最多5个） | - |
| `--duration` | 时长 [2, 10] 秒 | 5 |
| `--size` | 1280*720 / 1920*1080 等 | 1920*1080 |
| `--shot-type` | single / multi | single |
| `--no-audio` | 无声视频 | 有声 |

---

## 📐 分辨率对照表

| 档位 | 16:9 | 9:16 | 1:1 | 4:3 | 3:4 |
|------|------|------|-----|-----|-----|
| **720P** | 1280×720 | 720×1280 | 960×960 | 1104×832 | 832×1104 |
| **1080P** | 1920×1080 | 1080×1920 | 1440×1440 | 1648×1248 | 1248×1648 |

---

## ⚠️ 注意事项

### HappyHorse 1.0
- **异步调用**：必须设置 `X-DashScope-Async: enable` 请求头
- **Task 生命周期**：创建任务 → 轮询（建议 15s 间隔）→ 获取 video_url → 下载（URL 24h 过期）
- **跨地域限制**：模型、Endpoint URL、API Key 必须属于同一地域（北京/新加坡）
- **RPS 限制**：查询接口默认 RPS 为 20
- **Prompt 截断**：超过 5000 非中文字符或 2500 中文字符自动截断
- **水印**：默认添加 "Happy Horse" 水印，可设 `watermark: false` 关闭
- **R2V character 指代**：character1 对应 media[0]，最多支持 9 张参考图
- **i2v 宽高比**：自动跟随首帧，不支持 ratio 参数
- **video-edit 输出**：输入≤15s 则输出与输入一致，输入>15s 截取前 15s

### Wan 2.7
- **Wan 2.7 使用 HTTP 调用**，SDK 暂不支持 wan2.7
- 单段最长 **15 秒**（t2v/i2v），视频编辑最长 **10 秒**（Wan 2.7 videoedit）
- 最高分辨率 **1080P**（非 4K）
- 所有生成为异步任务，提交后返回 task_id
- 视频 URL **24 小时**过期，及时下载
- wan2.7-t2v **不需要** shot_type 参数，自然语言描述分镜即可
- 反向提示词 wan2.7-t2v 和 wan2.7-i2v 均支持
- 多角色互动使用 wan2.6-r2v-flash，最多 **5 个角色**
- i2v 输出宽高比由**输入素材**（首帧/首视频）决定
- 提示词需明确动作逻辑（如 "猫扑向狐狸" 而非 "猫和狐狸打架"）
