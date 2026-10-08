Yes — **your diagram is the correct flow**, and I need to correct my previous answer.


The flow in your image maps almost exactly to this:

```text
💡 IDEA
   │
   ▼
🔥 /grill-me
   │
   │ clarify decisions
   ▼
📋 /to-spec
   │
   │ create the destination/spec
   ▼
🎫 /to-tickets
   │
   │ break spec into tickets
   │ + define blocking relationships
   ▼
🔨 /implement
   │
   │ implement tickets
   ▼
🔍 /code-review
```

### So specifically, your question was:

> "Given the spec, is there a Matt skill that helps derive the tickets?"

**Yes: `/to-tickets`.**

Matt describes it as:

> **"Split a spec into small tickets an agent can build."** ([aihero.dev][2])

And the newer implementation of `/to-tickets` does something particularly important: it creates **tracer-bullet vertical slices** and declares their **blocking edges**. ([aihero.dev][1])

For example:

```text
SPEC
│
│ User can upload profile picture
│
├── Ticket A
│   Create image upload API
│
├── Ticket B
│   Store uploaded image
│   BLOCKED BY → A
│
├── Ticket C
│   Display profile image
│   BLOCKED BY → B
│
└── Ticket D
    Add upload error handling
    BLOCKED BY → A
```

So **`/to-tickets` isn't just "make a TODO list."**

It is trying to answer:

> **"What are the smallest meaningful pieces of implementation work, and in what order/dependency relationship can agents execute them?"**

---

## The five stages in your image

### 1. `/grill-me`

You have an idea:

> "I want users to be able to upload profile pictures."

The agent interviews you:

```text
What file types?
Maximum size?
Where are images stored?
Can users replace them?
What happens if upload fails?
Who can see the image?
```

You resolve the design decisions.

**Output:** shared understanding.

---

### 2. `/to-spec`

Now you say:

> `/to-spec`

It takes that conversation and turns it into a **spec**.

The spec is the **destination**.

```text
SPEC

Feature: Profile Picture Upload

User stories:
- User can upload a profile picture.
- User can replace it.
- User sees an error for invalid files.

Technical notes:
- Images stored in S3.
- Maximum size 5 MB.
- JPEG/PNG supported.
...
```

Matt explicitly describes `/to-spec` as turning an agreed conversation into a written specification. ([aihero.dev][3])

---

### 3. `/to-tickets`

This is the part you were looking for.

It reads:

```text
SPEC
   ↓
```

and produces:

```text
Ticket 1
Create image storage abstraction

Ticket 2
Implement upload endpoint
BLOCKED BY Ticket 1

Ticket 3
Implement profile image retrieval
BLOCKED BY Ticket 1

Ticket 4
Build profile image UI
BLOCKED BY Ticket 2 + 3
```

Now you have **agent-sized units of implementation work**.

---

### 4. `/implement`

The agent takes those tickets and actually builds them.

Matt's current flow also has `/implement-spec`, which can build an entire spec using parallel subagents, but the normal `/implement` flow works ticket-by-ticket. ([aihero.dev][4])

So:

```text
Ticket 1 → implement → done
Ticket 2 → implement → done
Ticket 3 → implement → done
Ticket 4 → implement → done
```

The blocking relationships determine what can happen in parallel.

---

### 5. Review

After implementation:

```text
Code
 ↓
/code-review
 ↓
Does implementation actually satisfy the spec?
```

Then potentially:

```text
/code-review
     ↓
problems found
     ↓
new/follow-up ticket
     ↓
/implement
```

---

# The key relationship

This is probably the mental model you were looking for:

```text
             HUMAN + AGENT
                  │
                  ▼
             /grill-me
                  │
          "What do we want?"
                  │
                  ▼
              /to-spec
                  │
          "What does done mean?"
                  │
                  ▼
            /to-tickets
                  │
       "What work gets us there?"
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
     Ticket A  Ticket B  Ticket C
        │         │         │
        └──── dependencies ─┘
                  │
                  ▼
              /implement
                  │
                  ▼
             /code-review
```

And there is another skill worth knowing about: **`/wayfinder`**.

That's **not** the normal spec → ticket step. It's for cases where the project is so large or unclear that you can't even determine the route in one session. It creates a map of **decision tickets**, resolves those decisions, and then hands off to `/to-spec` → `/to-tickets`. ([aihero.dev][5])

So the complete current Matt ecosystem is roughly:

```text
Small/normal feature:

/grill-with-docs
      ↓
   /to-spec
      ↓
 /to-tickets
      ↓
 /implement
      ↓
 /code-review
      ↓
   /retro
```

For something **huge and unclear**:

```text
/wayfinder
    ↓
resolve decisions
    ↓
/to-spec
    ↓
/to-tickets
    ↓
/implement
    ↓
/code-review
```

And `/ask-matt` exists as a **router** if you're unsure which skill/flow you should use. ([aihero.dev][6])

So yes: **the yellow "Tickets" box in your image is specifically `/to-tickets`.**

[1]: https://www.aihero.dev/skills/skills-changelog-v1-1-wayfinder-to-spec-to-tickets-grilling-improvements?utm_source=chatgpt.com "v1.1: /wayfinder, /to-spec, /to-tickets, /grilling improvements, and much more"
[2]: https://www.aihero.dev/skills-catalog?utm_source=chatgpt.com "AI Skills for Real Engineers"
[3]: https://www.aihero.dev/5-agent-skills-i-use-every-day?utm_source=chatgpt.com "5 Agent Skills I Use Every Day"
[4]: https://www.aihero.dev/skills-implement?utm_source=chatgpt.com "The /implement Skill"
[5]: https://www.aihero.dev/skills-wayfinder?utm_source=chatgpt.com "The /wayfinder Skill"
[6]: https://www.aihero.dev/skills-ask-matt?utm_source=chatgpt.com "The /ask-matt Skill"
