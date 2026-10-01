# The Steering Map — Simple Explanation

The main idea of this lesson is:

> **Don't put every instruction directly into the agent's context. Put frequently needed instructions there, and point to less-frequently needed instructions.**

The file explains this through **context load → push vs. point → AGENTS.md → over-pushing**. 

---

## 1. What is Steering?

Imagine you have an AI coding agent working on your project.

You want it to follow rules such as:

* How we write SQL
* How we write tests
* How migrations should be created
* How errors should be handled
* How APIs should be structured
* Which libraries we prefer

**Steering** means controlling/guiding the agent's behavior across sessions.

The problem is that a fresh session doesn't automatically remember your project-specific conventions.

So you need a way to tell the agent:

> "These are the rules for working in this codebase."

That's what **steering** is about. 

---

# 2. The Most Important Concept: Context Load

This is probably the most important part of the lesson.

Suppose you put this into the agent's context:

```text
Always use PostgreSQL.
Always write unit tests.
Always use repository pattern.
Always use async functions.
Always validate API input.
Always use structured logging.
...
```

Those instructions aren't free.

The lesson says:

> Anything loaded into the agent up front is paid for on every model-provider request. 

### Why?

A session might look like:

```text
Session
│
├── Turn 1
│   ├── Request 1
│   ├── Request 2
│   └── Request 3
│
├── Turn 2
│   ├── Request 4
│   ├── Request 5
│   └── Request 6
│
└── Turn 3
    ├── Request 7
    ├── Request 8
    └── Request 9
```

The context/history is carried into these requests.

So if you add 1,000 tokens of instructions at the beginning, those instructions can keep getting carried along.

---

# 3. Context Load Has TWO Costs

This is an important distinction.

### Cost #1 — Tokens

You are sending those instructions repeatedly.

```text
Instruction
    ↓
Request 1
Request 2
Request 3
Request 4
...
```

So there is a token/context cost.

### Cost #2 — Attention

This is more subtle.

The model has limited attention.

If you give it:

```text
Rule A
Rule B
Rule C
Rule D
Rule E
Rule F
Rule G
Rule H
Rule I
Rule J
...
```

then every additional instruction competes for the model's attention.

The lesson describes this as:

> Every extra instruction makes every other instruction a little quieter. 

So:

```text
More context
     ↓
More tokens
     +
More things competing for attention
     ↓
Higher context load
```

### Key takeaway

**Context load = token cost + attention cost.**

That's why blindly adding instructions isn't necessarily good.

---

# 4. Push vs Point

This is the core concept of the lesson.

There are two ways to give instructions to an agent:

```text
PUSH
vs
POINT
```

---

# 5. PUSH = Always-On Instructions

With **push**, you put the complete instruction directly into the context.

For example:

```text
AGENTS.md

Always use repository pattern.
Always write tests.
Always use PostgreSQL.
Always follow this 40-line migration procedure...
```

The agent sees those instructions immediately.

Think:

```text
┌───────────────────────────────┐
│       Agent Context           │
│                               │
│  BIG INSTRUCTION BLOCK        │
│                               │
│  BIG INSTRUCTION BLOCK        │
│                               │
│  User request                 │
└───────────────────────────────┘
```

The advantage:

**The agent can't miss the instruction.**

The disadvantage:

**You pay the context cost even when the instruction isn't relevant.** 

---

# 6. POINT = On-Demand Instructions

Instead of putting the whole instruction into the context, you put it somewhere in the project.

Then give the agent a small pointer.

For example:

```text
AGENTS.md

When working on database migrations,
read docs/database-migrations.md.
```

The actual 40-line instruction lives here:

```text
docs/database-migrations.md
```

So the context contains only:

```text
"When working on database migrations,
read docs/database-migrations.md."
```

That's a **context pointer**.

The agent follows the pointer when the task requires it. 

---

# 7. PUSH vs POINT — Visualize It

### PUSH

```text
             AGENT CONTEXT
┌─────────────────────────────────┐
│                                 │
│  ███████████████████████████    │
│  █ Migration Instructions █     │
│  ███████████████████████████    │
│                                 │
│  User request                   │
└─────────────────────────────────┘
```

The entire instruction is always present.

---

### POINT

```text
             AGENT CONTEXT
┌─────────────────────────────────┐
│                                 │
│  → Read migration instructions  │
│                                 │
│  User request                   │
└─────────────────────────────────┘
                  │
                  ↓
       docs/migrations.md
       ┌─────────────────────┐
       │ 40 lines of rules   │
       │ ...                 │
       └─────────────────────┘
```

Only the pointer is present initially.

The full instructions are loaded when needed.

The lesson summarizes it as:

| Strategy  | Cost                  | Availability     |
| --------- | --------------------- | ---------------- |
| **Push**  | High on every request | Always present   |
| **Point** | Minimal most turns    | Only when needed |



---

# 8. Where Does PUSH Usually Live?

A common place is:

```text
AGENTS.md
```

Typically at the root of your project.

For example:

```text
my-project/
│
├── AGENTS.md
│
├── src/
├── tests/
├── docs/
└── package.json
```

The harness can load `AGENTS.md` when the session starts.

The lesson also notes that Claude Code uses:

```text
CLAUDE.md
```

for a similar purpose. 

---

# 9. Why Can't We Just Put Everything in AGENTS.md?

This is where the lesson gets interesting.

Imagine you put:

