# Compaction — Simple Explanation

The main idea of this lesson is:

> **When an AI coding agent's conversation becomes too large and noisy, use `/compact` to compress the old session into a smaller summary and continue in a fresh session.**

The important part is that **compaction is not the same as clearing the context**.

---

## 1. The problem: the context gets too big

Imagine you are working with Claude Code/Copilot Agent on a feature.

Over time, the conversation contains:

```text
User requirements
    ↓
Agent explores files
    ↓
Reads 50 files
    ↓
Changes 20 files
    ↓
Runs tests
    ↓
Fixes errors
    ↓
Changes more files
    ↓
Discusses implementation decisions
```

Eventually, the session becomes huge.

The lesson calls the useful portion of the context the **"smart zone."**

Once you move beyond that zone, the model may have more difficulty because the context contains a lot of irrelevant information.

For example:

```text
156k tokens

├── Important requirements
├── Important architectural decisions
├── Why certain code exists
├── File changes
├── Old file contents
├── Tool output
├── Errors
├── Intermediate attempts
├── Repeated information
└── Lots of noise
```

The problem isn't simply:

> "There are too many tokens."

It's also:

> **A large percentage of those tokens aren't useful anymore.**

The lesson specifically mentions file reads, file writes, and files in different intermediate states as sources of this noise. 

---

# 2. Option 1 — Continue the same session

You can simply keep working.

```text
Session
   ↓
156k tokens
   ↓
Continue
   ↓
More tokens
   ↓
More noise
```

### Advantage

You retain the **full history**.

### Problem

The context becomes increasingly noisy and expensive in terms of latency/capability.

The lesson summarizes this as:

| Approach | Tokens | Quality                        |
| -------- | -----: | ------------------------------ |
| Continue |  156k+ | Full context but lots of noise |
| Clear    |    ~5k | Clean but loses understanding  |
| Compact  |   ~28k | Summarized context             |



---

# 3. Option 2 — `/clear`

Another approach is:

```text
Old session
    ↓
/clear
    ↓
Fresh session
```

Now the agent has a clean context.

But there is a major problem.

The agent **forgets the reasoning behind the implementation**.

For example, suppose earlier you told the agent:

> "Don't use Redis here because the data consistency requirement means we need the database as the source of truth."

After `/clear`, the agent can inspect the code and see:

```java
repository.save(...)
```

But it may not know:

> **Why did we choose this design?**

It has to rediscover the project.

The lesson describes this as **lossy re-exploration** because the agent can inspect the code again but loses much of the original "why." 

---

# 4. Option 3 — `/compact`

This is the interesting solution.

Instead of:

```text
OLD SESSION
     ↓
    DELETE
     ↓
NEW SESSION
```

you do:

```text
OLD SESSION
     ↓
  SUMMARIZE
     ↓
COMPACTED CONTEXT
     ↓
NEW SESSION
```

So `/compact` essentially performs a **handoff between sessions**.

The lesson describes it as taking the current context, squeezing it down, and using that compressed context to seed a fresh session. 

---

# 5. What does compaction actually preserve?

This is probably the most important part to understand.

The compacted summary tries to preserve:

```text
┌──────────────────────────────┐
│       COMPACTION SUMMARY     │
├──────────────────────────────┤
│ Original request             │
│ Agreed specification         │
│ Technical concepts           │
│ Important files              │
│ Errors + fixes               │
│ Problem-solving approach     │
│ User messages                │
│ Pending tasks                │
└──────────────────────────────┘
```

The source explicitly lists these elements. 

So instead of carrying every single historical token forward, the agent tries to preserve the **important state of the work**.

---

# 6. Why does the instruction after `/compact` matter?

The lesson gives:

```text
/compact Yeah, we're going to do some QA in this area.
```

That sentence tells the summarization model **what matters next**. 

This is subtle but very important.

Suppose your previous session was:

```text
Implement feature
+
Fix bugs
+
Write tests
+
Now QA the feature
```

If you say:

```text
/compact We're going to do QA on the finished feature.
```

the summarizer has a hint:

> "When deciding what to preserve, emphasize things that will help with QA."

So the instruction acts like a **compression objective**.

---

# 7. Think of it like a handoff document

Imagine two developers.

### Developer A

Works on the feature for 3 days.

They know:

```text
Requirement
Architecture
Why decisions were made
Files changed
Known bugs
Tests
Things intentionally NOT implemented
```

Then Developer A leaves.

Instead of giving Developer B the entire 3-day conversation, they give them:

```text
Project handoff document
```

Something like:

```text
Goal:
Implement customer comments.

Important decisions:
- Comments are stored in PostgreSQL.
- Redis was intentionally not used.
- Maximum comment length = 5000.

Files:
- app/lib/comments.ts
- app/api/comments.ts

Known issue:
- Validation currently happens on client and server.

Next task:
- QA the implementation.
```

