# 沉浸式翻译 · Immersive Translate（Chrome 扩展）

网页双语对照翻译插件：**保留原文版式，把译文逐段嵌入原文下方**。零构建、零依赖，纯原生 Chrome Extension MV3 实现。

![version](https://img.shields.io/badge/version-1.0.0-blue) ![mv3](https://img.shields.io/badge/manifest-v3-green)

## ✨ 功能

| 能力 | 说明 |
| --- | --- |
| 双语对照 | 译文插入原文下方，字号/对齐自动跟随原文（标题、列表、表格都适配） |
| 三种显示方式 | 双语对照 / 仅译文（隐藏原文）/ 悬停显示（鼠标移上去才出译文） |
| 多翻译引擎 | **DeepSeek**（推荐）、**任意 OpenAI 兼容接口**（硅基流动 / Moonshot / 通义 / 智谱 / Ollama…）、**Google 免费翻译**（零配置） |
| 智能段落识别 | 只翻正文，自动跳过代码块、按钮、表单、隐藏元素；列表/表格内译文不会破坏结构 |
| 动态内容 | MutationObserver 跟进无限滚动、SPA 路由等动态加载的内容 |
| 划词翻译 | 选中文字出现「译」气泡，点开译文卡片，一键复制 |
| 悬浮球 | 单击翻译/还原，拖动改位置，右键快捷菜单 |
| 缓存 | 段落级翻译缓存（本地存储），相同内容不重复请求、刷新秒出 |
| 站点规则 | 按域名设置「总是自动翻译 / 从不翻译」，优先级高于全局 |
| 术语表 | `原文=译文` 每行一条，强制进提示词 |
| 排除选择器 | 自定义不翻译的 CSS 选择器 |
| 快捷键 | `Alt+T` 翻译 / 还原当前页面 |
| 右键菜单 | 翻译此页面 / 还原原文 / 翻译选中文本 / 设置 |

## 📦 安装（开发者模式）

1. 打开 `chrome://extensions`
2. 右上角打开 **开发者模式**
3. 点击 **加载已解压的扩展程序**，选择本 `immersive-translate` 文件夹

> Edge：`edge://extensions` 同样操作。首次加载后建议固定到工具栏。

## 🚀 快速上手

1. 点击工具栏图标 → 选择目标语言（默认简体中文）
2. 点击「翻译此页面」或按 `Alt+T`
3. 想要更高质量译文：右键悬浮球 → 设置 → 翻译服务切换为 **DeepSeek**，填入 API Key → 「测试连接」

> 不配置任何 Key 时默认使用 Google 免费翻译，开箱即用；但中国大陆网络环境通常无法直连，推荐配置 DeepSeek。

## ⚙️ 翻译服务配置

| 服务商 | Base URL | 模型 | 说明 |
| --- | --- | --- | --- |
| DeepSeek | `https://api.deepseek.com/v1` | `deepseek-chat` | 推荐，中文质量好、价格低 |
| OpenAI 兼容 | 自定义 | 自定义 | 硅基流动 `https://api.siliconflow.cn/v1`、Moonshot `https://api.moonshot.cn/v1`、Ollama `http://localhost:11434/v1` 等 |
| Google 免费 | — | — | 无需 Key，走 `translate.googleapis.com` |

LLM 批量翻译：每次请求携带多段（默认 10 段，`[[n]]` 编号对应），失败自动二分降级，坏一段不影响整批。

## 🗂 目录结构

```
immersive-translate/
├── manifest.json          # MV3 清单（权限、快捷键、右键菜单）
├── icons/                 # 16/32/48/128 图标
├── src/
│   ├── background.js      # Service Worker：请求队列、并发、重试、缓存、消息路由
│   ├── content.js         # 内容脚本：段落识别、译文注入、悬浮球、划词、动态监听
│   ├── content.css        # 译文排版样式（三种显示模式）
│   ├── popup.html/js/css  # 工具栏弹窗
│   ├── options.html/js/css# 设置页（服务、显示、行为、高级、站点规则、缓存）
│   └── lib/
│       ├── defaults.js    # 默认配置、语言表、存储读写
│       └── providers.js   # 翻译引擎适配（LLM / Google）+ 提示词 + 解析
└── tools/make-icons.swift # 图标生成脚本（macOS）
```

## 🔐 权限说明

- `storage` / `unlimitedStorage`：保存设置与翻译缓存（仅本地 / 浏览器账号同步，不上传第三方）
- `scripting` + `host_permissions: <all_urls>`：在页面中注入译文（仅在用户主动触发翻译时执行）
- 翻译请求只发往你在设置中配置的翻译服务商

## 🧩 常见问题

- **点了没反应？** 刷新页面后再试；`chrome://` 内置页与应用商店页无法注入。
- **部分段落没翻译？** 可能被语言检测判定为目标语言、或命中排除选择器/最短长度限制（设置里可调）。
- **提示 429 / 限流？** 降低「并发请求数」或「单次请求段落数」。
- **PDF？** Chrome 内置 PDF 查看器不支持内容脚本，请下载后使用其他工具。
- **file:// 本地文件？** 需在扩展详情页打开「允许访问文件网址」。
# immersive-translate
