# QuestionSkillForAi

A Claude Code skill that interrogates code with a numbered bank of **strong questions** —
questions phrased to force a `file:line` citation, a count, a counter-example, or a
self-graded evidence tier, instead of "looks fine to me".

It also **keeps score**: every time a question actually catches a bug, that question's
counter goes up — so the list gradually sorts itself by real-world yield instead of by hunch.

> Content is in Chinese. 内容为中文。

---

## 这是什么

一份**强问法**清单，加一套用它扫描代码的工作流。

核心信念：多数 bug 不是"想到了但写错了"，是**没想到要问这一层**。而问法本身决定了能不能被想到——

| 弱问法 | 得到的 |
|---|---|
| 「这里有并发问题吗？」 | 一段听起来很周到的推理，结论是"应该没问题" |
| 「**谁**保证 A 和 B 之间不会插进第三个请求？**把那行贴出来**。贴不出来就直说没有。」 | 一个文件行号，或者一句"没有" |

组成：

- **8 条通用变换**（`T1`–`T8`）—— 把弱问法改造成强问法的手法
- **8 类 60 个问题**（`1.1`–`8.4`）—— 时序与并发 / 数据源与状态 / 约束的执行力 / 凭据密钥与生命周期 / 判据与证据 / 默认值兜底与未知态 / 复用漂移与文案 / 信任边界
- **计分表** —— 命中过的问题累计次数与最高严重度，用数据分层，而不是凭印象

## 安装

```bash
git clone https://github.com/CTW2000/QuestionSkillForAi.git
cp -r QuestionSkillForAi/question ~/.claude/skills/
```

装到 `~/.claude/skills/` 是用户级，所有项目都能用；装到项目里的 `.claude/skills/` 则只在该项目生效。

## 用法

| 输入 | 行为 |
|---|---|
| `/question` | **动笔前**模式：把正在讨论的设计过一遍，说出必须先想清的几件事 |
| `/question src/payment/` | **扫描**模式：自动选节，范围大就按节分派子代理 |
| `/question 用第一、五节扫刚才的 diff` | 只用点名的小节 |

也可以不敲斜杠——说「帮我看看这段有没有并发问题」同样会触发。

## 计分怎么用

扫描结束后回写 `questions.md` 文末两张表：

- **命中榜**：命中的编号 `+1`，记最高严重度
- **扫描日志**：每次扫描登记用了哪几节，**0 命中也要记**

第二张表是第一张表的**分母**。没有它，「0 命中」分不清是"问过十次都没事"（真的低价值）还是"从来没问过"（没采样）——前者可以删，后者删了就是在扔掉还没验证过的问题。

排名要看**「命中 × 最高严重度」两列一起**。只按次数排，低频高危的问题（凭据、越权、资金）会被系统性地压到底部，而那恰恰是最不该删的一类。

## 维护约定

- **编号永不复用**：删掉一条，它的编号一并作废。编号是身份不是位置，顺序可按逻辑调整。
- **没有条数上限**：清单变长就多派子代理，不要为了控长度而删问题。
- **删除必须人工确认**，且要同时满足「问过很多次仍 0 命中」且「从未命中过中危以上」。
- 新问题必须是强问法：要位置、要计数、要反例、要分档，不能是"是/否"能打发的。

## 由来

从一次真实项目的逐提交复盘中提炼——把踩过的坑归类，反推出"当初该问什么"，再把问法本身打磨成可复用的模板。所有项目专有的证据与提交号已移除，只保留可迁移的问法。
