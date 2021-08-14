---
title: 重新定义PPT的Slidev
date: 2021-08-13 22:44:15
updated: 2021-08-13 22:44:15
alias: slidev-tutorial
tags: [工具]
categories: [教程]
keywords: [slidev, slidev教程, ppt, powerpoint]
---
作为一个每次写 VBA 都要查文档的 MS Office 小白，PowerPoint 这种被人成为神器的演示工具对我来说却是阻碍我生产力的绊脚石，我总是无法沉下心做好元素的对齐，也被页码问题搞得焦头烂额。终于有一天，我意识到花费在样式和结构组织上的大量精力是浪费生命的，我应该更专注于 PPT 的核心——内容。如果能有一款基于 Markdown 的工具，让我逃脱格式调整的苦海，潜心编写内容该多好啊！而当我看到 Slidev ，问题终于有了转机。<!--more-->

## 什么是 Slidev
> When working with WYSIWYG editors, it is easy to get distracted by the styling options. Slidev remedies that by separating the content and visuals. This allows you to focus on one thing at a time, while also being able to reuse the themes from the community. Slidev does not seek to replace other slide deck builders entirely. Rather, it focuses on catering to the developer community.

Slidev 的文档中如是介绍自己，虽然功能和理念强大，但它却谦卑地把自己限定在为开发者提供帮助。

简单来说，Slidev 就是一款数据与样式分离的演示文档编辑工具。文档的内容组织是以 Markdown 标记语言为基础的，使用者可以结合 CSS 甚至 Vue 对文档的样式和交互进行自定义，项目基于 Node.js 实现。

Slidev 并不是这种思路的唯一实践者，实际上它还处于项目的初期阶段，功能不完整的同时也可能存在些稳定性问题，例如 Marp 等先驱者则是更加成熟的先驱实现。但 Slidev 的优势在于，其对 Vue 的良好支持使得他的扩展和复用能力几乎具有无限的可能，很适合专业开发者在 PPT 领域实现『弯道超车』。

## Slidev 适合做什么
1. **文本文档与演示文稿间的快速转换**：以技术分享场景为例，技术文档的内容往往已经足够满足分享用途，但将文档中的文字逐行粘贴到 PPT 中再进行格式微调实在是枯燥且浪费生命。而如果使用 Slidev ，我们只需要在原文档中加入分页符，再按修改文本文档的习惯对文章的组织结构进行微调即可快速创造出一份精美的 PPT ，效率不可谓不高。

2. **深度定制化需求**：尽管 PowerPoint 和 Keynote 的功能已十分强大，但其功能层面的天花板仍然被限定在了软件自身所提供的能力上。而在 Slidev 下，我们可以按开发前端页面的模式对 PPT 进行深度定制，甚至可以通过编写插件、修改 Slidev 源码的方式实现我们想要的任何功能。换而言之，只要你的前端动手能力足够强，那么你对你的 PPT 就有着 100% 的掌控力，这很适合有深度定制化需求的用户。

3. **高度复用**：借助 Slidev ，我们完全可以建立一套自己的 PPT 脚手架项目，这样一来每次制作 PPT 时我们只做好文本内容的编写即可，从而真正做到聚焦内容本身。而这种脚手架的可复用程度是 PowerPoint 下那简单的模板机制所无法比拟的。

## Slidev 不适合做什么
如果你对 PPT 的动画、图表、样式细节等有着高标准的要求，那么 Slidev 可能不太适合你的使用场景。倒也不是功能上不支持，主要是想要实现与 PowerPoint 完全一致的效果需要进行一定深度的开发，这在效率上大概率是得不偿失的。

另外如果你需要进行更复杂的公式编写，那 Latex 生态下的 Beamer 是个更好的选择，毕竟 Slidev 是以 Markdown 为核心的，对 Latex 的支持只能说是聊胜于无。

除此之外，Slidev 还有以下功能上的局限性：
1. Slidev 还在持续开发中，相当于 Beta 版本，所以实际使用中可能会遇到些 bug 。
2. 不支持直接导出为常用的 MS Office 的 `.ppt` 格式或 Keynote 的 `.key` 格式，难以满足一些正式场合的要求。
3. 项目提供了许多漂亮的模板，但实际上很多公司、会议和学校会要求使用固定的内部 MS Office 或 Keynote 模板，整体适配成本较高。
4. 由于 Markdown 语法本身的局限性，表格、图表类内容的制作非常繁琐，效率远低于 PowerPoint 和 Keynote。

## 如何安装 Slidev
使用 Node.js 的包管理工具 npm 或 yarn 在目标目录执行以下命令即可完成安装：
```shell
npm init slidev@latest

yarn create slidev
```

在 v0.14 之后，Slidev 提供了更便捷的安装方式，全局安装后便可在任意目录执行 `slidev` 命令快速初始化项目，同时这也省去了此前安装方式带来的重复且巨大的 node_modules 目录：
```shell
npm i -g @slidev/cli
slidev
```

