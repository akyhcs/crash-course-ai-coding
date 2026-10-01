## Handoff — simple explanation

The main idea is:

> **Compaction is for continuing work yourself. Handoff is for giving the work to someone/something else.**

### 1. What problem does `/handoff` solve?

Imagine this workflow:

```text
Claude
  ↓
implements feature
  ↓
finds bugs + makes decisions
  ↓
you want Codex to review it
```

The problem is that **Codex doesn't have Claude's conversation context**.

Claude knows things like:

* Why a particular design was chosen
* Which files were changed
* Which bugs were already fixed
* Which problems existed before your changes
* What has already been tested
* What still needs investigation

If you simply tell Codex:

```text
"Review my code"
```

Codex has to rediscover much of this.

That's where `/handoff` comes in.

---

# 2. Compaction vs Handoff

Think of them like this:

```text
                 CONTEXT TRANSFER
                       │
          ┌────────────┴────────────┐
          │                         │
      COMPACTION                 HANDOFF
          │                         │
          ↓                         ↓
 Same agent/session             Different agent
 Same workspace                Different workspace
          │                         │
          ↓                         ↓
 Claude → Claude               Claude → Codex
```

### Compaction

Compaction essentially says:

> "My current conversation is getting too large. Summarize it so I can continue."

```text
Session 1
   │
   │ compact
   ↓
Summary
   │
   ↓
Session 2
(same agent/context ecosystem)
```

### Handoff

Handoff says:

> "I'm finished with this session. Create a portable document containing everything the next agent needs."

```text
Claude session
      │
      │ /handoff
      ↓
 handoff.md
      │
      ├────→ Codex
      │
      ├────→ colleague
      │
      ├────→ another repo
      │
      └────→ future session
```

That's the key distinction.

---

# 3. Why is `handoff.md` useful?

The handoff document is a **secondary source of context**.

The original conversation is the **primary source**.

```text
PRIMARY SOURCE
────────────────────
Claude conversation
    │
    │ summarize
    ↓
SECONDARY SOURCE
────────────────────
handoff.md
    │
    ├──→ Codex
    ├──→ another Claude session
    ├──→ colleague
    └──→ another workspace
```

So instead of transferring the entire conversation, you transfer the important knowledge extracted from it.

---

# 4. What goes inside the handoff?

The example gives a very useful structure.

A good handoff might contain:

```text
1. Feature being worked on
2. Files that changed
3. Git status / git diff information
4. Important decisions
5. Bugs discovered and fixed
6. Known pre-existing problems
7. Verification already performed
8. Project conventions
9. Things the next agent should review
10. Suggested skills/tools for the next agent
```

For example:

```markdown
# Handoff: Star Rating Review

## Objective
Implement star-rating review functionality.

## Files Changed
- src/reviews/StarRating.ts
- src/reviews/ReviewForm.ts
- tests/reviews.test.ts

## Important Decisions
- Ratings are stored from 1–5.
- Existing reviews are updated rather than duplicated.

## Bugs Fixed
- Fixed duplicate review submission.
- Fixed invalid rating validation.

## Verification
- Unit tests pass.
- Manual testing completed.

## Known Pre-existing Issues
- Review pagination has an existing issue.
- Not related to this feature.

## Next Agent
Review the implementation for:
- edge cases
- test coverage
- API validation
- concurrency issues
```

Now Codex doesn't need to reconstruct all of that itself.

---

# 5. Why put it in `/tmp`?

This is an important detail.

The lesson intentionally doesn't save:

```text
project/
   handoff.md   ❌
```

Instead:

```text
/tmp/
   handoff-course-star-ratings-review.md
```

Why?

Because the handoff is intended to be **temporary**.

It's not part of the application.

It's not something you want to commit:

```bash
git add handoff.md
```

It's also not intended to become permanent agent memory.

Think:

```text
Source code
    ↓
Permanent

handoff.md
    ↓
Temporary bridge
```

So the handoff document is basically a **temporary context-transfer artifact**.

---

# 6. How do you actually use it?

Suppose Claude has finished implementing something.

You say:

```text
/handoff pass to Codex to review
```

The skill creates something like:

```text
/tmp/handoff-course-star-ratings-review.md
```

Then you start a fresh Codex session.

You provide:

```text
@/tmp/handoff-course-star-ratings-review.md
```

Codex reads it into its context.

Now the flow is:

```text
Claude
  │
  │ conversation
  │
  ↓
/handoff
  │
  ↓
handoff.md
  │
  │ portable
  ↓
Codex
  │
  ↓
Review implementation
```

---

# 7. The really important concept: portability

This is probably the biggest lesson.

Compaction is **agent/workspace-bound**.

Handoff is **portable**.

### Compaction

```text
Claude
  │
  └── compact
        │
        ↓
    Claude continues
```

### Handoff

```text
Claude
   │
   └── handoff.md
          │
          ├──→ Codex
          ├──→ Claude
          ├──→ colleague
          ├──→ another repo
          └──→ another machine/session
```

The Markdown file becomes the **interface between agents**.

---

# 8. Handoff can also be used for side tasks

This is a very practical use case from the lesson.

Suppose you're implementing:

```text
User authentication
```

while working you discover:

```text
Unrelated bug:
Payment calculation is wrong
```

You don't want to interrupt your authentication work.

So:

```text
Current task
     │
     ├── Authentication → continue now
     │
     └── Payment bug → /handoff
                         │
                         ↓
                  handoff document
                         │
                         ↓
                  fix in another session
```

Later you can start another agent/session with the handoff.

This is essentially **task parking with context**.

---

# 9. A useful mental model

Think of agents as developers.

### Compaction

You are talking to the **same developer**:

> "You've been working for a long time. Write yourself a summary so you can continue."

### Handoff

You are talking to a **different developer**:

> "Another developer will take over. Write everything they need to know."

That's why handoff needs to be more explicit.

---

# 10. Handoff vs Compaction — cheat sheet

| Situation                        | Use            |
| -------------------------------- | -------------- |
| Same agent continues             | **Compaction** |
| Same workspace                   | **Compaction** |
| Conversation is too large        | **Compaction** |
| Want to preserve current context | **Compaction** |
| Claude → Codex                   | **Handoff**    |
| Agent A → Agent B                | **Handoff**    |
| Repo A → Repo B                  | **Handoff**    |
| Give work to colleague           | **Handoff**    |
| Save a temporary task for later  | **Handoff**    |
| Need a portable context artifact | **Handoff**    |

### One-line rule

> **Compaction preserves context for continuation; handoff packages context for transfer.**

---

## How this fits with the lessons you've been studying

You can now see a larger pattern emerging:

```text
                    AI Coding Workflow
                           │
          ┌────────────────┼────────────────┐
          ↓                ↓                ↓
       /teach            /grill           /handoff
          │                │                │
          ↓                ↓                ↓
 Understand            Validate          Transfer
 codebase              requirements      context
          │                │                │
          └────────────────┼────────────────┘
                           ↓
                     Implementation
                           │
                           ↓
                       Compaction
                           │
                           ↓
                    Continue working
```

And the crucial distinction is:

```text
Compaction = CONTINUE
Handoff    = TRANSFER
```

That is the core idea of this lesson.
