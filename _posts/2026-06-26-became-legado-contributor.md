---
layout: post
title: "我成为了 legado-with-MD3 的贡献者"
date: 2026-06-26 22:00:00 +0800
tags: [开源贡献, legado, Kotlin, Jetpack Compose, 心路历程]
description: "记录我第一次向 4.8k star 的开源项目 legado-with-MD3 提交 PR 并被合并的完整经历:从一个看书时发现的小 bug,到成为项目的 contributor。"
---

> 这是我的第一个被合并的开源 PR,也是我第一次出现在一个 4.8k star 项目的 contributor 列表里。写下这段经历,既是为了纪念,也是希望给「想参与开源但一直没敢动手」的人一点参考。

## 项目背景:legado-with-MD3

[legado-with-MD3](https://github.com/HapeLee/legado-with-MD3) 是开源阅读 App「阅读(Legado)」的 Material Design 3 重设计版,主要由 **Kotlin + Jetpack Compose** 构建,目前有 **4.8k star**。简单说,它把我每天用来抓小说看的那个 App 的界面用 MD3 风格重做了一遍,并且正在从传统 Android Views 往 Compose 迁移。

我是它的用户。所以这个故事的开头,不是「我想找个项目刷贡献」,而是「我真的被这个 bug 烦到了」。

## 起因:书架拖不动了

那天我在整理书架,切到「手动排序」模式想把几本书挪个位置,结果长按拖拽——没反应。不仅拖不动,选中书的时候也没有任何选中态的视觉反馈,完全分不清自己到底选了哪本。

反馈给作者之前,我先自己扒了一下源码(毕竟是开源项目)。在 `BookshelfScreen.kt` 里找到了书架列表的拖拽实现,它用了 `sh.calvin.reorderable` 这个 Compose 可拖拽列表库。

## 定位:一个被忽略的参数

`reorderable` 库的 `ReorderableItem` 其实长这样(简化):

```kotlin
ReorderableItem(state, key) { isDragging ->   // 库会把拖拽状态告诉你
    // 你的内容
}
```

注意那个 `isDragging` 参数——库已经把「当前这一项是不是正在被拖」告诉你了。但项目原来的代码,lambda 里压根没接收这个参数,自然也就没有任何「拖动中」的视觉变化。

根因一句话:**库提供了状态,代码没用**。于是拖动时既没有半透明效果,手感和「能不能拖」也变得很可疑(手势和点击混在一起,体验糊成一团)。

对应的是项目 issue [#1565](https://github.com/HapeLee/legado-with-MD3/issues/1565)。

## 修复:四行代码的事

改法其实非常朴素——把 `isDragging` 接住,拖动时给这一项加个半透明:

```kotlin
// 修复后
ReorderableItem(...) { isDragging ->
    BookItem(
        modifier = Modifier
            .longPressDraggableHandle(...)
            .alpha(if (isDragging) 0.5f else 1f),   // 拖动时半透明
        ...
    )
}
```

加上 `androidx.compose.ui.draw.alpha` 的 import,改动也就四行左右。本地构建一跑,拖动反馈清晰、选中态也正常了,问题解决。

## 提交与合并

流程很标准:Fork → 新建分支 `fix/bookshelf-drag-feedback` → 提交 → 推送 → 在 GitHub 上开 PR([#1568](https://github.com/HapeLee/legado-with-MD3/pull/1568))。PR 描述我按 **Problem → Root Cause → Changes → Verification** 的结构写,并附了 Before/After 代码对比和 AI 使用披露。

**2026-06-26,这个 PR 被 HapeLee 合并进了 `main`。** 看到邮件提醒的那一刻,我才反应过来:我成了这个项目的 contributor。

## 一个小插曲:被 bot 提了 review

合并前还有个小插曲。`gemini-code-assist` 这个 bot 在我的 PR 下留了条 review:建议把 `Modifier.alpha(...)` 换成 `Modifier.graphicsLayer { alpha = ... }`,理由是这样可以把透明度变化推迟到 **draw 阶段**,避免在拖动过程中触发不必要的 Compose 重组,拖动更顺滑。

这是个合理的优化——`alpha` 修饰符内部也会走到 graphicsLayer,但显式写出来语义更清晰、性能更可控。我按建议改了一版(提交 `654f309`),然后再交由作者合并。这件事让我真切感受到:开源协作里,「代码被看见、被讨论、被改进」是常态,不用怕被挑毛病。

```kotlin
// 最终版本:graphicsLayer 把绘制推迟到 draw 阶段
ReorderableItem(...) { isDragging ->
    BookItem(
        modifier = Modifier
            .longPressDraggableHandle(...)
            .graphicsLayer { alpha = if (isDragging) 0.5f else 1f },
        ...
    )
}
```

## 几点心得

- **从用户视角切入最自然。** 这不是「为了贡献而贡献」,我是真的被 bug 烦到了。这种动机下写出的 PR,描述天然就贴合真实场景,维护者也更容易共情。
- **根因往往很小。** 一个没接住的 lambda 参数,几行代码的修复。难点不在写代码,而在「定位到是这里」。AI 在读源码、解释库 API 时帮了我不少,但拍板的还得是人。
- **别怕 review。** 不管是来自人还是 bot,review 都是项目变好的方式。被提建议不丢人,认真回应就好。
- **PR 描述是给维护者看的。** Problem → Root Cause → Changes → Verification 的结构,加上 Before/After,维护者扫一眼就能判断要不要合,合并概率高很多。

## 写在最后

从「被 bug 烦到的用户」到「项目 contributor」,中间其实只隔着:愿意打开源码看一眼。希望这篇能让你也想试一试——下个被合并的 PR,可能就是你的。

> 📎 完整的技术复盘(含另外两个待合并 PR)见 [AI 辅助开源的实战笔记:3 个 PR 的踩坑与修复]({% post_url 2026-06-26-ai-open-source-pr-notes %})。
