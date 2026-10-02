This lesson is explaining **what an Agent Skill is, how it differs from the earlier document + pointer pattern, and when a skill should be model-invoked vs user-invoked.** 

### The core idea

Think of it like this:

```text
Document + Pointer
        ↓
   works in one repo
        ↓
Skill = Document + Pointer + Packaging
        ↓
portable and reusable
```

A **skill is essentially a packaged pointer**. It contains a `SKILL.md` plus optional supporting files, all bundled into one folder. 

For example:

```text
.agents/
└── skills/
    └── writing-for-agents/
        ├── SKILL.md
        ├── SKILL-MECHANICS.md
        └── examples/
```

The important part is that **the entire folder is not loaded into the context automatically**.

---

## 1. What stays in context?

For a **model-invoked skill**, only:

```text
name + description
```

stays in the context.

Then, if the agent thinks:

> "This task looks relevant to `writing-for-agents`."

it loads:

```text
SKILL.md
```

And if `SKILL.md` says:

> "For frontmatter details, read SKILL-MECHANICS.md"

then it can load that file too.

So the chain is:

```text
Context Window
      ↓
name + description
      ↓
SKILL.md
      ↓
supporting files
```

This is the **layered / progressive disclosure** idea in the lesson. 

### Why is this useful?

Imagine your skill contains 500 lines of instructions.

You **don't** want those 500 lines occupying context on every request.

Instead:

```text
Always:
  "writing-for-agents — use when creating/editing agent instructions"

Only when relevant:
  → SKILL.md

Only when specifically needed:
  → SKILL-MECHANICS.md
```

That's the main optimization.

---

# 2. Skill vs Instruction

This connects directly to your previous question.

### Instruction

An instruction is essentially:

> **"Do X when Y happens."**

For example:

```text
Whenever you modify database schema,
run the migration tests before committing.
```

It tells the agent **what to do**.

### Skill

A skill is a **reusable package of instructions and supporting knowledge**.

For example:

```text
writing-for-agents/
├── SKILL.md
├── SKILL-MECHANICS.md
└── examples/
```

So:

```text
Instruction
    ↓
the rule/behavior

Skill
    ↓
a packaged collection of instructions + references
```

The skill is therefore **not just another type of instruction**.

It's a **delivery mechanism for instructions and knowledge**.

---

# 3. Why package it as a Skill?

The earlier document + pointer approach has a limitation.

Suppose you have:

```text
AGENTS.md
    ↓
docs/database-migrations.md
```

That works nicely in your repository.

But now another team wants the same process.

You would have to manually copy:

```text
AGENTS.md
docs/database-migrations.md
```

The lesson says this becomes difficult to carry between projects. 

A skill solves that:

```text
database-migrations/
├── SKILL.md
└── migration-details.md
```

You can package the folder and share it.

Therefore:

```text
Document + pointer
        = local reuse

Skill
        = portable reuse
```

The lesson explicitly describes skills as **portable pointers**. 

---

# 4. Model-invoked vs User-invoked

This is probably the most important part of the lesson.

There are two ways a skill can be triggered. 

## Model-invoked

The agent can discover it automatically.

Example:

```yaml
name: writing-for-agents
description: Writing documents for agents. Use when creating or editing skills...
```

You ask:

```text
Create a new SKILL.md for database migrations.
```

The agent sees:

```text
writing-for-agents
```

and thinks:

> "This skill is relevant."

Then it loads it.

You don't need to explicitly say:

```text
/use writing-for-agents
```

### Cost

You pay a small **context cost**:

```text
name + description
```

is always visible.

But you get automatic discovery.

---

# 5. User-invoked Skill

You can instead write:

```yaml
name: handoff
description: Compact the current conversation into a handoff document.
disable-model-invocation: true
```

Now the agent **cannot automatically discover it**.

You have to explicitly invoke it.

For example:

```text
/handoff
```

The benefit:

```text
0 context cost
```

The downside:

```text
YOU must remember the skill exists.
```

That's why the lesson describes this as a trade-off between **context load** and **cognitive load**. 

---

# 6. The easiest way to remember this

Use this mental model:

```text
MODEL-INVOKED

Context:
┌───────────────────────────────┐
│ skill name + description      │ ← always here
└───────────────────────────────┘
              ↓
       Agent notices it
              ↓
          SKILL.md
              ↓
       supporting files
```

Versus:

```text
USER-INVOKED

Context:
┌───────────────────────────────┐
│ nothing about the skill       │
└───────────────────────────────┘

              ↓
        YOU type /skill-name
              ↓
          SKILL.md
              ↓
       supporting files
```

---

# 7. One subtle point in the lesson

Look at this:

> `disable-model-invocation: true`

It does **not** mean:

> "Don't load this skill."

It means:

> **"Don't let the model discover/invoke this skill automatically."**

You can still explicitly invoke it yourself.

So:

```text
disable-model-invocation: true
```

controls **who can trigger the skill**.

It does not mean the skill doesn't exist.

---

# 8. Why the second quiz answer is "model"

