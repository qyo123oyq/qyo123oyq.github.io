---
layout: post
title: "AI 辅助开源的实战笔记:3 个 PR 的踩坑与修复"
date: 2026-06-26 12:00:00 +0800
tags: [开源贡献, AI辅助, PR, TypeScript, Kotlin, React]
description: "3 个开源项目的 PR 贡献记录:从 bug 定位、代码修复到提交流程的完整复盘。其中 legado-with-MD3 的 PR 已被合并。"
---

> 用 AI 辅助参与开源,真正难的不是「让 AI 写代码」,而是「定位问题、验证修复、写出让维护者一目了然的 PR」。这篇是我最近给 3 个开源项目提 PR 的完整复盘。

## 📊 一图看完

<div class="stats">
  <div class="stat-card"><div class="num">3</div><div class="label">提交的 PR</div></div>
  <div class="stat-card"><div class="num">1</div><div class="label">已合并</div></div>
  <div class="stat-card"><div class="num">2</div><div class="label">待合并</div></div>
  <div class="stat-card"><div class="num">3</div><div class="label">技术栈</div></div>
</div>

## 📋 贡献时间线

<div class="timeline">
  <div class="timeline-item">
    <div class="time">2026-06-24</div>
    <div class="content"><strong>MissionSquad/mcp-github #3</strong> — 修复 get_rate_limit 因 GitHub API 字段变更导致的 schema 校验失败 <span class="tag tag-pending">待合并</span></div>
  </div>
  <div class="timeline-item">
    <div class="time">2026-06-26</div>
    <div class="content"><strong>jimuzhe/tiez-clipboard #135</strong> — 修复搜索栏呼出时标签下拉框遮挡分类筛选按钮的布局问题 <span class="tag tag-pending">待合并</span></div>
  </div>
  <div class="timeline-item">
    <div class="time">2026-06-26</div>
    <div class="content"><strong>HapeLee/legado-with-MD3 #1568</strong> — 修复书架手动排序模式下拖拽无视觉反馈的交互问题 <span class="tag tag-merged">已合并</span></div>
  </div>
</div>

## 🔧 PR 详情

<div class="pr-card">
  <div class="repo">📦 <a href="https://github.com/MissionSquad/mcp-github">MissionSquad/mcp-github</a> · PR <a href="https://github.com/MissionSquad/mcp-github/pull/3">#3</a></div>
  <h3>fix(rate-limit): make unstable resource fields optional and allow passthrough <span class="tag tag-pending">待合并</span></h3>
  <div class="meta">
    <span class="tag tag-fix">fix</span>
    <span class="tag tag-bug">bug</span>
    <span>TypeScript</span>
    <span>~6 行改动</span>
  </div>
  <div class="desc">
    <strong>问题:</strong>调用 <code>get_rate_limit</code> 时报错 <code>MCP error -32603</code>,提示 <code>resources.code_scanning_upload</code> 字段必须存在。排查发现 GitHub API 已不再返回该字段,但 Zod schema 把它标为必填项。
  </div>
  <div class="highlight-box">
    💡 <strong>核心修复:</strong>将 5 个不稳定字段改为 <code>.optional()</code>,并添加 <code>.passthrough()</code> 容忍未来新增字段。同时把启动方式从 <code>npx</code> 改为本地构建产物,解决了 npx 安装日志污染 stdout 导致的 JSON-RPC 解析错误。
  </div>
</div>

修复前后的 schema 对比:

```typescript
// 修复前
export const RateLimitSchema = z.object({
  resources: z.object({
    core: RateLimitResourceSchema,
    code_scanning_upload: RateLimitResourceSchema,  // 必填但已不存在
    // ...
  }),
});

// 修复后
export const RateLimitSchema = z.object({
  resources: z.object({
    core: RateLimitResourceSchema,
    code_scanning_upload: RateLimitResourceSchema.optional(),  // 可选
    // ...
  }).passthrough(),  // 容忍额外字段
});
```

