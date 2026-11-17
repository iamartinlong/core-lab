# 04 · 交接文档

> 最后更新：2026-11-17 · 交接人：WorkBuddy 智能设计助手

## 项目一句话

「CORE// 计算机解剖实验室」——单文件交互式网站，拆开一台计算机：7 大部件图鉴、CPU 五层解剖工作台、启动链路协作动画。纯 HTML/CSS/JS，零依赖。

- **仓库**：https://github.com/iamartinlong/core-lab.git
- **设计稿**：Ardot fileId `746599557890956`（https://ardot.tencent.com/file/746599557890956）
- **License**：MIT

## 文件清单

```
core-lab/
├── index.html          # 整站（CSS/JS 内联，~75KB）— 唯一需要改的代码文件
├── assets/             # 8 张站点渲染图（cpu/mainboard/ram/gpu/ssd/psu/cooling/exploded.png）
├── design-source/
│   ├── component-renders.miora          # 画板素材板（8 张图 + 布局）
│   └── component-renders_assets/        # 8 张 AI 渲染原图（原始文件名，与 assets 内容一致）
├── docs/               # 本目录：需求 / 设计方案 / 实施记录 / 交接
│   ├── 01-requirements.md
│   ├── 02-design-spec.md
│   ├── 03-development-log.md
│   └── 04-handover.md
├── README.md
└── .gitignore
```

## 如何运行 / 修改

- 运行：双击 `index.html` 即可（file:// 已验证）；或 `python -m http.server 8765 --directory core-lab`。
- 改样式/脚本：全在 `index.html` 内联，无构建步骤，改完刷新即生效。
- 改完建议跑一遍检查：7 个弹层能开能关、工作台 5 层切换联动、播放/暂停/倍速、主题切换后刷新保持、390 宽手机模拟无横向滚动条。

## 关键代码位置（index.html 内）

| 功能 | 关键 ID / class |
|---|---|
| 弹层 | `#modalImg`、`#modalSpecs`、`#prevPart`/`#nextPart`，开关判断 `.open` class + visibility |
| 工作台图层 | `#layerChips`（tablist，键盘监听挂容器）、`#wbKicker`、`#wbFacts`、`#specFocus`、`.layer-fact` |
| 播放控制 | `#playBtn`、`#speedBtn`、`.console-seg`（六段 seek）、`.console-time` |
| 主题 | `#themeToggle`、localStorage key `corelab-theme` |
| 遮罩 | `#backdrop`（visibility 过渡 0.28s） |
| 图鉴 | `#atlasGrid`、`.part-card` |
| 层色 | 顶盖青 / 硅脂蓝 / 晶粒紫 / 基板蓝 / 触点绿（CSS 变量层控） |

## 已验证质量项（2026-11-12 全量审计，18 段全绿）

结构/可访问性（标题、alt、锚点、按钮标签、landmark）· 7 弹层全交互 · 工作台 5 层联动 + 键盘 · 播放链路 · 主题持久化 · 390~1280 零溢出 · 控制台零错误零资源失败。

## 已知限制 / 设计取舍

1. 延迟阶梯条为静态宽度（牺牲生长动画换打印/慢设备完整性），见 docs/03。
2. 参数为典型消费级示例值，非特定型号实测；渲染图页脚已声明 AI 生成。
3. `design-source/` 8 张原图与 `assets/` 内容重复（约 +14MB），是为溯源保留的；图多了再考虑 Git LFS。
4. `.miora` 画板文件在 GitHub 上只能下载查看，不会渲染预览。

## 后续待办建议（按优先级）

1. **GitHub Pages 上线**：仓库 Settings → Pages → 选 `main` 分支根目录，即可直接访问（单文件站最适合）。
2. **内容扩展**：如新增部件/层，沿用现有数据结构（弹层参数卡 4 条、工作台 5 层 facts），注意保持层级色一致性。
3. **图片优化**：渲染图单张 1.5~2MB，如加载速度有要求，可出 WebP 版本（`assets/` 替换 + `index.html` 引用同步改）。
4. **移动端微调**：767 断点以下已零溢出，但工作台五层高亮带在超窄屏的视觉密度可再打磨。

## 协作约定

- 改动一律走 commit，主分支 `main`；commit message 用 `feat:/fix:/chore:/docs:` 前缀。
- 文档随代码同步更新（本交接文档的「最后更新」日期也要改）。