The lesson gives this situation:

> You wrote a skill you need maybe twice a month, and you know you will forget it exists.

The correct choice is:

```text
model-invoked
```

Why?

Because the problem isn't context.

The problem is:

> **"I will forget to invoke it."**

So you want the agent to see the description and recognize:

```text
"This task looks like that skill."
```

The lesson explicitly explains this trade-off. 

---

# 9. Why supporting files are important

Suppose:

```text
SKILL.md
```

has 100 lines.

But 30 of those lines are detailed frontmatter mechanics that are needed only 10% of the time.

Don't do:

```text
SKILL.md
├── general instructions
├── general instructions
├── 30 lines of rare details
└── more instructions
```

Instead:

```text
SKILL.md
    ↓
"When dealing with frontmatter, read
 SKILL-MECHANICS.md"

SKILL-MECHANICS.md
    ↓
30 lines of detailed information
```

Now the detailed information enters context **only when needed**.

That's progressive disclosure. 

---

## The whole lesson in one diagram

```text
                 SKILL
                   │
          ┌────────┴────────┐
          │                 │
    Name + Description   SKILL.md
          │                 │
      always visible       loaded
          │              when needed
          │                 │
          │          supporting files
          │                 │
          └────────┬────────┘
                   ↓
             Agent behavior
```

And the key distinction is:

```text
Instruction
= WHAT the agent should do

Pointer
= WHERE the detailed instruction lives

Skill
= POINTER + INSTRUCTIONS + SUPPORTING FILES
  packaged into a portable reusable unit
```

That last line is probably the **single most important takeaway** from this lesson.


-----


Yes — **you can achieve the same behavior with a skill**, but the important question is **what problem each one is solving**.

The easiest way to understand it is:

> **Instruction = the actual rule/behavior.**
> **Skill = a packaged, reusable way to deliver those instructions when needed.**

For example, suppose you want the agent to always follow this rule:

```text
Whenever you modify the database schema:
1. Check existing migrations
2. Create a migration
3. Run migration tests
4. Verify backward compatibility
```

That is an **instruction**.

You could put it directly in `AGENTS.md`:

```text
AGENTS.md

Whenever you modify the database schema:
follow these migration steps...
```

The agent gets that instruction as part of the project's normal guidance.

---

### Now put the same thing into a Skill

You could have:

```text
skills/
└── database-migrations/
    └── SKILL.md
```

And inside:

```text
SKILL.md

When modifying the database schema:

1. Check existing migrations
2. Create a migration
3. Run migration tests
4. Verify backward compatibility
```

So yes:

```text
Instruction:
    "Do these 4 things."

Skill:
    "Here is a reusable package containing those 4 things."
```

**The actual logic can be identical.**

The difference is **how and when that logic enters the agent's context.**

---

## This is the key difference

Imagine you have 20 different instructions.

### Approach 1 — Put everything in `AGENTS.md`

```text
AGENTS.md
│
├── Database rules
├── Testing rules
├── API rules
├── Security rules
├── Documentation rules
├── Deployment rules
├── React rules
└── ...
```

The agent potentially carries all of this guidance around.

---

### Approach 2 — Package specialized instructions as Skills

```text
AGENTS.md
│
├── core project rules
│
skills/
├── database-migrations/
├── testing/
├── api-development/
├── security/
└── documentation/
```

Now you can have:

```text
Database task
      ↓
database-migrations skill
      ↓
load detailed instructions
```

while:

```text
Frontend task
      ↓
frontend skill
      ↓
load frontend instructions
```

This is why the lesson calls a skill a **"pointer, packaged."** 

---

## So what are we actually trying to achieve?

There are **two separate goals**:

### Goal 1 — Tell the agent how to behave

That's the **instruction**.

```text
"Always run migration tests."
```

### Goal 2 — Don't load every piece of knowledge all the time

That's where **pointer + skill + progressive disclosure** comes in.

```text
Only tell the agent:

"database-migrations exists and is relevant to database work."

                ↓

Agent needs it

                ↓

Load SKILL.md

                ↓

Need more detail?

                ↓

Load supporting document
```

The lesson explicitly describes this layered loading: name/description → `SKILL.md` → supporting files. 

---

### One analogy

Think of a **restaurant menu**.

**Instruction:**

> "If the customer orders pizza, use the oven at 250°C for 10 minutes."

That's the actual instruction.

**Skill:**

> A packaged "Pizza Preparation" folder containing:
>
> * cooking instructions
> * recipes
> * oven settings
> * special cases

And the agent initially only sees:

```text
Pizza Preparation
"Use when preparing pizza."
```

It opens the detailed material **only when pizza is actually being prepared**.

---

So your intuition is correct:

> **"Can't we achieve the same thing with a skill?"**

**Yes.**

But the lesson isn't saying *skills replace instructions*.

It's saying:

> **Skills are a mechanism for packaging, discovering, and progressively loading instructions/knowledge.**

That's why **instruction = what the agent should do**, while **skill = a reusable package that can contain the instructions for a particular kind of work**.