<div class="pr-card">
  <div class="repo">📦 <a href="https://github.com/jimuzhe/tiez-clipboard">jimuzhe/tiez-clipboard</a> · PR <a href="https://github.com/jimuzhe/tiez-clipboard/pull/135">#135</a></div>
  <h3>fix(AppHeader): move type filter buttons above search input to avoid tags-dropdown overlay <span class="tag tag-pending">待合并</span></h3>
  <div class="meta">
    <span class="tag tag-fix">fix</span>
    <span class="tag tag-bug">bug</span>
    <span>React / TypeScript</span>
    <span>~20 行改动</span>
  </div>
  <div class="desc">
    <strong>问题:</strong>在剪贴板列表顶部向上滑动呼出搜索栏时,标签筛选下拉框(tags-dropdown)会覆盖「文本 / 图片 / 文件」等分类筛选按钮,导致用户无法点击筛选。
  </div>
  <div class="highlight-box">
    💡 <strong>核心修复:</strong>将分类筛选按钮的 DOM 位置移到搜索输入框和标签下拉框<strong>之前</strong>,确保它们始终在标签下拉框上方可见。
  </div>
</div>

DOM 层叠顺序的调整:

```text
// 修复前:筛选按钮在 tags-dropdown 之后
search-container
├── search-input
├── tags-dropdown (absolute, z-index: 100000)
└── type-filter-buttons   ← 被遮挡

// 修复后:筛选按钮在 tags-dropdown 之前
search-container
├── type-filter-buttons   ← 始终可见
├── search-input
└── tags-dropdown
```

<div class="pr-card">
  <div class="repo">📦 <a href="https://github.com/HapeLee/legado-with-MD3">HapeLee/legado-with-MD3</a> · PR <a href="https://github.com/HapeLee/legado-with-MD3/pull/1568">#1568</a></div>
  <h3>fix(bookshelf): improve manual sort drag visual feedback and selection handling <span class="tag tag-merged">已合并</span></h3>
  <div class="meta">
    <span class="tag tag-fix">fix</span>
    <span class="tag tag-bug">bug</span>
    <span>Kotlin / Jetpack Compose</span>
    <span>~4 行改动</span>
  </div>
  <div class="desc">
    <strong>问题:</strong>用户反馈在书架中选择「手动排序」后,无法拖拽移动书籍位置,且选中书籍时不显示选中状态。排查发现 <code>sh.calvin.reorderable</code> 库的 <code>ReorderableItem</code> 提供了 <code>isDragging</code> 参数但代码未使用,导致拖拽时无视觉反馈。
  </div>
  <div class="highlight-box">
    💡 <strong>核心修复:</strong>捕获 <code>isDragging</code> 状态,在拖拽时对书籍项应用 <code>.alpha(0.5f)</code> 半透明效果,提供清晰的拖拽视觉反馈。合并后根据 review 又把它改成 <code>Modifier.graphicsLayer { alpha = ... }</code> 把绘制推迟到 draw 阶段,避免不必要的重组。
  </div>
</div>

修复前后的 Compose 代码:

```kotlin
// 修复前
ReorderableItem(...) {
    BookItem(
        modifier = Modifier.longPressDraggableHandle(...),
        ...
    )
}

// 修复后
ReorderableItem(...) { isDragging ->
    BookItem(
        modifier = Modifier
            .longPressDraggableHandle(...)
            .graphicsLayer { alpha = if (isDragging) 0.5f else 1f },  // 拖拽视觉反馈
        ...
    )
}
```

> 这是我第一个被 4.8k star 项目合并的 PR,完整心路历程我单独写了篇 → [我成为了 legado-with-MD3 的贡献者]({% post_url 2026-06-26-became-legado-contributor %})。

## 💡 经验总结

### 1. 用 AI 辅助开源贡献的实战流程

整个流程可以概括为:**Fork 仓库 → 阅读源码定位问题 → AI 辅助分析 → 编写修复 → 本地验证 → 推送分支 → 创建 PR → 标注 AI 使用披露**。关键是 AI 能做代码分析、生成修复方案,但最终验证和决策需要人来把关。

### 2. 常见坑点

- **schema 过时:**API 响应结构随时间变化,Zod/JSON Schema 等校验层需要保持同步,使用 `.optional()` 和 `.passthrough()` 可以提高健壮性。
- **Npx 启动 MCP Server:**npx 的安装日志会写入 stdout,污染 JSON-RPC 协议流,改用本地构建产物可以避免。
- **DOM 层叠顺序:**绝对定位元素的 z-index 问题往往通过调整 DOM 顺序而非修改 z-index 解决更干净。
- **Compose 手势冲突:**拖拽和点击事件需要清晰分离,利用库提供的状态参数(如 `isDragging`)可以改善交互。

### 3. PR 描述规范

好的 PR 描述应该包含:**Problem(问题)→ Root Cause(根因)→ Changes(改动)→ Verification(验证)**。加上 Before/After 的代码对比和 AI 使用披露,让维护者一目了然。