## Slidev 使用入门
### 编写你的第一个 Slidev PPT
我们可以像平时编写文章一样，直接用 Markdown 的语法进行 PPT 的编写，唯一的区别在于需要使用 `---` 进行分页：

```markdown
# 标题1

## 副标题1

正文**第一页**

<!-- 这是一段提示文字 -->

---

# 标题2

## 副标题2

正文**第二页**

---

# 标题3

## 副标题3

正文**第三页**

```

随后执行 `slidev` 命令即可在本地查看渲染好的 PPT ：
- 预览模式：http://localhost:3030/
- 演示者模式：http://localhost:3030/presenter

以演讲时常用的演示者模式为例，Markdown 编写的内容如期渲染，同时界面非常简洁，常用的功能一应俱全：

![Slidev demo screenshot](https://gmiimg.com/1e5b493c9f8455e96d0a76d1b91ca295.png)

### 导出 PPT
当我们希望将 PPT 导出为 PDF 时，首先要安装如下依赖：

```shell
npm i -D playwright-chromium
```

随后只需一行命令便可完成 PDF 导出：
```shell
slidev export
```

除了 PDF ，目前 Slidev 还支持以下形式的导出：
- 导出为 PNG 图片：`slidev export --format png`
- 导出为支持点击的 PDF 文件：`slidev export --with-clicks`
- 构建项目用于 SPA 部署：正如所有现代化前端工具一样，你可以使用 `slidev build` 命令将 PPT 打包构建，并按前端部署的常见方式将 `dist/` 目录中的内容部署在服务器上以供其他用户访问

## 使用 Windi CSS 美化 PPT
相信前端同学一定听说过大名鼎鼎的 Tailwind CSS 项目，该项目通过设置大量的预设 class 大大减轻了前端工程师进行样式微调的工作量的同时也很大程度上降低了不同项目间的代码重复率并为标准化提供了思路。而 Slidev 所依赖的 Windi CSS 也是同样的理念，项目作者在 README 中如是写道：
> If you are already familiar with Tailwind CSS, think about Windi CSS as an on-demanded alternative to Tailwind, which provides faster load times, fully compatible with Tailwind v2.0 and with a bunch of additional cool features.

而在 Slidev 中，我们可以按照常规的前端页面代码编写方式内嵌 HTML 元素并用 Windi CSS 的预设 class 来自定义样式。例如如果我们想为 PPT 新增一个按钮，只需在 Markdown 文件中增加如下内容：
```html
<button class="bg-blue-400 hover:bg-blue-500 text-sm text-white font-mono font-light py-2 px-4 rounded border-2 border-blue-200 dark:bg-blue-500 dark:hover:bg-blue-600">
```

预览时便可看到期望的效果：
![Windi CSS demo](https://gmiimg.com/2975de353c6ac68a76c76983375f62cb.png)

此外，Slidev 也支持通过扩展 `defineWindiSetup` 这个 Vue 组件的方式将 Slidev 的默认样式也完全交给用户来进行自定义修改。

## 建立基于 Vue 的工程化 PPT 项目
Slidev 使用 Vue 3 进行客户端的应用渲染，因此我们可以使用标准的 Vue 项目的开发流程对 PPT 进行扩展甚至抽象，具有极高的可玩性。实践中，我们在正常完成 Vue 插件的编写后，通过扩展 Slidev 的入口函数即可完成插件整合：

```javascript
import { defineAppSetup } from '@slidev/types'

export default defineAppSetup(({ app, router }) => {
  // Vue App
  app.use(YourPlugin)
})
```

除了对 Slidev 进行扩展时可以使用 Vue 来进行，我们在编写 PPT 内容的时候也可以借助 Vue 的 MVVM 模式对 PPT 内部的事件进行监听、对内容进行动态渲染，实际上这也是 Slidev 动画机制的实现方式。

## 未来可期
本文仅介绍了 Slidev 的一些基础功能，实际上这款工具的现有功能已非常强大：
- 代码高亮、Latex 、流程图、动画等基础功能一应俱全。
- Slidev 服务端底层基于 Vite 实现，因此用户可以以 Vite 的开发模式对 Slidev 进行深度自定义扩展。
- Slidev 制作的 PPT 可支持 Monaco Editor 编辑器，也就是说你与 PPT 的交互方式可以从单调的鼠标点击升级为动态修改内容。
- 基于 WebRTC 的录制功能，演讲视频录制再也不用麻烦 OBS 登场了。

前有 Marp 后有 Slidev ，这些开源项目的创造者们为 PPT 制作方式的细分领域提供了强大的基础工具。『重新定义 PPT 』或许是标题党，但相信在合适的场景下，这些工具一定能为你的工作带来切实的便利。