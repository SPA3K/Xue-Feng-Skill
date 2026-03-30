# Xue-Feng-Skill

> **帮你选择高考专业以及后续人生发展路线。基于张雪峰老师B站200+教学视频ASR转写分析提炼而成。**

本项目是一个 [Claude Code Skill](https://docs.anthropic.com/en/docs/claude-code)，将张雪峰老师公开分享的高考志愿填报方法论系统化提炼为 AI 可用的决策框架。语料总量 298,367 字符，覆盖 114 个视频。

## 工作流程

```
┌─────────────────────────────────────────────────────────────────┐
│                     Xue-Feng-Skill Pipeline                     │
└─────────────────────────────────────────────────────────────────┘

  ┌───────────┐     ┌───────────┐     ┌───────────┐
  │  Step 1   │     │  Step 2   │     │  Step 3   │
  │  B站搜索   │────▶│  视频下载   │────▶│  音频提取   │
  │           │     │           │     │ M4A → WAV │
  │ bilibili  │     │ bilibili  │     │  audio    │
  │ _search   │     │_downloader│     │_extractor │
  └───────────┘     └───────────┘     └───────────┘
       │                                    │
       │  search_results.json               │
       ▼                                    ▼
  ┌─────────────────────────────────────────────────┐
  │              data/videos/*.m4a                   │
  │              data/audio/*.wav                    │
  └─────────────────────────────────────────────────┘
                                                │
                                                ▼
  ┌───────────┐     ┌───────────┐     ┌───────────┐
  │  Step 6   │     │  Step 5   │     │  Step 4   │
  │ Skill生成  │◀────│  内容分析   │◀────│ ASR 转写   │
  │           │     │           │     │           │
  │  skill    │     │ content   │     │  whisper  │
  │_generator │     │ _analyzer │     │_transcriber│
  └───────────┘     └───────────┘     └───────────┘
       │                 │                   │
       ▼                 ▼                   ▼
  SKILL.md        analysis_report    data/transcripts/
                     .json            *.txt  *.json
```

### 各步骤说明

| Step | 模块 | 输入 | 输出 | 说明 |
|------|------|------|------|------|
| 1 | `bilibili_search` | 关键词 | `search_results.json` | 搜索张雪峰相关视频，按播放量过滤 |
| 2 | `bilibili_downloader` | 视频列表 | `data/videos/*.m4a` | 批量下载视频音频流 |
| 3 | `audio_extractor` | M4A 文件 | `data/audio/*.wav` | 转换为 Whisper 所需的 WAV 格式 |
| 4 | `whisper_transcriber` | WAV 文件 | `data/transcripts/*` | OpenAI Whisper ASR 语音转文字 |
| 5 | `content_analyzer` | 转写文本 | `analysis_report.json` | 提炼方法论、统计高频维度 |
| 6 | `skill_generator` | 分析报告 | `SKILL.md` | 生成 Claude Code Skill 文件 |

## 快速使用

### 安装 Skill

```bash
cp -r skills/xue-feng-skill ~/.claude/skills/
```

安装后在 Claude Code 中直接问专业选择相关问题即可自动激活。

### 运行 Pipeline（从零构建）

```bash
pip install -r requirements.txt
python pipeline.py                # 全流程
python pipeline.py --step asr     # 只运行 ASR
python pipeline.py --from-step asr  # 从 ASR 开始到最后
```

## 项目结构

```
├── SKILL.md                    # 生成的 Skill 文件（核心产出）
├── pipeline.py                 # 全流程编排脚本，支持断点恢复
├── requirements.txt
├── tools/
│   ├── bilibili_search.py      # B站视频搜索
│   ├── bilibili_downloader.py  # B站音频下载
│   ├── audio_extractor.py      # M4A → WAV 转换
│   ├── whisper_transcriber.py  # Whisper ASR 转写
│   ├── content_analyzer.py     # 内容分析与方法论提炼
│   ├── skill_generator.py      # Skill 文件生成
│   ├── title_filter.py         # 标题过滤
│   └── build_final_list.py     # 最终视频列表构建
├── skills/
│   └── xue-feng-skill/
│       └── SKILL.md            # Skill 安装源
└── data/
    ├── search_results.json     # 搜索结果
    ├── search_results_final.json
    └── transcripts/            # ASR 转写结果（137个视频）
        ├── *.txt               # 纯文本转写
        └── *.json              # 带时间戳的转写
```

## 数据说明

`data/transcripts/` 目录包含 137 个视频的 ASR 转写结果：

- **`.txt`** — 纯文本，可直接阅读
- **`.json`** — 包含分段时间戳信息（start/end/text）

转写使用 OpenAI Whisper `base` 模型完成。由于是自动语音识别，部分专有名词可能存在识别误差。

## ClaWHub

本 Skill 同步发布在 [ClaWHub](https://clawhub.com) 同名项目。

---

> 雪峰老师说过：**"我不是在教你怎么选专业，我是在教你怎么认识这个社会。"**
>
> 他的方法论本质上是一套社会现实的认知框架。专业会变、政策会变、行业会变，但"从现实出发、从自身条件出发、用排除法做选择"这套思维方式不会过时。
>
> 愿每一个用到这个Skill的人，都能像曾经被雪峰老师指点过的那些孩子一样，少走弯路，找到属于自己的路。

## License

MIT
