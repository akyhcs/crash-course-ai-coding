This lesson is teaching a very important **context-engineering pattern: “Doc + Pointer.”**

### 1. The core problem

There is a hidden rule in this project:

> **Whenever the database schema changes, `scripts/seed.ts` must also be updated.**

For example:

```text
Schema change
     ↓
Generate migration
     ↓
Update seed.ts
     ↓
Reseed database
     ↓
Typecheck
```

The agent **cannot reliably discover this rule from the code itself** because the relationship between the schema and the seed process is partly a project-specific convention.

So you need to provide this knowledge to the agent.

---

### 2. The naive solution: put everything in `AGENTS.md`

You could put all migration instructions directly into `AGENTS.md`:

```text
# Database migrations

Whenever you change the schema:

1. Update schema
2. Generate migration
3. Update scripts/seed.ts
4. Reseed
5. Typecheck
...
```

The advantage:

**The agent always has the information.**

But there is a cost.

Imagine the agent is working on:

```text
"Change the button color on the homepage."
```

It still receives:

```text
Database migration instructions...
Database migration instructions...
Database migration instructions...
```

even though they are irrelevant.

That consumes:

* **Input tokens**
* **Context window**
* **Attention budget**
* **Cognitive load**

And if you eventually have 10 different project-specific instruction sets, `AGENTS.md` becomes huge.

---

# 3. The better solution: Doc + Pointer

Instead, separate the **instruction** from the **trigger**.

### `AGENTS.md`

Keep only:

```text
When making a database schema change, consult
`docs/database-migrations.md` for the exact steps.
```

And move the actual instructions into:

```text
docs/database-migrations.md
```

So now you have:

```text
                 AGENTS.md
                     │
                     │ pointer
                     ↓
       docs/database-migrations.md
                     │
                     ↓
          detailed migration steps
```

This is the **doc-plus-pointer pattern**.

---

# 4. Why is this better?

There are two different concepts:

### Context availability

The agent needs to know:

> "There is a special rule for database changes."

That tiny piece of information stays in `AGENTS.md`.

### Context loading

The agent only needs the **full instructions** when it is actually changing the database.

So:

```text
Normal UI task
    ↓
AGENTS.md
    ↓
No migration needed
    ↓
Stop
```

But:

```text
"Add a column to users"
    ↓
AGENTS.md
    ↓
Agent recognizes database schema change
    ↓
Follow pointer
    ↓
Read docs/database-migrations.md
    ↓
Execute migration procedure
```

That's much more efficient.

---

# 5. What makes a good pointer?

The lesson says a pointer needs **two things**.

### A. Stable path

The agent needs to know exactly where the information lives.

Good:

```text
docs/database-migrations.md
```

Bad:

```text
See the migration documentation somewhere in the repo.
```

The first is deterministic.

---

### B. Clear trigger

The agent needs to know **when** it should follow the pointer.

Weak:

```text
See docs/database-migrations.md.
```

The agent might think:

> "Why should I read this?"

Better:

```text
When making a database schema change, consult
`docs/database-migrations.md` for the exact steps.
```

Now the pointer contains:

```text
WHEN → database schema changes
WHERE → docs/database-migrations.md
WHY → exact migration steps
```

That's a very powerful pattern.

---

# 6. The deeper idea: reachable ≠ loaded

This is probably the **most important concept in this lesson**.

You don't need every piece of project knowledge to be loaded into the agent's context all the time.

You need important knowledge to be **reachable when relevant**.

Think of it like a filesystem:

```text
AGENTS.md
│
├── database rules → docs/database-migrations.md
├── API rules      → docs/api.md
├── testing rules  → docs/testing.md
└── deployment     → docs/deployment.md
```

You don't dump all four documents into every request.

Instead:

```text
Task
 ↓
Relevant pointer
 ↓
Relevant document
 ↓
Relevant instructions
```

This is **context routing**.

---

# 7. Why the database example is particularly important

The lesson gives another subtle point:

> The database is disposable.

The agent needs to know that:

```text
data.db
```

contains only seed data and can safely be:

```text
deleted
   ↓
recreated
   ↓
reseeded
```

That information may **not be derivable from the source code**.

This is an example of **human/project knowledge**.

There are two types of knowledge:

| Knowledge     | Example                                                         |
| ------------- | --------------------------------------------------------------- |
| Derivable     | "This function calls this API"                                  |
| Non-derivable | "Our database is disposable; delete/reseed it after migrations" |

Agents can inspect code to discover the first.

You need to explicitly provide the second.

---

# 8. What `/writing-for-agents` is doing

The lesson introduces another skill:

```text
/writing-for-agents
```

Its purpose is to help turn human knowledge into **agent-readable instructions**.

Instead of writing:

> "Whenever you change the database, remember to update the seed stuff because otherwise things can get weird."

you want something operational:

```text
# Database migrations

When changing the database schema:

1. Update the Drizzle schema.
2. Generate the migration.
3. Update `scripts/seed.ts` to match the new schema.
4. Reseed the database.
5. Run typecheck.
6. Verify the seeded data matches the schema.
```

Notice the difference:

**Human prose**

```text
Be careful with database changes.
```

vs.

**Agent-oriented instruction**

```text
When changing the database schema:
1. ...
2. ...
3. ...
```

The second is much easier for an agent to execute.

---

# 9. The complete architecture

After this lesson, your repository should conceptually look like:

```text
repo/
│
├── AGENTS.md
│
├── docs/
│   └── database-migrations.md
│
├── scripts/
│   └── seed.ts
│
└── database/
    └── schema.ts
```

### `AGENTS.md`

Small, always-loaded routing information:

```text
When making a database schema change, consult
`docs/database-migrations.md` for the exact steps.
```

### `docs/database-migrations.md`

Detailed instructions:

```text
# Database Migrations

...

## Steps

1. Change schema
2. Generate migration
3. Update seed.ts
4. Reseed
5. Typecheck
6. Verify
```

---

# 10. Then comes the test

The interesting part of the exercise is **not simply creating the document**.

You need to test whether the pointer actually works.

Ask an agent:

> Add a simple column to an existing database table.

Then observe its behavior.

Ideally:

```text
Agent sees AGENTS.md
       ↓
Recognizes database schema change
       ↓
Reads docs/database-migrations.md
       ↓
Updates schema
       ↓
Generates migration
       ↓
Updates seed.ts
       ↓
Runs seed
       ↓
Runs typecheck
```

If the agent does all of that, your pointer successfully routed it to the right knowledge.

---

# 11. Connection to what you learned earlier

This connects directly to your previous lessons about **context engineering, context triage, compaction, and steering**.

You can think about it like this:

```text
                    AGENT
                      │
              ┌───────┴───────┐
              │               │
          Context          Context
          already           pointer
          loaded              │
              │               ↓
              │        Relevant document
              │               │
              └───────┬───────┘
                      ↓
                  Reasoning
                      ↓
                    Action
```

The pointer is essentially a **routing mechanism for context**.

Instead of:

> "Here is everything you might possibly need."

you give the agent:

> "Here is how to find what you need when the relevant situation occurs."

---

## The key takeaway

**Don't put every instruction into `AGENTS.md`.**

Use:

> **Small always-loaded pointer + detailed conditionally-loaded document.**

Or, in one sentence:

> **You don't need every piece of knowledge in the agent's context; you need the right knowledge to be reachable at the right time.**

That's the main idea behind **Steering With a Pointer**.
