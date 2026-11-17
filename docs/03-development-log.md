# 03 · 实施与修复记录

## 技术选型（用户拍板）

- **单 HTML 文件**：CSS/JS 全部内联，零依赖、无 CDN、无构建
- **1440 设计稿基线 + 响应式重排**：断点 1439 / 1023 / 767，全宽度重排而非等比缩放
- 产物：`index.html`（约 75KB）+ `assets/` 8 张图

## 实现的交互清单

| 交互 | 实现要点 |
|---|---|
| 部件详情弹层 ×7 | 编号/标题/介绍/渲染图/4 参数卡/启动链路 STEP 标注；上一下一件循环切换、ESC、点遮罩关闭 |
| 卡片三态 | hover 上浮 + 发光描边，选中态 |
| 启动链路播放 | 播放/暂停、2× 倍速、六段进度条 seek、重播；数据总线脉冲 + CPU 四时钟阶段（取指/解码/执行/写回）+ 总线帧联动 |
| 拆解工作台 5 层 | 顶盖/导热硅脂/晶粒/基板/触点，逐层下探全联动（见下） |
| 主题切换 | 浅/深双主题，localStorage key `corelab-theme` 持久化 |
| 全局 | 滚动进度条、导航高亮、图层 chip tablist + ←/→ 键盘 |

## 工作台逐层下探（返工重写）

用户反馈「点击不同标记，视图好像没有变化」。根因：原图层 chip 点击只改左上角一个小标签文字，主视觉不动。

**教训：切换类交互必须联动主视觉 + 说明 + 数据面板，只动小角落等于没做。**

重写后每层切换同步：主图横向高亮带（层色：青/蓝/紫/蓝/绿）、LAYER 标签、图内锚点标签、L0x 角标、层说明（英文小标 + 中文正文）、三个信息块（材质/厚度/连接）、右侧「当前聚焦层」chip。ARIA tablist/tab/tabpanel + aria-selected，键盘 ←/→ 可切换。

## 关键技术坑（踩过的，务必记住）

1. **SVG 默认 fill**：装饰线稿 SVG 的 `<circle>`/`<path>` 不写 `fill` 会被填成纯黑（盖出大黑圆/黑三角）。所有线稿类 SVG 根上加 `fill="none"`。
2. **headless Chrome 三个假象**：
   - `--window-size=390` 被钳到最小 ~500px → 截图像溢出其实不是。真实 390 需 CDP `Emulation.setDeviceMetricsOverride {width:390, mobile:true}`。
   - `--virtual-time-budget` 不推进 CSS transition → 条形动画在长图里不可见（因此把延迟阶梯改静态宽度）。
   - 锚点跳转 + `scroll-behavior:smooth` 时截图会截到滚动中途 → 先 `scrollBehavior='auto'` 再滚。
3. **本机 curl 走代理**：测 127.0.0.1 必须加 `--noproxy "*"`，否则 502 误判服务挂了。
4. **延迟阶梯条**：`width:0 → var(--w)` 动画在无 JS/慢设备下空白 → 改静态 `width:var(--w)`，入场交给整块 `.reveal` 淡入。
5. **窄屏孤字**：`hero-meta` 的 `&nbsp;` 硬拼三项在窄屏孤字换行 → 三个 `<span>` + flex wrap + `white-space:nowrap`。

## 质量验证（最终审计，2026-11-12）

`audit-full.mjs` 全量审计 18 段全绿：

- 文档结构：22 级标题、9 图全 alt、19 锚点全命中、27 按钮全有标签、favicon、landmark 齐全
- 7 弹层开合 + 循环切换 + ESC + 遮罩关闭 ✅
- 工作台 5 层联动 + 键盘切换 ✅
- 播放链路（播放/暂停/2×/seek 300/300ms）✅
- 主题持久化（corelab-theme）✅
- 溢出扫描 390/768/1024/1280 **全 0** ✅
- 控制台无错误、无资源加载失败 ✅

验证工具链（`.workbuddy/tmp/`，仓库外）：`verify-site.mjs`（静态校验）、`audit-full.mjs`（CDP 全量审计）、`crop-long.mjs`/`crop-lv.mjs`（PNG 分段裁切比对，本机无 PIL/ImageMagick）。

## 本地预览

```bash
python -m http.server 8765 --bind 127.0.0.1 --directory core-lab
# 或直接双击 index.html（file:// 下相对路径 assets 已验证可加载）
```
