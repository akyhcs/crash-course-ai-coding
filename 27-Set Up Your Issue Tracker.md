This lesson is essentially teaching **how to make GitHub Issues the external “source of truth” for your AI coding agent**.

### The architecture

Think of the setup like this:

```text
Your local repository
│
├── AGENTS.md
│     └── "Issue tracker instructions are here"
│
└── docs/agents/issue-tracker.md
      └── Detailed GitHub Issue conventions
              │
              ▼
        GitHub Issues
              │
              ├── Specs / PRDs
              ├── Tickets
              ├── Bugs
              └── Tasks
```

The important idea is that **the spec/ticket itself does NOT live in your repository**.

Instead:

```text
Repository
   │
   │ pointer
   ▼
docs/agents/issue-tracker.md
   │
   │ tells agent how to use
   ▼
GitHub Issues
   │
   ├── Spec
   ├── Ticket
   └── Discussion/history
```

### Why not just put everything in `docs/`?

Because the course is trying to solve a bigger problem: **context management**.

If you keep adding things like:

```text
docs/
├── spec-1.md
├── spec-2.md
├── ticket-1.md
├── ticket-2.md
├── architecture.md
├── decisions.md
└── old-requirements.md
```

your repository gradually becomes full of historical/contextual information.

GitHub Issues gives you:

* a place for specs
* a place for tickets
* comments/discussion
* status
* labels
* history
* collaboration
* access from your coding agent

So the repository contains the **code and lightweight instructions**, while GitHub contains the **work/context around the code**.

---

## What does `/setup-matt-pocock-skills` actually do?

This is the key part.

The skill investigates your project and establishes:

> "Where should this agent look when I ask about issues/specs/tickets?"

It then creates something like:

### `AGENTS.md`

```markdown
## Agent skills

### Issue tracker

Issues and PRDs live as GitHub issues on <your-username>/<your-repo>,
managed with the `gh` CLI.

See `docs/agents/issue-tracker.md`.
```

Notice that `AGENTS.md` **doesn't contain all the GitHub instructions**.

It contains a **pointer**.

Then:

### `docs/agents/issue-tracker.md`

contains the detailed rules:

```text
How to:
- create an issue
- read an issue
- list issues
- comment
- label
- close
- use gh CLI
```

This is the **steering/pointer pattern** you were asking about in your previous lessons.

---

# The important distinction

You can think of the three pieces as:

```text
AGENTS.md
   ↓
"Where should I look?"
   ↓
docs/agents/issue-tracker.md
   ↓
"How should I behave?"
   ↓
GitHub Issues
   ↓
"What actual work/context exists?"
```

So:

| Thing              | Purpose                                                              |
| ------------------ | -------------------------------------------------------------------- |
| `AGENTS.md`        | **Pointer**                                                          |
| `issue-tracker.md` | **Instructions**                                                     |
| GitHub Issue       | **Actual work/context**                                              |
| Skill              | **Reusable procedure for setting things up / performing a workflow** |

That's why this lesson connects directly to the previous lessons you've been studying.

### In one sentence

**The skill sets up the instructions, `AGENTS.md` points the agent to those instructions, and GitHub Issues becomes the external source of truth for your specs and tickets.**

And the final test:

```text
You: "Add an issue to the issue tracker."

Agent
  ↓
Reads AGENTS.md
  ↓
Finds issue-tracker.md
  ↓
Learns GitHub is the issue tracker
  ↓
Runs `gh issue create`
  ↓
GitHub Issue created
```

That is the whole workflow this lesson is teaching.
