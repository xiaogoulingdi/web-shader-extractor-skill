---
name: web-shader-extractor
description: |
  从网页中提取 WebGL/Canvas/Shader 视觉特效代码，反混淆后移植为独立原生 JS 项目。
  触发条件：用户提供网址并要求提取 shader、提取特效、提取动画效果、提取 canvas 效果、
  复刻某网站的视觉效果、"把这个网站的背景效果扒下来"、重建玻璃/液体/FBO 折射、
  复盘高端网页的滚动叙事、图层合成、指针交互、portal/遮罩转场等。
---

# Web Shader Extractor

从网页提取 WebGL/Canvas/Shader 特效，反混淆并移植为独立项目。

核心原则：
- **先 1:1 复刻，确认正确后再考虑简化框架**
- **全程自主执行，不中断用户** — 提取是只读操作，安全性无风险。除 Phase 3 简化提议外，所有步骤自动完成。遇到问题自行判断最佳方案继续推进，只在需要用户做产品决策时才询问。
- **先证据，后审美调参** — 优先还原 shader、FBO、资源、时间基准、层级关系。只有确认 root cause 后再调参数。
- **同时复刻技术管线和运动叙事** — 高级网页的相似度来自 shader、图层、滚动节奏、指针交互和视觉编排共同作用。不要只复制 shader 而忽略动画阶段和主视觉对象。

## 前置条件：确认 Chrome DevTools MCP 可用

**开始任何提取工作前，必须先确认 Chrome DevTools MCP 已加载。**

用 ToolSearch 检索关键字 `chrome devtools navigate evaluate`：
- 若搜索结果包含 `mcp__chrome-devtools__navigate_page` → 可以开始
- 若结果中无任何 `mcp__chrome-devtools__*` 工具 → **立即提示用户安装后再继续**：

```
Chrome DevTools MCP 未安装，请运行：
  claude mcp add chrome-devtools --scope user npx chrome-devtools-mcp@latest
安装后重启 Claude Code，再重新触发本 skill。
```

不要绕过此检查降级到 Playwright 继续提取 — Playwright headless 在复杂 WebGL 站点下无法捕获 shader 源码和渲染管线，会导致提取不完整。

工具优先级：
1. **Chrome DevTools MCP**（必须）— `navigate_page` + `initScript` 注入 WebGL 拦截器，`evaluate_script` 查询运行时状态，`list_network_requests` 捕获资源。
2. **Playwright**（仅限 Phase 3 验证）— headless 截图对比移植效果，不用于侦察阶段。
3. **curl**（静态补充）— 获取原始 HTML，提取内嵌配置和密钥。

## Phase 1: 侦察

目标：一次页面加载，最大化采集所有运行时数据。

### 1.0 视觉与运动编排观察

在提取 shader 前，先人工观察并记录页面视觉结构。对于高端作品集、产品展示、campaign site 等强动效页面，读取 references/visual-choreography.md。

必须记录：
- 主视觉对象：如玻璃文字、箭头、鼠标、贴纸、背景幕布、portal 光线等。
- 动画阶段：进入、变形/翻转、显现、保持、退出/倒放。
- 交互作用范围：鼠标是否真实改变物体轨迹，还是只产生局部折射/烟雾/水纹。
- 图层归属：DOM、Canvas、Three.js、FBO、post-processing 分别负责什么。
- 叙事连续性：某个小物件是否在后续场景成为转场主角。

如果用户反馈“感觉不像”，先检查运动编排和图层关系，再追加 shader 复杂度。

### 1.1 WebGL 拦截注入

在页面脚本执行前注入拦截器，捕获：
- `gl.shaderSource()` → 所有 shader 源码（vertex + fragment）
- `gl.bindFramebuffer()` → FBO 切换序列（即渲染管线拓扑）
- `gl.uniform*()` → uniform 名称与初始值
- `gl.drawArrays() / drawElements()` → draw call 顺序与参数

同时也拦截 2D Canvas 的 `getContext('2d')` 以覆盖纯 Canvas 特效。

### 1.2 运行时查询

页面加载稳定后，主动查询：
- 框架版本：`window.THREE?.REVISION`、`window.BABYLON?.Engine?.Version` 等
- GL 能力：`gl.getSupportedExtensions()`、renderer info
- 全局配置：`__NEXT_DATA__`、`__NUXT__`、`window` 上的自定义全局变量
- Canvas 元素的 `data-engine` 属性
- DOM/canvas/WebGL 层级：canvas z-index、overlay、mask、clip-path、pointer-events、fixed layer 结构
- 滚动/指针状态来源：wheel/touch/pointermove 监听器、damping/easing、时间归一化方式

### 1.3 静态资源采集

并行用 curl 获取原始 HTML，与运行时数据交叉提取：
- 网络请求中的 JS bundle URL → 批量下载到 /tmp/
- HTML 内嵌的 JSON 配置、API 密钥
- 框架特征速查 → references/tech-signatures.md
- 可视资源：模型、纹理、字体、贴纸、背景图片、音频。记录哪些资源必须进入 FBO，哪些只需 DOM 展示。

### 1.4 路由判断

根据 URL、HTML 特征、运行时数据判断是否匹配已知平台：

| 匹配条件 | 跳转 |
|----------|------|
| unicorn.studio 域名或 embed 特征 | → references/unicorn-studio.md |
| shaders.com 域名或 Nuxt+TSL 特征 | → references/shaders-com.md |
| 无匹配 | → 继续 Phase 2 通用流程 |

### 1.5 深度分析（按需）

