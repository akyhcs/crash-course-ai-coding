The main idea is **not about two different kinds of skills**. It is the **same skill mechanism**, but with two different scopes.

### Think of it like this

```text
                    SKILL
                      │
          ┌───────────┴───────────┐
          │                       │
     USER-LEVEL              PROJECT-LEVEL
   ~/.agents/skills/       .agents/skills/
          │                       │
     "How I work"          "How this project works"
          │                       │
       Personal                 Team
       Global                 Project-specific
       Not committed          Git committed
```

### 1. User-level skill

Location:

```text
~/.agents/skills/
```

This means:

> **"I want this behavior wherever I work."**

For example, suppose you personally like to use a `/teach` skill that explains code in a certain format.

You create:

```text
~/.agents/skills/teach/
    SKILL.md
```

Now when you open:

```text
project-A
project-B
project-C
```

the skill is available in all of them.

So:

```text
Your machine
│
├── project-A ── has access to /teach
├── project-B ── has access to /teach
└── project-C ── has access to /teach
```

It is **your personal workflow**.

---

### 2. Project-level skill

Location:

```text
my-project/
└── .agents/
    └── skills/
        └── database-migrations/
            └── SKILL.md
```

This means:

> **"Anyone working on THIS project must follow this workflow."**

For example, your project might require:

```text
1. Create migration
2. Run migration validation
3. Never modify an existing migration
4. Update migration documentation
5. Run tests
```

That knowledge is specific to the repository.

So you put it here:

```text
my-project/
└── .agents/
    └── skills/
        └── database-migrations/
```

Then commit it:

```bash
git add .agents/skills/
git commit
```

Your teammate clones the repo:

```text
git clone ...
```

and automatically gets the skill.

---

## The easiest rule to remember

Ask:

> **"Who needs this knowledge?"**

### If the answer is **me**

Use:

```text
~/.agents/skills/
```

Examples:

* My preferred code-review style
* My `/teach` workflow
* My personal documentation workflow
* My personal debugging shortcut

### If the answer is **everyone working on this repo**

Use:

```text
.agents/skills/
```

Examples:

* How database migrations work **in this repo**
* How deployments work
* How to write tests for this codebase
* How this project's API conventions work
* How to modify the project's architecture safely

---

## Why project-level skills are powerful

Imagine you have:

```text
.agents/skills/database-migrations/SKILL.md
```

and it says:

```text
When modifying the database:

1. Never edit an already-applied migration.
2. Create a new migration.
3. Run migration tests.
4. Update schema documentation.
5. Verify rollback behavior.
```

This becomes part of the **project's institutional knowledge**.

Six months later:

```text
Developer A ──┐
Developer B ──┼──> same skill
Developer C ──┘
                  ↓
              same workflow
```

And when the migration process changes, you update:

```text
SKILL.md
```

and commit it.

Everyone gets the updated workflow.

---

# One important connection to your previous question

You were asking earlier about:

> **Instruction vs Skill**

This is where the distinction becomes clearer.

Think of three layers:

```text
INSTRUCTION
    ↓
"Always follow these rules"
    ↓
SKILL
    ↓
"Here is a reusable workflow for doing X"
    ↓
PROJECT
    ↓
"Here is the workflow specifically required by THIS repository"
```

For example:

### Instruction

```text
Always write production-quality code.
```

Very broad.

### Skill

```text
When asked to review code:
1. Inspect the implementation.
2. Check edge cases.
3. Check tests.
4. Explain problems.
5. Suggest fixes.
```

Reusable workflow.

### Project skill

```text
When modifying database migrations in THIS repository:
1. Check docs/database-migrations.md
2. Never edit applied migrations
3. Use our migration CLI
4. Run migration tests
5. Update schema docs
```

Now the knowledge is **specific to the repository**.

---

## And this explains the "pointer" lesson you asked about

Earlier you saw the pattern:

```text
AGENTS.md
     │
     │ pointer
     ↓
docs/database-migrations.md
```

That's another important idea:

**Don't put huge amounts of detailed project knowledge into the always-loaded instruction file.**

Instead:

```text
AGENTS.md
   │
   └── "For database migration procedures,
        read docs/database-migrations.md"
                              │
                              ↓
                    detailed knowledge
```

A skill can similarly package a reusable procedure:

```text
.agents/skills/
└── database-migrations/
    └── SKILL.md
```

So you're learning a broader pattern:

```text
Instructions → steer the agent
Skills       → package reusable workflows
Pointers     → tell the agent where deeper knowledge lives
User scope   → my workflow
Project scope→ team's/project's workflow
```

### Quiz answers

**Q1:** Database migrations required by the repo + teammates need it → **Project skill**

```text
.agents/skills/database-migrations/
```

**Q2:** Your personal phrasing shortcut that you want everywhere → **User skill**

```text
~/.agents/skills/
```

The **one-line mental model** is:

> **User skill = "how I work." Project skill = "how we work on this project."**
