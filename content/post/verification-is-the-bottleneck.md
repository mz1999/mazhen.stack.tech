---
title: "从 Vibe Coding 到软件工厂，软件工程正在经历什么"
date: 2026-07-24T21:55:26+08:00
draft: false
tags: [ai]
categories: [tech]
---

### 从一个人提前命名了未来，到整个行业被迫面对它

2025 年 2 月 3 日，Andrej Karpathy [在 X 上发了一条帖子](https://x.com/karpathy/status/1886192184808149383)，描述他新发现的一种编程方式："我把它叫做 vibe coding。你完全沉浸在感觉中，拥抱指数增长，忘掉代码的存在。"他用 Cursor Composer 加 Sonnet，对着麦克风说话，不读生成的代码，遇到报错就把错误信息贴回去让 AI 修，修不好就接受或者绕过去。

Karpathy 不是在开玩笑，也不是在炫技。他是一个对方向有敏感度的人，提前命名了一种还没到来的工作状态。但当时工具还撑不起这个命名。大多数工程师试了一下 AI 编程，发现在真实项目里一塌糊涂，于是把 vibe coding 归类为"大佬的周末玩具"。不是 Karpathy 说错了，是路还没修好。

接下来的一年里，路在修。2025 年下半年，模型能力发生了代际跃升（Opus 4.5、Codex 5、Gemini 3），更关键的是 agent 从编辑器里的自动补全进化成了终端里的自主执行者：控制 shell、跑测试、搜索文档、调用服务。形态变了，体验就变了。

2026 年 1 月，大神们集体转向。

1 月 7 日，Ruby on Rails 创造者 DHH 发表[《Promoting AI agents》](https://world.hey.com/dhh/promoting-ai-agents-3ee04945)。半年前他还在 [Lex Fridman 播客](https://lexfridman.com/dhh-david-heinemeier-hansson)上说"AI can't code"，说自己不喜欢 AI 在编辑器里抢键盘。现在他说："AI agents really came alive for me... They're fully capable of producing production-grade contributions to real-life code bases." 他给 agent 们"升职"了，从助手升到了团队成员。虽然他加了一句"pure vibe coding remains an aspirational dream for professional work for me, for now"，但态度已经翻转。

1 月 12 日，Linus Torvalds 在 GitHub 上发布了 [AudioNoise](https://github.com/torvalds/AudioNoise) 项目，用 Google 的 Antigravity AI 生成音频可视化功能。提交信息里写了一句："Is this much better than I could do by hand? Sure is." [ZDNET 的标题](https://www.zdnet.com/article/linus-torvalds-vibe-coding-ai/)是："Even Linus Torvalds is vibe coding now."

差不多同一时间，Redis 作者 antirez 发表[《Don't fall into the anti-AI hype》](https://antirez.com/news/158)，说 AI 将"永远彻底改变编程"，而且比他预期的快得多。对大多数项目而言，"亲自写代码已不再是明智选择，除非只为乐趣"。他给出的实证：几小时内完成了原本需要数周的 4 个任务，包括 5 分钟生成 700 行纯 C 的 BERT 推理库。

到了 2026 年 4 月，DHH 上 [Pragmatic Engineer 播客](https://www.youtube.com/watch?v=JiWgKRgdgpI)时已经"barely writes any code by hand"了。从"AI can't code"到"几乎不手写代码"，中间只隔了不到一年。

然后是 7 月的总爆发。[antirez 发帖](https://x.com/antirez/status/2076249566635085826)说逐行审查让人类成了瓶颈；[Uncle Bob Martin 说](https://x.com/unclebobmartin/status/2080257779395154409)"我现在的策略是不读任何 agent 写的代码"；Addy Osmani 在 O'Reilly 发了["Agentic Code Review"](https://www.oreilly.com/radar/agentic-code-review/)长文；KTH 教授 Martin Monperrus 发表[论文](https://arxiv.org/abs/2606.13175)宣称"代码审查已死"。

从 Karpathy 提前命名，到大神们集体转向，再到行业级辩论全面展开，中间 17 个月。观念变化是结果，不是原因。真正的原因是工具在 2025 年下半年跨过了一个阈值：agent 从"帮你补全一行代码"变成了"替你完成一个 ticket"。到这一步，"要不要看它写的代码"就成了每个工程师每天的操作决策，不再是茶余饭后的话题。

而"不看代码"这个选项，也同时从一个极端主张变成了一个真实的工程可能性，以及一个真实的系统性风险。这两面是同时成立的。

![提前命名：路还没修好就立了路牌](https://cdn.mazhen.tech/2026/20260724215741391.png)

---

### 为什么是现在

三件事同时发生了。

**Coding agent 跨过了"替你完成一个 ticket"的阈值。** 2025 年初的 AI 编程工具能做什么？补全函数、写代码片段、在简单 greenfield 项目里跑通 demo。大多数工程师试了一下，发现在真实的十年老代码库里一塌糊涂，结论就是"玩具"。2026 年上半年不一样了。Claude Code 在 7 月 10 日 GA 了 Auto Mode，agent 可以连续执行多步操作不需要逐步审批；GPT-5.6 在 7 月 9 日成为默认模型；Codex 从 2 月的 100 万用户涨到 7 月 14 日的 800 万。这意味着 agent 第一次能在多文件、多步骤、长上下文的真实工程任务中维持连贯性。它从"帮你写个函数"变成了"替你完成一个 ticket"。

**第一批"不看代码"的人已经撞墙了，而且公开复盘了。** Dex Horthy，HumanLayer 创始人，"context engineering"这个词就是他造的。2025 年 7 月他开始了一个激进实验：完全自动化的代码工厂，agent 写代码，人类完全不审查。四个月后关停，推倒重来。生产环境崩了，怎么 prompt 模型都找不到根因，团队花好几天在意大利面条代码里跋涉，最后发现一个主键被错误地路由穿过了整个代码库。修完之后又花三周重新 onboard 一个"从未有人类读过"的代码库。2026 年 3 月他在 Computer History Museum 的 Coding Agents Conference 上[公开讲了这件事](https://www.youtube.com/watch?v=YwZR6tc7qYg)，修正了自己之前的立场："I don't think it's okay to not read the code." 他还观察到一个"5 个月周期"：行业在"不看代码真爽"和"天哪代码库一团糟"之间来回摆。这个实验的意义在于，它把"不看代码会怎样"从假设变成了实证。Uncle Bob 7 月说"我不读代码"的时候，他面对的反例是有血有肉的：你确定不会四个月后撞墙？

**工业界数据把问题从轶事变成了统计事实。** Faros AI 追踪 22,000 名开发者：AI 采用后代码变动率飙升 861%，审查耗时中位数涨 441.5%，缺陷率从 9% 到 54%，零审查合并的 PR 增加 31.3%。GitClear 的数据：AI 用户日产出 4 倍代码量，交付价值仅增约 12%。DORA 2025 报告调查近 5000 人后说了一句很准确的话：AI 是"镜子和乘数"，在纪律严明的组织中放大效率，在碎片化的组织中放大混乱。Google 75% 的新代码由 AI 生成并经工程师批准（Pichai 2026 年 4 月[原话](https://blog.google/innovation-and-ai/infrastructure-and-cloud/google-cloud/cloud-next-2026-sundar-pichai/)）。Anthropic 内部数据：引入 AI 辅助审查前，仅 16% 的 PR 收到过有意义的评审反馈。这不是哪个团队的个别困境了。

---

### 瓶颈从来不是生成

把上面这些信息放在一起看，我觉得最核心的一个判断是：软件工程的瓶颈从来不是"能写多少代码"，是"能多快地证明代码是对的"。AI 没有创造这个瓶颈，它只是把瓶颈暴露到了无法忽视的程度。

Addy Osmani 在 7 月 22 日发表的["Software Factories, Light and Dark"](https://addyo.substack.com/p/software-factories-light-and-dark)里用了一个很准确的比喻：生成是宽口漏斗，验证是窄颈。你把嘴加大，只会让颈部堆得更深。

![宽口漏斗与窄颈：生成快验证慢](https://cdn.mazhen.tech/2026/20260724215830551.png)

"要不要看 AI 写的代码"其实是个错误的问题，因为它把讨论锁死在"人的阅读速度"这个不可扩展的维度上。该问的是：我们怎么扩展验证能力，让它跟上生成速度？

Lucas Costa 在 Hacker News 上那篇["Backpressure is All You Need"](https://viralpique.com/backpressure-is-all-you-need/)把这个思路推到了头。Backpressure，背压，分布式系统里的老概念：下游告诉上游"我处理不过来了"。TCP 有窗口流控，Kafka 有消费者组延迟监控。到了 AI 编程这里，agent 是快速生产者，人类是慢速消费者，大多数团队在两者之间没有任何背压机制。结果就是审查队列无限增长，审查质量退化，团队要么橡皮图章式批准，要么自己成为瓶颈。

背压的解法不是让人读得更快。是在代码到达人类之前，让机器先验证尽可能多的东西。类型系统是第一层，编译器即时抓住整类错误。测试套件是第二层，失败的测试告诉 agent 你刚才做的事打破了某些东西。Linter 和架构强制规则是第三层。人类审查是最后一层，只处理机器判断不了的东西。

[GitLab 公开的 AI-Assisted Development Playbook](https://handbook.gitlab.com/handbook/engineering/workflow/ai-assisted-development/) 里有两条原则我觉得说得特别好。一条是"Fix the environment, not the prompt"：agent 产出坏代码时，别写更好的 prompt，加一条 lint 规则、一个测试、一份文档。环境修复跨会话持久，prompt 不会。另一条是"Constraints are multipliers"：一条 CI 门禁比一千行 prompt 指令抓住更多 bug。把规则编码在 CI 里，别编码在自然语言里。

---

### 软件工厂：Loop、Harness、Factory

Addy Osmani 那篇文章提供了一个比"看不看代码"精确得多的思考框架。三层结构：

Loop（循环）是最小单元。一个 agent 重复做一件事：收集上下文、行动、检查结果、再来，直到某个条件满足。

Harness（缰绳）是循环运行的环境。沙箱、可用工具、跨运行的记忆、定义"完成"意味着什么的门禁。没有缰绳的模型会永远空转。

Factory（工厂）是多个有缰绳的循环同时运行，由工作队列喂入，通过审查门禁排入生产，人类从上方拥有整个系统。它不是一个更大的 agent，是一个由循环组成的组织架构图。

![Loop → Harness → Factory：嵌套的循环与灯开关](https://cdn.mazhen.tech/2026/20260724215941339.png)

在这个框架下，"要不要看代码"变成了一个更精确的问题：人类判断应该放在工厂的哪个位置？

这就引出了"暗工厂"和"亮工厂"。暗工厂（Dark Factory）借自制造业，FANUC 在日本从 2001 年就跑无灯工厂了。在软件里，"暗"就是一个物理事实：代码被写出来、被验证、被发布，但没有人类读过它。验证完全由机器完成。亮工厂是同一条流水线，只是在判断力所在的地方留着灯。Agent 仍然做大部分构建，但人类在代码发布前读产出物，灯在"错误判断代价高昂"的地方亮着。

这里有一个关键区分：不是所有循环都有资格关灯。一个循环能"赚得"全自动化地位，条件是检查廉价、高频、且不容易被伪造。类型门禁、属性测试、绿/红 oracle，这些够硬。但"错误答案代价高昂且只有人类能抓住"的循环，微妙的生产 bug、大爆炸半径、将塑造一年以上工作的架构决策，必须保持灯亮。

Dex Horthy 说得更直白："全暗工厂是最大化 token 利用率的好方法。如果你的信念是'我的工作是从机器中榨取尽可能多的智能'，那你就猛烧 token。但我的主张是 token smarter：怎么在不必关灯的情况下，从 AI 中获取尽可能多的价值，同时保持控制、品味、判断和对系统架构的理解？"

---

### 理解力债务：测试全绿不等于系统被理解

这场讨论里最容易被忽视、但我觉得可能最致命的概念，是 Addy Osmani 提出的 [comprehension debt，理解力债务](https://addyosmani.com/blog/comprehension-debt/)。定义很简单：代码库中存在的代码量，与任何人类真正理解的代码量之间，不断扩大的差距。

它和技术债不一样。技术债会通过构建变慢、依赖纠缠来宣告自己的存在。理解力债务制造的是虚假信心：代码看起来干净，测试是绿的，一切正常。直到某个最糟糕的时刻，清算悄然到来。

Margaret-Anne Storey 描述过一个学生团队：第七周时他们已经无法做简单修改而不打破某些意想不到的东西。问题不是代码乱，是没有人能解释设计决策为什么那样做、系统的不同部分应该如何协作。用她的话说，"系统的理论蒸发了。"

![测试全绿，但没人看懂系统](https://cdn.mazhen.tech/2026/20260724220006369.png)

这个概念解释了为什么 Uncle Bob 的约束体系（测试 + 指标 + 变异测试）能抓住功能错误，但抓不住理解力债务。回想 Dex 的 dark factory：一个主键被错误路由穿过整个代码库，所有单元测试都可能是绿的，因为每个局部行为都是"正确的"。但系统的整体结构已经被腐蚀了，没有任何一个测试在检查"结构是否合理"。四个月不看代码，测试一直绿着，直到生产崩溃。然后发现没有人，包括 AI，能理解这个系统为什么变成了这样。

更深一层的问题是训练激励。Dex 在好几个场合讲过：当前 coding model 的每一个 benchmark 都只奖励一件事，测试通过了吗？在整个训练循环中，模型不会因为"留下了一个六个月后人类无法工作的代码库"而受到惩罚。架构卓越的成本函数以月和年衡量，你没法对复杂设计决策计算出整洁的梯度。所以"模型更强了就不需要人看了"这个期望，至少在训练激励结构改变之前，是个幻觉。

---

### Clean Code 的反转

2026 年 4 月，巴西开发者 Fabio Akita 写了一篇["Clean Code for AI Agents"](https://akitaonrails.com/en/2026/04/20/clean-code-for-ai-agents/)，指出了一个很有意思的反转。Uncle Bob 2008 年写的那些原则，小函数、单一职责、有意义的命名、不要重复，在 agent 时代没有过时。它们从"人的审美偏好"变成了"机器的技术约束"。

具体来说：文件超过 500 行，agent 的注意力就退化（Claude Code 默认每次读 2000 行，但注意力精度在远未达到窗口上限时就开始下降）。命名不唯一，grep 返回 50 个结果，agent 浪费 token 逐个排查。函数超过 20 行，agent 没法在单次工具调用中完整推理。

所以 Uncle Bob 说"我约束函数大小和圈复杂度"的时候，他不只是在做质量门禁。他是在保证代码对下一个 agent 迭代仍然可导航。agent 写出来一坨 2000 行的泥球，不仅人类看不懂，下一个 agent 也看不懂。

最戏剧性的反转是注释。Uncle Bob 2008 年的信条是"好代码自己解释自己，注释是代码坏味道"。但 agent 有完美的语法流利度，不需要你解释 `x++` 是什么意思。它缺的是 provenance，决策溯源：为什么选了这个方案而非显而易见的那个？哪个生产 bug 导致了这段奇怪的逻辑？哪个上游 lib 的已知问题迫使你用了这个 workaround？这些信息只存在于注释、commit message 或人脑里。对 agent 来说，注释是工具调用中最容易获取的上下文来源。所以 Akita 说"不要删 agent 写的注释"，它写注释是因为在生成代码时判断了这些信息值得为未来的编辑保留。

Akita 的结尾我直接引："Clean code was never fashion. It became infrastructure." 那些在 2010 年代被"move fast and break things"冲掉的 XP/TDD/SOLID 纪律，在 agent 时代反而成了技术分水岭。保持了纪律的人能自信地说"我不读代码，因为我的约束体系足够厚"。没有这些纪律的人，不看代码就是在裸奔。

---

### 那到底该怎么做

说了这么多，落到操作层面。我试着把各家的实践综合成一个分层的东西，从底往上：

**把验证编码进环境。** GitLab 那条原则值得贴墙上："One CI gate catches more bugs than a thousand lines of prompt instructions." 类型系统开到最严格（TypeScript strict mode、Rust 编译器、mypy）。测试必须一条命令就能跑，agent 不会做需要 10 步手动设置的验证。架构边界用工具强制执行，ArchUnit、dependency-cruiser。Linter 规则全开，agent 不会抱怨，它会服从。

**判断力前置。** Dex 的数据：200 行设计文档把 PR 返工率从 50% 压到 5%。Osmani 的说法：agent 开始循环之前花一小时审计划，把"2000 行 diff 里找决策"变成"200 行计划里审决策"。审查对象从实现上移到意图、规格、接口、证据这条链。弱团队只审实现，强团队检查链条上任何断裂的环节。

**按风险分级。** 不是所有代码都值得同等审查。一次性脚本和原型，跑通就行。产品逻辑，审接口、不变量、边界情况。高风险变更（认证、支付、隐私、迁移、并发），人类专家逐段看，没有商量余地。GitLab 定义了五个自治级别，从 Level 1（人写一切，AI 只补全）到 Level 5（人设架构和质量标准，AI 做其余一切）。他们的警告很实在：跳过基础设施直接上 Level 4 或 5，会产出不可靠的结果并放大技术债。

**保持对系统的理解。** 这是 Uncle Bob 和 Dex 的分歧所在，也是 dark factory 失败的核心教训。Uncle Bob 能不读代码，是因为他通过指标（覆盖率、圈复杂度、依赖结构、模块大小）和 Gherkin 验收测试保持着对系统行为的高层理解。Dex 的教训是：如果你连架构层面的理解都放弃了，四个月后你将无法调试。实操上就是定期做"架构漫步"，不审具体 diff，但审系统结构是否在漂移。Osmani 的说法是"拥有外循环"：agent 执行内循环（调查、实现、测试），你拥有外循环（判断问题是否被正确解决、诊断是否合理、变更是否该批准、承担错误的后果）。

**让代码本身成为 agent 的基础设施。** Akita 的清单：函数 4-20 行，文件 500 行以内；命名唯一且可 grep（grep 回来超过 5 个不相关结果就是坏名字）；显式类型（Python 加 type hints 比任何逻辑重构都更能提升 agent 生产力）；注释写 WHY 不写 WHAT；可预测的目录结构；结构化 JSON 日志；幂等 setup 脚本。把这些写进 CLAUDE.md / AGENTS.md / .cursor/rules，agent 每次迭代都会读。Akita 说了一句大实话："No LLM does any of this by default." 你不写规则，agent 就按训练数据的平均水平来：80 行函数、无测试、重复逻辑、2000 行文件。

---

![为每个循环找到灯开关位置](https://cdn.mazhen.tech/2026/20260724220023954.png)

### 最后说几句

写到这里我比较确信的一个结论是：AI 没有取消软件工程的纪律，它改变了纪律的形式。旧的纪律是写代码、读代码、理解代码、对代码负责。新的纪律是**写规格、设约束、建验证体系、从指标推断质量、在判断力最值钱的地方保持灯亮、对结果负责**。

Uncle Bob 说"我是工程师，因为我承担责任"。这句话 2026 年和 2008 年一样成立。变化的是承担责任的方式：从"我读了每一行所以我知道它是对的"，变成"我设计了约束体系所以我有信心它是对的，我保持了架构理解所以出问题时我能介入"。

但我想诚实地加一句：这套新纪律的建设成本不低。200 行设计文档、多层验证管道、维护 CLAUDE.md、定期架构漫步，这些都要花时间。对 Uncle Bob 做的游戏和工具，这个成本可能低于逐行读代码。对大多数复杂的十年企业系统，这个成本可能更高。但替代方案，四个月后发现代码库不可维护，代价更高。

Dex Horthy 有一句话我觉得是这整场讨论最好的总结："The hard, skilled job is deciding where to put each switch." 全暗，四个月后推倒重来。全亮，没人能按时完成审查，你成为瓶颈。真正的工程技艺是为每一个循环找到正确的灯开关位置。

机器人可以在暗中操作。但人类需要看见自己在做什么。如果工厂地板上全是黑的，你什么都看不见，甚至找不到灯开关，那才是真正危险的地方。
