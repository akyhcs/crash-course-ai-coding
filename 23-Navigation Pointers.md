The file is about **Navigation Pointers** in agentic coding. The key idea is:

> **A navigation pointer tells the agent where to look; it does not tell the agent what to do.** 

### In simple terms

Think of an agent working in a codebase like a person navigating a city:

* **Without a pointer:** it searches directories → opens files → follows references → eventually finds the important file.
* **With a pointer:** you give it the address directly.

For example:

```md
# Navigation

- `scripts/seed.ts` - the definition of the database's starting data,
  rebuilt from scratch on every `npm run db:seed`. Every schema change
  lands here too.
```

Now, when the agent changes the database schema, it knows:

**Schema change → also check `scripts/seed.ts`.**

The lesson specifically says this prevents the agent from missing the seed script during migrations. 

---

## The important distinction: Pointer vs Instruction vs Skill

This connects directly to the confusion you've been having in the previous lessons.

| Mechanism              | Main question it answers                                | Example                                                   |
| ---------------------- | ------------------------------------------------------- | --------------------------------------------------------- |
| **Instruction**        | **What should the agent do?**                           | "Whenever you modify the schema, update the seed script." |
| **Navigation pointer** | **Where should the agent look?**                        | "`scripts/seed.ts` contains the seed data."               |
| **Skill**              | **How should the agent perform a particular workflow?** | `/database-migration` → follow migration procedure        |
| **@-mention**          | **Where should it look for this one task?**             | `@scripts/seed.ts`                                        |

The source explicitly describes a navigation pointer as telling the agent **where to look rather than what to do**. 

### The really useful mental model

```text
Instruction
     ↓
WHAT should happen?
     ↓
"Schema changes must also update seed data."


Navigation Pointer
     ↓
WHERE is the relevant thing?
     ↓
"scripts/seed.ts"


Skill
     ↓
HOW should I perform the workflow?
     ↓
"Follow these migration steps..."


@-mention
     ↓
WHERE for THIS ONE TURN?
     ↓
"Read @scripts/seed.ts"
```

---

## Why not just put everything in `AGENTS.md`?

Because that increases the **context cost**.

The lesson gives the example of simply pasting the entire seed script into `AGENTS.md`. That would put all of that code into context for every session, even when the agent doesn't need it. A three-line pointer gives the agent the location without loading the entire file. 

So:

```text
❌ AGENTS.md
   └── paste 500 lines of seed.ts

✅ AGENTS.md
   └── "scripts/seed.ts contains the database seed data"
```

The second approach is basically **lazy loading**:

```text
AGENTS.md
   ↓
"I know where seed.ts is"
   ↓
Only open seed.ts when needed
```

---

## The subtle part: Navigation pointers can also describe hidden dependencies

This is probably the most valuable part of this lesson.

Suppose your repository has:

```text
skills/
  engineering/
  productivity/
  misc/

.claude-plugin/
  plugin.json

README.md
```

An agent looking at the filesystem may **not know** that:

```text
new skill
   ↓
engineering/
   ↓
must update plugin.json
   ↓
must update docs/
   ↓
must update README.md
```

Those relationships aren't necessarily visible from the folder structure.

So `AGENTS.md` can contain a pointer/rule such as:

```md
Every skill in `engineering/` or `productivity/` must have a
reference in README.md and an entry in plugin.json.
```

The lesson calls out these non-obvious dependencies explicitly. 

So navigation pointers aren't only:

```text
"Look at this file."
```

They can also help expose:

```text
"These important files are related."
```

---

# One important warning: stale pointers

Suppose you have:

```md
- `scripts/seed.ts` - database seed script
```

Then you refactor:

```text
scripts/seed.ts
        ↓
scripts/database/seed.ts
```

but forget to update `AGENTS.md`.

Now you have:

```text
AGENTS.md
   ↓
scripts/seed.ts ❌
```

The build can still be completely green.

The agent may trust the pointer, look for the old path, discover that it doesn't exist, and waste context figuring out what happened. 

Therefore:

> **Whenever you change project structure, check your navigation pointers.**

---

# And finally: `@mention` is basically a temporary pointer

This is another important connection.

The source gives:

| Scope         | Mechanism                      |
| ------------- | ------------------------------ |
| Every session | `AGENTS.md` navigation pointer |
| One turn      | `@mention`                     |



So imagine you need:

```text
scripts/special-migration.ts
```

only for today's task.

Don't permanently add:

```md
- scripts/special-migration.ts
```

to `AGENTS.md`.

Instead:

```text
@ scripts/special-migration.ts
```

That tells the agent:

> "For **this turn**, go directly here."

Whereas `AGENTS.md` says:

> "For **future tasks too**, this location is important."

---

## The whole lesson in one picture

```text
                 AGENT
                   │
                   ▼
          ┌─────────────────┐
          │ What do I need? │
          └────────┬────────┘
                   │
        ┌──────────┴──────────┐
        │                     │
        ▼                     ▼
   "WHAT?"                 "WHERE?"
   Instruction          Navigation Pointer
        │                     │
        │                     ▼
        │              scripts/seed.ts
        │
        ▼
   "WHAT should I do?"
        │
        ▼
   Update seed script


        HOW?
         │
         ▼
       Skill
         │
         ▼
   Follow migration workflow
```

**The key takeaway:** don't make `AGENTS.md` a giant knowledge dump. Put **small instructions and pointers** there, so the agent can quickly navigate to the detailed information only when it needs it. 