```text
AGENTS.md

1. Coding standards
2. Database rules
3. Migration rules
4. Security rules
5. Frontend rules
6. Backend rules
7. Deployment rules
8. Testing rules
9. Documentation rules
10. Performance rules
11. ...
```

You might think:

> "Great! Now the agent knows everything."

But that's potentially a bad strategy.

Why?

Because **all those rules are pushed into the context even when they're irrelevant.**

The lesson calls this the **"epidemic of over-pushing."** 

---

# 10. The Chocolate Cake Example 🍰

This example makes the problem very clear.

Suppose your global instruction says:

```text
I work under PCI DSS compliance.
Anything security relevant should be flagged.
```

Now the user asks:

> "Give me a chocolate cake recipe."

The agent might respond:

```text
Here is a chocolate cake recipe...

Also, because you work under PCI DSS,
you should consider the security implications
of handling chocolate cake...
```

😂

Obviously that's ridiculous.

Why did it happen?

Because the security instruction was **pushed globally**.

The agent sees:

```text
EVERY TASK
   ↓
Security rule
```

instead of:

```text
Security-related task
   ↓
Security rule
```

The lesson's point is that pushed rules can be applied in situations where they don't belong. 

---

# 11. The Better Design

Instead of:

```text
AGENTS.md

ALWAYS:
Security rules...
40 lines...
```

use:

```text
AGENTS.md

For security-related work,
read docs/security.md
```

Then:

```text
docs/
├── security.md
├── migrations.md
├── testing.md
├── frontend.md
└── deployment.md
```

Now:

```text
Coding task
   ↓
Does it involve security?
   ↓
No
   ↓
Don't load security rules
```

But:

```text
Security task
   ↓
Does it involve security?
   ↓
Yes
   ↓
Read security.md
```

That's **pointing**.

---

# 12. The Rule to Remember

The course gives a very important default:

> **Point by default.** 

Think of it like this:

```text
                    STEERING
                       │
              ┌────────┴────────┐
              │                 │
            PUSH              POINT
              │                 │
       Always needed?      Sometimes needed?
              │                 │
             YES              YES
              │                 │
        Put in context      Put in file
                              +
                         small pointer
```

---

# 13. When Should You PUSH?

Use **push** when the rule is:

### 1. Extremely important

Example:

```text
Never expose secrets in logs.
```

### 2. Almost always relevant

Example:

```text
Use TypeScript for this repository.
```

### 3. Short

Example:

```text
Run tests before declaring the task complete.
```

The key is:

> **High-frequency + high-importance instructions are good candidates for push.**

---

# 14. When Should You POINT?

Use **pointing** when the instruction is:

### Infrequently needed

Example:

```text
Database migration procedure
```

if migrations happen only once a week.

### Large

Example:

```text
40-line migration procedure
```

### Domain-specific

Example:

```text
Payments architecture rules
```

when you're currently working on authentication.

### Conditional

Example:

```text
When modifying the payment system,
read docs/payments.md.
```

---

# 15. The Quiz Example

The file gives this exact situation:

> A database migration convention occurs about once a week and takes 40 lines.

The answer is:

```text
POINT
```

So:

```text
AGENTS.md

When creating database migrations,
read docs/database-migrations.md
```

rather than putting all 40 lines directly into `AGENTS.md`.

Why?

Because otherwise you're effectively saying:

```text
40 lines
×
every model request
×
every session
```

for something needed only occasionally. 

---

# 16. One More Important Concept: "Pointing" Is Not the Same as Manual Prompting

You might think:

> "Why don't I just paste the migration instructions whenever I need them?"

The lesson says that's also not ideal.

Because now **you** have to remember to do it.

So there are three possibilities:

```text
PUSH
↓
Agent always gets it
But expensive

POINT
↓
Agent knows where to get it
Cheap most of the time

MANUAL PROMPT
↓
You remember to provide it
Cheap for context
But easy to forget
```

The preferred approach for infrequent but important rules is:

```text
POINT
```

because the agent can pull the rule when appropriate. 

---

# 17. How This Fits With Agent Skills

This connects directly to the lessons you've been studying about **skills, compaction, handoffs, and context engineering**.

You can think of the architecture like this:

```text
                    AGENT
                      │
             ┌────────┴────────┐
             │                 │
          ALWAYS             ON-DEMAND
          NEEDED              NEEDED
             │                 │
             ↓                 ↓
        PUSH / AGENTS.md    POINT / SKILLS
                               │
                               ↓
                        Specific instructions
```

So instead of making one gigantic:

```text
AGENTS.md
```

you can have a lightweight steering map:

```text
AGENTS.md
   │
   ├── → database instructions
   ├── → testing instructions
   ├── → frontend instructions
   ├── → security instructions
   └── → deployment instructions
```

The agent then loads the relevant material based on the work.

---

# 18. The Big Mental Model

I'd remember the entire lesson with this:

```text
        STEERING THE AGENT
               │
               ↓
      What instructions
       should it know?
               │
       ┌───────┴───────┐
       ↓               ↓
    ALWAYS           SOMETIMES
    NEEDED            NEEDED
       │               │
       ↓               ↓
     PUSH             POINT
       │               │
       ↓               ↓
  AGENTS.md       Separate file
       │               │
       ↓               ↓
Always in          Load only
context            when needed
       │               │
       └───────┬───────┘
               ↓
         Lower context
             load
```

### One sentence to memorize:

> **Push small, universal rules; point to large or situation-specific rules.**

And the deeper principle is:

> **Steering isn't just about creating good instructions. It's about deciding which instructions deserve permanent context and which should be loaded only when relevant.** 