That is essentially what compaction is doing.

---

# 8. But there is a catch: compaction is LOSSY

This is the most important concept in the entire lesson.

Suppose the original conversation contains:

```text
100 pieces of information
```

After compaction:

```text
100 pieces
      ↓
summarization
      ↓
30 pieces
```

The 30 pieces hopefully contain the important information.

But:

```text
30 ≠ 100
```

Some nuance disappears.

The lesson calls the original conversation the **primary source** and the compacted summary a **secondary source**. 

Think:

```text
PRIMARY SOURCE
Original conversation
        │
        │ compression
        ▼
SECONDARY SOURCE
Compaction summary
```

The secondary source is therefore **lossy**.

---

# 9. Why is this still useful?

Because you are trading:

```text
More information
      +
More noise
      +
Less room
```

for:

```text
Less information
      +
Less noise
      +
Much more room
```

The lesson summarizes this trade-off as:

|                   | Information | Noise | Maneuverability |
| ----------------- | ----------- | ----- | --------------- |
| Original session  | Full        | Lots  | Limited         |
| Compacted session | Lossy       | Less  | More            |



---

# 10. The key use case: QA

This is where the lesson recommends compaction particularly strongly.

Imagine:

```text
Phase 1
Understand requirement
      ↓
Phase 2
Design
      ↓
Phase 3
Implement
      ↓
Phase 4
Debug
      ↓
Phase 5
Feature complete
      ↓
       🔥 CONTEXT HUGE
      ↓
/compact "Now QA this feature"
      ↓
Fresh compacted session
      ↓
QA
```

Why is this a good point to compact?

Because you're **not trying to redesign the feature**.

You're saying:

> "The implementation is done. Now validate it."

The relevant knowledge can be summarized relatively effectively.

The lesson specifically identifies QA of a finished feature as an especially good situation for compaction. 

---

# 11. Compaction vs Clear

This distinction is worth memorizing.

### `/clear`

```text
OLD CONTEXT
    ↓
   ❌
    ↓
EMPTY CONTEXT
```

The agent must rediscover the project.

### `/compact`

```text
OLD CONTEXT
    ↓
SUMMARIZE
    ↓
IMPORTANT STATE
    ↓
NEW CONTEXT
```

The agent starts with a compressed understanding of the previous work.

---

# 12. Compaction vs caching

Another important distinction from the quiz:

**Caching is not compaction.**

If you stay in the same session:

```text
Old tokens
   ↓
still there
   ↓
cached
```

They may be cheaper to process, but they are **still part of the original context**.

With compaction:

```text
Old context
   ↓
summarized
   ↓
fresh session
```

The old detailed tokens are not simply sitting there waiting to be retrieved in full.

---

# 13. A practical workflow

For AI coding, you can think of your workflow like this:

```text
             BUILD
               │
               ▼
        Context grows
               │
               ▼
       Feature completed?
          /          \
        No            Yes
        │              │
        │              ▼
        │          /compact
        │              │
        │              ▼
        │        Fresh session
        │              │
        │              ▼
        │             QA
        │              │
        └──────────────┘
```

And the command could be:

```text
/compact We're done implementing the feature. Next, we're going to QA it thoroughly.
```

The important part isn't the exact wording.

It's that you give the summarizer **the goal of the next phase**.

---

# 14. The deeper concept

This lesson is really teaching a broader principle about **agent context management**:

> **Context is a resource.**

You don't necessarily want:

```text
MAXIMUM CONTEXT
```

You want:

```text
MAXIMUM RELEVANT CONTEXT
```

That's a major difference.

For example:

```text
156k tokens
│
├── 20k useful
├── 30k somewhat useful
└── 106k historical noise
```

Having 156k tokens doesn't automatically mean the agent is better informed.

Sometimes:

```text
28k highly relevant tokens
```

can be more useful than:

```text
156k mixed-quality tokens
```

---

# 15. One-line mental model

Remember this:

> **`/clear` = forget and start again.**
> **`/compact` = summarize what matters and continue in a fresh context.**

And the critical caveat:

> **Compaction preserves context, but not perfectly — it is a lossy secondary source.** 

### Quiz answers from the lesson

**Q1:** What happens to the old detail after compaction?

✅ **Lossy — it becomes a summary and some nuance is gone.** 

**Q2:** What should you type before QA?

✅ **`/compact` + a short sentence explaining what you're doing next.** 

The overall progression you're learning from these AI coding lessons is essentially:

```text
Understand
   ↓
Plan
   ↓
Execute
   ↓
Context grows
   ↓
Compact
   ↓
QA / Continue
```

That makes **compaction a context-management/handoff mechanism**, rather than an implementation technique.
