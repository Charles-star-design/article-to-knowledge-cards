---
name: make-knowledge-cards
description: Convert an article pasted in chat or a local Markdown/TXT file into 5-8 concise knowledge cards (fewer when the source is short or repetitive). Use this skill whenever the user wants to distill an article into study cards, revision notes, key takeaways, or self-test questions, or says "make knowledge cards", "generate flashcards from this article", "summarize into cards", or provides an .md/.txt file and wants it turned into structured, one-idea-per-card notes. Do not use for web pages, PDFs, Anki package generation, or GUI automation.
license: MIT
---

# Make Knowledge Cards

Turn a source article into a small set of durable study cards. The job is faithful compression: keep what actually matters, make each card hold exactly one idea, and never invent knowledge the source did not contain.

## Scope

- **Supported inputs:** article text pasted directly in the conversation, or a local `.md` / `.txt` file the user points to.
- **Not supported:** fetching web pages or URLs, PDF files, generating Anki decks or `.apkg` packages, and any GUI automation. If a request involves these, say the input is unsupported and ask the user to paste the text or provide a `.md`/`.txt` file instead. Do not silently substitute a workaround.

## Output contract

Produce **5-8 cards** as Markdown for a rich source — but fewer when the source genuinely holds fewer distinct ideas. The number follows the material, never the other way around. Each card has exactly four fields:

```markdown
### 卡片 N：简短标题

- **核心知识：** 一句话点明这张卡的唯一知识点。
- **简明解释：** 用白话解释这个知识点，说清它是什么、为什么重要或如何运作。
- **例子 / 自测问题：** 给出原文中的例子，或提出一个能检验理解的问题（附答案要点）。
```

Rules for the fields:

- **标题** — short and specific; it should name the single idea, not a whole section. Avoid vague titles like "背景介绍" or "总结".
- **核心知识** — one sentence, one idea. If you find yourself writing two claims joined by "并且/以及", check whether they are really one concept with parallel members (see Boundary cases) before splitting.
- **简明解释** — clarify the idea in plain language. Stay within what the source supports; do not add outside knowledge, even if you think it is helpful.
- **例子 / 自测问题** — prefer an example that actually appears in the source. If the source has none, write a self-test question with its answer point. Composing the wording of a question yourself is expected; inventing the example, scenario, or statistic it refers to is not. A question is valid only if its answer comes from the source.

## The core discipline

These are the constraints that separate good cards from a bloated summary. Apply them throughout.

1. **Extract what truly matters.** Prioritize the author's main claims, key definitions, cause-and-effect relationships, methods/steps, and conclusions. A practical test: an idea earns a card only if it answers a distinct "what / why / how" question on its own. Drop rhetorical filler, anecdotes, transitions, and repetition. A card about a trivial detail is worse than no card.
2. **One card, one idea.** "One idea" means one claim or one mechanism, not one sentence fragment. A single concept with several parallel members — for example, "two uses: food storage and rearing larvae" — is still one idea and stays together. Split only when the parts are genuinely separate claims a reader could understand independently. If an idea is too small to stand alone, fold it into the related card rather than padding the count.
3. **Remove duplication.** When the source restates the same point in different words, keep it once — in the clearest form. If two candidate cards would answer the same question, they are duplicates: merge them and keep the more concrete phrasing (a real example or number beats a vague restatement). Merge near-identical cards before outputting.
4. **Never invent.** Every factual claim, number, example, and answer point must be traceable to the source text. If something is unclear or ambiguous in the source, say so in the card or omit it — do not fill the gap with plausible-sounding content.
5. **Do not force the count.** 5-8 is the target range for a rich article, not a quota. If the source only genuinely contains 3 distinct ideas, output 3 cards and state briefly that the source had limited distinct content. If the source is mostly repetition, merge it down to the few real ideas. Quality over quantity, always.

## Boundary cases

- **Parallel members = one card.** "The chamber has two uses: storing food and rearing larvae" is one idea about the chamber's function, not two cards. The same goes for a list of values belonging to one CSS property.
- **Restatements = one card.** "Keep going no matter what", "small daily efforts add up", and "time will magnify it" express the same idea about persistence; merge them rather than issuing a card per sentence.
- **A self-test question may be your own wording.** Only its answer must be in the source. When the article offers no example at all, a question is the preferred way to fill the field.

## Workflow

1. **Read the whole source.** Identify the central thesis and enumerate the distinct, important ideas. Note which ideas are definitions, which are mechanisms, which are examples, and which are merely repeated.
2. **Select and deduplicate.** Choose the ideas that earn a card — those that answer a distinct what/why/how question. Merge restatements and parallel members of one concept. Discard padding. Decide the realistic card count based on what is actually there, not on the target range.
3. **Draft each card.** Apply the four-field template. Keep 核心知识 to a single idea, and source 例子/自测问题 from the article whenever possible.
4. **Audit before output.** For each card, check:
   - Does it contain only one idea?
   - Is every claim grounded in the source?
   - Is it a duplicate of another card?
   - Is this idea important enough to keep?
   Remove or merge any card that fails a check. The final count should reflect the number of genuinely distinct ideas — normally 5-8 for a rich article, but smaller without apology when the source is thin or repetitive. Never split an idea merely to reach five.
5. **Output only the cards**, preceded by a single blockquote line stating the source and the card count. No preamble about your process.

## Worked example

**Source excerpt:** "睡眠不足会削弱免疫系统。研究发现，每晚睡眠少于6小时的人，接触感冒病毒后患病概率是睡足8小时者的四倍。此外，深度睡眠阶段身体会释放更多细胞因子，这类蛋白质帮助免疫细胞对抗感染。"

**Output:**

> 来源：用户粘贴的片段；共 2 张卡片（原文知识点有限，未强行补足）。

### 卡片 1：睡眠不足削弱免疫防御
- **核心知识：** 睡眠不足会削弱免疫系统，使人更容易患病。
- **简明解释：** 原文指出睡眠不足对免疫防御有负面影响，是后文具体数据和机制的前提。
- **例子 / 自测问题：** 自测：根据原文，睡眠不足对人体免疫的总体影响是什么？答案要点：削弱免疫系统，更容易患病。

### 卡片 2：少于6小时睡眠使感冒概率增至四倍
- **核心知识：** 每晚睡眠少于6小时的人，接触感冒病毒后患病概率是睡足8小时者的四倍。
- **简明解释：** 这是原文给出的具体量化证据，用对比说明睡眠时长与患病风险的关系。
- **例子 / 自测问题：** 例子：原文对比了每晚睡少于6小时与睡足8小时两组人，前者患病概率是后者的四倍。

Note how the example splits two distinct ideas (general effect vs. specific statistic), omits a third minor point rather than padding, and keeps every claim traceable to the source.