如果拦截器已捕获完整 shader 源码，可跳过 bundle 分析。仅在以下情况启动 Agent 分析 JS bundle：
- 拦截器未能捕获 shader（如 TSL 节点系统不经过 shaderSource）
- 需要理解混淆后的配置编码逻辑
- 需要还原 onBeforeCompile 注入的 GLSL 片段

Agent 分析规则 → references/extraction-workflow.md

## Phase 2: 移植

目标：将提取的 shader/配置/渲染管线重建为可独立运行的项目。

### 框架选择原则

根据提取结果判断最合适的移植目标：
- 纯 2D 全屏 shader → 原生 WebGL2（零依赖）
- 纯 2D Canvas 特效 → Vanilla JS（零依赖）
- 3D / PBR / GPGPU / onBeforeCompile → 保留原始框架（CDN importmap）
- 不确定 → 先用原始框架，Phase 3 再评估简化

详细策略 → references/porting-strategy.md

### 图层与 FBO 重建

涉及玻璃、液体、折射、mask、portal、多层 canvas/DOM 混合时，读取 references/layering-and-fbo.md。

必须确认：
- 玻璃/液体材质采样的 FBO 内容是什么。
- DOM 可见资源是否也需要被绘制进 FBO。
- update/render 顺序是否让折射采样到当前帧内容。
- DOM 层、Canvas FX 层、WebGL 层、文字 mask 层的 z-index 是否符合源站视觉关系。

常见错误：贴纸或图片只在 DOM 中显示，没有进入 refraction target，因此玻璃 shader 永远无法折射它们。

### 动画编排移植

将 Phase 1 的运动编排表转换成代码阶段：
- 用 normalized progress 定义 phase boundaries。
- 明确哪个对象驱动转场，而不是用无关对象叠在中心。
- 对进入/退出成对设计，特别是源站有“倒放收束”时。
- 文字显现优先使用真实遮罩、clip-path、stencil、FBO mask 或几何遮挡，而不是简单 opacity。
- 保持主视觉对象连续性，例如首页右下角小箭头在后续转场中放大/翻转。

如果动画“不对味”，优先检查 phase timing、主对象、mask 关系和最终状态是否残留过渡物件。

### 关键约束

- **参数严格对齐**：从源站提取的值直接使用，不手动调参补偿视觉差异（视觉差异说明有 root cause 未解决）
- **色彩空间一致**：全链路 linear，仅最终输出做 linear→sRGB
- **时间基准对齐**：确认原站用秒还是帧累加，移植后保持一致
- **层级一致**：确保可见层和采样层一致，尤其是 DOM/WebGL 混合场景。
- **交互语义一致**：区分“鼠标推动物体”“鼠标触发折射/水纹”“鼠标只影响光照/相机”。

## Phase 3: 交付

### 3.1 验证

打开移植后的页面，截图与原站对比。重点检查：
- 色彩/亮度是否一致
- 动画节奏是否匹配
- 多 pass 渲染的合成顺序是否正确
- 主视觉对象是否承担同样的转场叙事
- 指针/滚轮响应是否有同样的快慢和局部性
- 最终状态是否残留不该出现的转场对象

如发现差异，优先排查 root cause（色彩空间、时间基准、FBO 顺序），不要通过调参修补。

需要迭代调优或遇到浏览器自动化不稳定时，读取 references/validation-and-iteration.md。对于滚动叙事，必须使用多阶段截图或短录屏，不要只看单张截图。

### 3.2 简化提议（询问用户）

效果验证正确后，如果存在简化空间（如可以剥离框架改用原生 WebGL），向用户提议简化方案，由用户决定是否执行。

### 3.3 提取报告（询问用户）

询问用户是否生成 `EXTRACTION-REPORT.md`。报告内容：
- 来源/作者/平台/时间
- 目标效果描述
- 提取思路与迭代时间线
- 场景结构 / 渲染管线 / 关键资源
- 发现的关键经验
- 剩余已知差异
- 技术栈对比（原始 vs 移植）
- 运动编排表：进入/显现/保持/退出阶段
- 图层与 FBO 数据流：哪些资源进入采样纹理，哪些留在 DOM

### 3.4 发布与归因（按需）

如果用户要发布修改后的 skill、复刻流程或相关代码，读取 references/publishing-and-attribution.md。

发布前确认：
- 上游项目 license 是否允许 fork/修改/再发布。
- 是否保留原作者署名和 license。
- 是否避免发布目标网站的专有素材、密钥、完整 bundle 或未经授权代码。
- 是适合给上游提交 PR，还是维护个人 fork。

## Reference 索引

| 需要时 | 读取 |
|--------|------|
| 识别框架特征 | references/tech-signatures.md |
| Agent 提取 prompt + 反混淆规则 | references/extraction-workflow.md |
| 获取配置参数 | references/config-extraction.md |
| Three.js TSL 节点 shader 重建 | references/tsl-extraction.md |
| 编码/加密配置解码 | references/encoded-definitions.md |
| onBeforeCompile GLSL 注入陷阱 | references/shader-injection.md |
| 移植框架选择 + 项目结构 | references/porting-strategy.md |
| 高端网页视觉编排 / 动画叙事 | references/visual-choreography.md |
| DOM/WebGL/FBO 分层与折射重建 | references/layering-and-fbo.md |
| 动态验证、迭代和性能检查 | references/validation-and-iteration.md |
| 发布、fork、PR、署名和授权建议 | references/publishing-and-attribution.md |
| Unicorn Studio 专用流程 | references/unicorn-studio.md |
| shaders.com 专用流程 | references/shaders-com.md |
