# 让 AI 在复杂代码库中工作

它似乎已被广泛接受，AI 编码工具在真实的生产代码库中表现挣扎。[斯坦福关于 AI 对开发者生产力的影响的研究](https://www.youtube.com/watch?v=tbDDYKRFjhk)发现：

1. AI 工具生成的很多“额外代码”最终只是重新处理上周生成的垃圾代码。
2. 编码代理在新技术项目或小改动上很棒，但在大型成熟代码库中，它们往往会让开发者*更低效*。

常见的回应介于悲观主义者“这永远不会成功”和更谨慎的“也许有一天，当模型更智能时”之间。

经过几个月的不停尝试，我发现**如果你拥抱核心上下文工程原则，就能用今天的模型取得很大进步**。

这不是另一个“10 倍你的生产力”的推销。我[倾向于在与 AI 炒作机器互动时保持谨慎](https://hlyr.dev/12fa)。但我们偶然发现了让我对可能成就充满乐观的工作流程。我们让 Claude Code 处理 300k 行 Rust 代码库，在一天内完成一周的工作量，并保持通过专家审查的代码质量。我们使用一种我称之为“频繁有意压缩”的技术家族——故意构建如何在整个开发过程中向 AI 提供上下文的结构。

我现在完全相信，AI 用于编码不仅仅是玩具和原型，而是深度技术工程工艺。

### 来自 AI 工程师的上下文基础

AI Engineer 2025 的两场演讲从根本上塑造了我的思考。

第一场是 [Sean Grove 关于“规范是新代码”的演讲](https://www.youtube.com/watch?v=8rABwKRsec4)，第二场是 [斯坦福关于 AI 对开发者生产力的影响的研究](https://www.youtube.com/watch?v=tbDDYKRFjhk)。

Sean 认为我们都在*错误地进行 vibe 编码*。与 AI 代理聊天两个小时，指定你想要什么，然后丢弃所有提示，只提交最终代码……就像 Java 开发者编译 JAR 并提交编译后的二进制文件，同时丢弃源代码。

Sean 提出，在 AI 时代，规范将成为真正的代码。两年后，你在 IDE 中打开 Python 文件的频率将与今天打开十六进制编辑器读取汇编代码的频率相似（对我们大多数人来说，从来没有）。

[Yegor 关于开发者生产力的演讲](https://www.youtube.com/watch?v=tbDDYKRFjhk) 处理了一个正交问题。他们分析了 100k 名开发者的提交，发现其中包括：

1. AI 工具往往导致大量重工，削弱了感知到的生产力提升

<img width="2008" height="1088" alt="image" src="https://github.com/user-attachments/assets/f7cec497-3ee2-47d1-8f91-a18210625e19" />

2. AI 工具在新绿地项目中工作良好，但在棕地代码库和复杂任务中往往适得其反

<img width="1326" height="751" alt="Screenshot 2025-08-29 at 10 55 32 AM" src="https://github.com/user-attachments/assets/06f03232-f9d9-4a92-a182-37056bf877a4" />

这与我与创始人交谈时听到的相符：

* “太多垃圾。”
* “技术债务工厂。”
* “在大仓库中不起作用。”
* “在复杂系统中不起作用。”

关于 AI 编码用于困难东西的一般氛围倾向于

> 也许有一天，当模型更智能时……

甚至 [Amjad](https://x.com/amasad) 在 [9 个月前的 Lenny 的播客](https://www.lennysnewsletter.com/p/behind-the-product-replit-amjad-masad) 中谈到 PM 如何使用 Replit agent 原型化新东西，然后交给工程师实现生产。
(免责声明：我最近（好吧，从来没有）与他更新过，这个立场可能已经改变)

每当我听到“也许有一天当模型智能时”我通常会跳起来宣称**这就是上下文工程的全部**：从*今天的*模型中获得最大价值。

### 今天实际可能做什么

我会在下面深入探讨一点，但为了证明这不仅仅是理论，让我概述一个具体例子。几周前，我决定用我们的技术测试 [BAML](https://github.com/BoundaryML/baml)，这是一个 300k 行 Rust 代码库，用于与 LLM 合作的编程语言。我充其量是一个业余 Rust 开发者，从未接触过 BAML 代码库。

在一小时左右内，我有一个 [修复 bug 的 PR](https://github.com/BoundaryML/baml/pull/2259#issuecomment-3155883849)，维护者第二天早上批准了。几周后，[@hellovai](https://x.com/hellovai) 和我合作向 BAML 提交 35k 行代码，添加 [取消支持](https://github.com/BoundaryML/baml/pull/2357) 和 [WASM 编译](https://github.com/BoundaryML/baml/pull/2330)——团队估计每个功能需要高级工程师 3-5 天。我们在约 7 小时内准备好两个草稿 PR。

再次，这都围绕我们称之为 [频繁有意压缩](#what-works-even-better-frequent-intentional-compaction) 的工作流程构建——本质上设计整个开发过程围绕上下文管理，保持利用率在 40-60% 范围内，并在正确点构建高杠杆的人类审查。我们使用“研究、规划、实施”工作流程，但这里的核能力/学习远超过任何特定工作流程或提示集。

### 我们奇怪的旅程到达这里

我正在与我遇到的最高效的 AI 编码者之一合作。
每隔几天他们就会提交 **2000 行 Go PR**。
这不是 nextjs 应用或 CRUD API。这是复杂的、[易于竞态的系统代码](https://github.com/humanlayer/humanlayer/blob/main/hld/daemon/daemon_subscription_integration_test.go#L45)，它通过 unix 套接字进行 JSON RPC，并管理从分叉 unix 进程的流式 stdio（主要是 Claude Code SDK 进程，稍后详述 🙂）。

仔细阅读每几天 2000 行复杂 Go 代码的想法简直不可持续。我开始感觉有点像 Mitchell Hashimoto，当他为 ghostty 添加 [AI 贡献必须披露](https://github.com/ghostty-org/ghostty/pull/8289) 规则时。

我们的方法是采用类似 Sean 的 **规范驱动开发**。

起初很不舒服。
我必须学会不阅读每行 PR 代码。
我仍然相当仔细地阅读测试，但规范成为我们构建什么以及为什么的真相来源。

转变花了大约 8 周。
这对所有人来说都非常不舒服，尤其是对我来说。
但现在我们飞起来了。几周前，我一天提交了 6 个 PR。
过去三个月，我手动编辑非 Markdown 文件的次数可以用一只手数清。

## 编码代理的高级上下文工程

我们需要的是：

* 在棕地代码库中工作良好的 AI
* 解决复杂问题的 AI
* 没有垃圾
* 在团队中保持心智一致

（是的，当然，让我们尽量多花 token。）

我将深入探讨：

1. 我们应用上下文工程到编码代理中学到的东西
2. 使用这些代理是一个深度技术工艺的维度
3. 为什么我不相信这些方法是可泛化的
4. 我被反复证明关于 (3) 错误的次数

### 但首先：管理代理上下文的朴素方式

我们大多数人从将编码代理像聊天机器人一样开始使用。你来回交谈（或 [醉醺醺地大喊](https://ghuntley.com/six-month-recap/#:~:text=Last%20week%2C%20over%20Zoom%20margaritas%2C%20a%20friend%20and%20I%20reminisced%20about%20COBOL.))，通过问题 vibe 你的方式，直到你用尽上下文、放弃或代理开始道歉。

<img width="7718" height="4223" alt="image" src="https://github.com/user-attachments/assets/7361a203-9d95-42e2-ac16-1f38b04adb58" />

一个稍聪明的方式是当你偏离轨道时重新开始，丢弃会话并启动一个新的，或许在提示中多一点指导。
> [原始提示]，但确保使用 XYZ 方法，因为 ABC 方法不起作用

<img width="7727" height="4077" alt="image" src="https://github.com/user-attachments/assets/1bbbc8ad-60da-4f8b-98c3-e6603b04a0ce" />

### 稍聪明：有意压缩

你可能做过我称之为“有意压缩”的东西。无论是否在轨道上，当你的上下文开始填满时，你可能想暂停工作，用一个新上下文窗口重新开始。为此，你可能使用像这样的提示

> “将我们到目前为止所做的所有内容写入 progress.md，确保注明最终目标、我们采取的方法、我们到目前为止完成的步骤，以及我们正在处理当前的失败”

<img width="7309" height="4083" alt="image" src="https://github.com/user-attachments/assets/64b940e5-89b1-4f6c-a79c-ec2810d9af77" />

你也可以 [使用提交消息进行有意压缩](https://x.com/dexhorthy/status/1961490837017088051)。

### 我们到底在压缩什么？

什么消耗上下文？

* 搜索文件
* 理解代码流
* 应用编辑
* 测试/构建日志
* 来自工具的巨大 JSON 块

所有这些都会淹没上下文窗口。**压缩** 只是将它们提炼成结构化工件。

有意压缩的良好输出可能包括像这样的东西

<img width="1309" height="747" alt="Screenshot 2025-08-29 at 11 10 36 AM" src="https://github.com/user-attachments/assets/a7d5946d-4e81-46e8-b314-d02dae1f00ee" />

### 为什么痴迷于上下文？

正如我们在 [12-factor agents](https://hlyr.dev/12fa) 中深入探讨的，LLM 是无状态函数。影响输出质量的唯一东西（不训练/调优模型本身）是输入的质量。
这对 [使用](https://www.youtube.com/watch?v=F_RyElT_gJk) 编码代理与一般代理设计同样正确，你只是有一个更小的空间，而不是构建代理，我们在谈论使用代理。
在任何给定点，像 Claude Code 这样的代理的回合是一个无状态函数调用。上下文窗口输入，下一步输出。

<img width="7309" height="4083" alt="image" src="https://github.com/user-attachments/assets/c1e920e8-5dc5-4dd2-b76d-853b85a92e6a" />

也就是说，你的上下文窗口内容是你影响输出质量的唯 一杠杆。所以是的，值得痴迷。

你应该优化你的上下文窗口以实现：

1. 正确性
2. 完整性
3. 大小
4. 轨迹

换句话说，你的上下文窗口最坏的情况，按顺序是：

1. 不正确的信息
2. 缺失的信息
3. 太多噪音

如果你喜欢方程，这里有一个愚蠢的你可以参考：

<img width="1320" height="235" alt="Screenshot 2025-08-29 at 11 11 30 AM" src="https://github.com/user-attachments/assets/a6ea98a6-665b-48af-983b-a1cb2c45e44c" />

正如 [Geoff Huntley](https://x.com/GeoffreyHuntley) 所说，

> 游戏的名称是你只有大约 **170k 的上下文窗口** 来使用。
> 所以使用尽可能少的它至关重要。
> 你使用上下文窗口越多，你得到的结果越差。

Geoff 对这个工程约束的解决方案是一种他称之为 [Ralph Wiggum 作为软件工程师](https://ghuntley.com/ralph/) 的技术，基本上涉及在 while 循环中永远运行一个代理，并使用一个简单的提示。

```
while :; do
  cat PROMPT.md | npx --yes @sourcegraph/amp
done
```

如果你想了解更多关于 ralph 或 PROMPT.md 中的内容，你可以查看 Geoff 的帖子或深入我们上周末在 YC Agents Hackathon 上构建的项目，由 [@simonfarshid](https://x.com/simonfarshid)、[@lantos1618](https://x.com/lantos1618)、[@AVGVSTVS96](https://x.com/AVGVSTVS96) 和我，成功（大部分）[一夜之间将 BrowserUse 移植到 TypeScript](https://github.com/repomirrorhq/repomirror/blob/main/repomirror.md)

Geoff 将 ralph 描述为解决上下文窗口问题的“hilariously dumb”解决方案。[我不完全确定它是 dumb 的](https://ghuntley.com/content/images/size/w2400/2025/07/The-ralph-Process.png)。

### 回到压缩：使用子代理

子代理是管理上下文的另一种方式，通用子代理（即不是 [自定义](https://docs.anthropic.com/en/docs/claude-code/sub-agents) 的）一直是 Claude Code 和许多编码 CLI 的功能，从早期开始。

子代理不是关于 [玩过家家和拟人化角色](https://x.com/dexhorthy/status/1950288431122436597)。子代理是关于上下文控制。

子代理的最常见/直接用例是让你使用一个新上下文窗口进行查找/搜索/总结，从而使父代理能够直接工作，而不将 `Glob` / `Grep` / `Read` 等调用混淆其上下文窗口。

https://github.com/user-attachments/assets/cb4e7864-9556-4eaa-99ca-a105927f484d

<details><summary>(视频在手机上不播放？展开静态图像版本)</summary>
  <img width="7309" height="4083" alt="image" src="https://github.com/user-attachments/assets/c72e7dba-1476-4ee9-9cb0-0f97d428b82a" />
</details>

理想的子代理响应可能类似于上面的理想临时压缩

<img width="1309" height="747" alt="Screenshot 2025-08-29 at 11 10 36 AM" src="https://github.com/user-attachments/assets/a7d5946d-4e81-46e8-b314-d02dae1f00ee" />

让子代理返回这个并不简单：

<img width="7309" height="4083" alt="image" src="https://github.com/user-attachments/assets/2bcd30f6-84fd-4911-ac15-63f75619e76d" />

### 什么工作更好：频繁有意压缩

我想谈论的并且我们在过去几个月采用的技术落入我称之为“频繁有意压缩”的范畴。

本质上，这意味着设计你的**整个工作流程**围绕上下文管理，并保持利用率在 40%-60% 范围内（取决于问题复杂性）。

我们这样做是将它分成三个（大约）步骤。

我说“大约”是因为有时我们跳过研究直接规划，有时我们会在实施前做多次压缩研究。

我将在下面的具体例子中分享每个步骤的示例输出。对于给定的功能或 bug，我们倾向于这样做：

**研究**

理解代码库、与问题相关的文件，以及信息流，或许潜在问题原因。

这是我们的 [研究提示](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md)。
它目前使用自定义子代理，但在我使用的其他仓库中，我使用更通用的版本，使用 Claude Code Task() 工具与 `general-agent`。
通用版本几乎同样好。

**规划**

概述我们将采取的确切步骤来修复问题，以及我们需要编辑的文件和如何编辑，对每个阶段的测试/验证步骤超级精确。

这是我们 [用于规划的提示](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/create_plan.md)。

**实施**

逐步通过规划，按阶段进行。对于复杂工作，我经常在每个实施阶段验证后将当前状态压缩回原始规划文件。

这是我们 [使用的实施提示](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/implement_plan.md)。

旁注 - 如果你听了很多关于 git worktree 的，这就是唯一需要在 worktree 中做的步骤。我们倾向于在 main 上做其他一切。

**我们如何管理/共享 Markdown 文件**

我将跳过这个部分以简洁，但欢迎在 [humanlayer/humanlayer](https://github.com/humanlayer/humanlayer) 中启动 Claude 会话并询问“thoughts tool”如何工作。

### 将此付诸实践

我和 [@vaibhav](https://www.linkedin.com/in/vaigup/) 每周做一个 [实时编码会话](https://github.com/ai-that-works/ai-that-works)，我们白板并编码一个高级 AI 工程问题的解决方案。这是我的周亮点之一。

几周前，我 [决定分享更多关于过程](https://hlyr.dev/he-gh)，好奇我们的内部技术是否能一击修复 BAML 的 300k 行 Rust 代码库，BAML 是一个用于与 LLM 合作的编程语言。我从 @BoundaryML 仓库中挑选了一个 [（公认的小）bug](https://github.com/BoundaryML/baml/issues/1252) 并开始工作。

你可以 [观看该集](https://hlyr.dev/he-yt) 以了解更多关于过程，但概述它：

**值得注意**：我充其量是一个业余 Rust 开发者，我从未在 BAML 代码库中工作过。

#### 研究

- 我创建了一份研究，我阅读了它。Claude 决定 bug 无效，代码库正确。
- 我扔掉那份研究并启动了一个新的，带更多指导。
- 这里是 [我最终使用的最终研究文档](https://github.com/ai-that-works/ai-that-works/blob/main/2025-08-05-advanced-context-engineering-for-coding-agents/thoughts/shared/research/2025-08-05_05-15-59_baml_test_assertions.md)

#### 规划

- 在研究运行时，我不耐烦并启动了一个没有研究的规划，看 Claude 是否能直接去实施规划 - [你可以在这里看到它](https://github.com/ai-that-works/ai-that-works/blob/main/2025-08-05-advanced-context-engineering-for-coding-agents/thoughts/shared/plans/fix-assert-syntax-validation-no-research.md)
- 研究完成后，我启动了另一个使用研究结果的实施规划 - [你可以在这里看到它](https://github.com/ai-that-works/ai-that-works/blob/main/2025-08-05-advanced-context-engineering-for-coding-agents/thoughts/shared/plans/baml-test-assertion-validation-with-research.md)

规划都相当短，但它们显著不同。它们以不同方式修复问题，并有不同的测试方法。不深入细节，它们都“会工作”，但带研究的那个在*最佳*位置修复问题，并规定了符合代码库惯例的测试。

#### 实施

- 这发生在播客录制的前一晚。我并行运行两个规划，并在晚上关机前提交两个作为 PR。

到第二天上午 10 点 PT 我们上节目时，[带研究的 PR 已经被 @aaron 批准](https://github.com/BoundaryML/baml/pull/2259#issuecomment-3155883849)，他甚至不知道我在为播客做一点 🙂。我们 [关闭了另一个](https://github.com/BoundaryML/baml/pull/2258/files)。

所以在我们原始的 4 个目标中，我们达到了：

- ✅ 在棕地代码库中工作（300k 行 Rust 项目）
- 解决复杂问题
- ✅ 没有垃圾（PR 已合并）
- 保持心智一致

### 解决复杂问题

Vaibhav 仍然怀疑，我想看看我们是否能解决更复杂的问题。

所以几周后，我们两人花了 7 小时（3 小时研究/规划，4 小时实施）提交 35k 行代码，向 BAML 添加取消和 WASM 支持。
[取消 PR 上周刚刚合并](https://github.com/BoundaryML/baml/pull/2357)。[WASM 一个仍然开放](https://github.com/BoundaryML/baml/pull/2330)，但有一个在浏览器中从 JS 应用调用 WASM 编译 Rust 运行时的演示。

虽然取消 PR 需要更多爱来越过线，但我们只用一天就取得了惊人进步。Vaibhav 估计 BAML 团队的高级工程师完成每个 PR 需要 3-5 天。

✅ 所以我们也能解决复杂问题。

### 这不是魔法

记住例子中我阅读研究并扔掉因为它错的部分？或我和 Vaibhav 坐着 DEEPLY ENGAGED 7 小时？做这个时你必须参与你的任务，否则它 WON'T WORK。

有一种人总是寻找解决所有问题的魔法提示。它不存在。

通过研究/规划/实施的频繁有意压缩会让你的表现**更好**，但让它**足够好用于困难问题**的是你将高杠杆人类审查构建到你的管道中。

<img width="7309" height="4083" alt="image" src="https://github.com/user-attachments/assets/01c7818a-9a0d-4ede-a23b-fb0c2e80f843" />

### 脸上有蛋

几周前，[@blakesmith](https://www.linkedin.com/in/bhsmith/) 和我坐下来 7 小时并 [试图从 parquet java 移除 hadoop 依赖](https://github.com/dexhorthy/parquet-java/blob/remove-hadoop/thoughts/shared/plans/remove-hadoop-dependencies.md) - 关于一切出错的深入探讨和我的理论为什么，我将留给另一篇文章，足以说它没有顺利进行。tl;dr 是研究步骤没有深入依赖树，并假设类可以上游移动而不引入深度嵌套的 hadoop 依赖。

有一些大而难的问题你不能只在 7 小时内提示你的方式通过，我们仍然好奇而兴奋地与朋友和伙伴黑客推动边界。我认为另一个学习是你可能需要至少一个代码库专家，对于这个案例，那不是我们中的任何一个。

### 关于人类杠杆

如果从所有这些中你带走一件事，让它是这样：

一行坏代码是……一行坏代码。
但一行坏的 **规划** 可能导致数百行坏代码。
一行坏的 **研究**，对代码库如何工作或某些功能位置的误解，可能让你得到数千行坏代码。

<img width="7309" height="4083" alt="image" src="https://github.com/user-attachments/assets/dab49f61-caae-4c15-b481-ee9b8f64995f" />

所以你想 **聚焦人类努力和注意力** 于管道的 HIGHEST LEVERAGE 部分。

<img width="9830" height="4520" alt="image" src="https://github.com/user-attachments/assets/cf981f70-5e61-4938-aa9a-7dcb88c9f8a4" />

当你审查研究和规划时，你比审查代码获得更多杠杆。（顺便说一句，我们在 [humanlayer](https://hlyr.dev/code) 的主要焦点之一是帮助团队构建和利用高质量工作流程提示，并为 AI 生成的代码和规范 crafting 伟大的协作工作流程）。

### 代码审查是为了什么？

人们对代码审查的目的有许多不同意见。

我更喜欢 [Blake Smith 在 Code Review Essentials for Software Teams 中的框架](https://blakesmith.me/2015/02/09/code-review-essentials-for-software-teams.html)，其中他说代码审查最重要的部分是心智一致 - 保持团队成员了解代码如何改变以及为什么。

<img width="7309" height="4083" alt="image" src="https://github.com/user-attachments/assets/77f4001b-175f-4da6-a6d4-e00b80489476" />

记住那些 2k 行 GoLang PR？我在意它们正确和设计良好，但团队内部不安和挫败的最大来源是缺乏心智一致。**我开始失去对我们的产品是什么以及它如何工作的触感。**

我期望任何与非常高效的 AI 编码者合作过的人都有这个经历。

这实际上是我们研究/规划/实施的最重要部分。
每个人提交更多代码的保证副作用是，在任何给定时间，任何给定工程师不熟悉的代码库比例将更大。

我甚至不会试图说服你研究/规划/实施是对大多数团队的正确方法 - 它可能不是。但你 ABSOLUTELY 需要一个工程过程

1. 保持团队成员在同一页上
2. 使团队成员能够快速了解代码库的不熟悉部分

对于大多数团队，这是拉取请求和内部文档。对于我们，现在是规范、规划和研究。

我不能每天阅读 2000 行 GoLang。但我 *可以* 阅读 200 行写得好的实施规划。

当某事损坏时，我不能花一小时+ 在 40+ 个守护进程文件上探险（好吧，我可以，但我不想）。我 *可以* 指导研究提示给我速度运行在哪里看以及为什么。

### 回顾

基本上我们得到了我们需要的一切。

- ✅ 在棕地代码库中工作
- ✅ 解决复杂问题
- ✅ 没有垃圾
- ✅ 保持心智一致

（哦，是的，我们的三人团队平均每月在 Opus 上花费约 12k 美元）

所以你不要认为我只是另一个 [炒作的留胡子销售家伙](https://www.youtube.com/watch?v=IS_y40zY-hc&lc=UgzFldRM6LU5unLuFn54AaABAg.AMKlTmJAT5ZAMKrOOAMw3I)，我会注意到这并不完美适用于每个问题（我们会回来另一轮声音，parquet-java）。

8 月整个团队花了两周时间在棘手的竞态条件下旋转圈子，螺旋成 GoLang 中的 MCP sHTTP keepalives 问题和其他一堆死胡同。

但现在那是例外。一般来说，这对我们工作良好。我们的实习生第一天提交了 2 个 PR，第 8 天 10 个。我真心怀疑它是否会为其他人工作，但我 和 Vaibhav 在 7 小时内提交了 35k 行工作的 BAML 代码。（如果你还没见过 Vaibhav，他是我认识的最细致的工程师之一，当涉及代码设计和质量时。）

### 即将到来

我相当自信编码代理将被商品化。

难点将是团队和工作流程转变。在 AI 编写我们 99% 代码的世界中，一切关于协作都会改变。

我相当强烈地相信如果你不弄清楚这个，你会被弄清楚的人超越。

### 好吧，显然你有东西要卖给我

我们对规范优先、代理式工作流程相当看好，所以我们正在构建工具来使它更容易。其中许多事情，我痴迷于扩展这些“频繁有意压缩”工作流程协作地跨大型团队的问题。

今天，我们在私有 beta 中推出 CodeLayer，我们新的“后 IDE IDE” - 想想“Superhuman for claude code”。如果你是 Superhuman 和/或 vim 模式的粉丝，并且准备超越“vibe 编码”并认真构建代理，我们很乐意让你加入等待列表。

**在 [https://humanlayer.dev](https://humanlayer.dev) 注册**。

## 对于 OSS 维护者 - 让我们一起发布一些东西

如果你是湾区基于复杂 OSS 项目的维护者，我的公开提议 - 我将与你在 SF 的周六亲自配对 7 小时，看看我们是否能发布一些大东西。

我获得很多关于限制和这些技术哪里不足的学习（以及，任何运气，一个工作的合并 PR，我可以指向它添加大量价值）。你学习工作流程的唯一方式是我发现有效的方式 - 直接 1x1 配对。

## 对于工程领导者

如果你或你认识的工程领导者想用 AI 10 倍他们的团队生产力，我们正在向前部署与 ~10-25 人工程组织，帮助团队进行文化/过程/技术转变，以过渡到 AI 优先编码世界。

### 感谢

- 感谢所有听过这个帖子早期啰嗦版本的朋友和创始人 - Adam、Josh、Andrew 和更多更多
- 感谢 Sundeep 经受这个疯狂的风暴
- 感谢 Allison、Geoff 和 Gerred 将我们拖入未来