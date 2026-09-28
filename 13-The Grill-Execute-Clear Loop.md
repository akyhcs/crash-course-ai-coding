This lesson is teaching a **workflow for building software with an AI coding agent**:

> **Grill → Execute → Clear**

Instead of saying *“build comments”* and immediately letting the agent code, you first force the agent to discover the requirements and hidden decisions.

### 1. Grill 🧠

You start with a vague requirement:

> “I want to add comments to lessons so students can ask questions and discuss.”

The `/grill-me` skill then **interviews you**.

For example:

```text
Who can comment?
├── Students
├── Instructors
└── Both

Can comments be edited?
├── Yes
│   ├── Anytime
│   └── Within 15 minutes
└── No

Can comments be deleted?
├── Author
├── Instructor
└── Nobody

Comment structure?
├── Flat
└── Threaded
    └── How many levels?

Moderation?
├── None
├── Report comments
└── Instructor moderation

Notifications?
├── None
├── Reply notifications
└── Mention notifications
```

This is what the lesson means by a **design tree**.

---

### 2. What is the "frontier"?

This is the most important concept.

The **frontier** is basically:

> **The set of decisions that you can answer right now because all the decisions they depend on are already known.**

Imagine:

```text
                    Lesson Comments
                          │
                    Who can comment?
                          │
              ┌───────────┴───────────┐
              │                       │
           Students               Instructors
              │
       Can students edit?
              │
        ┌─────┴─────┐
       Yes           No
       │
   When can they edit?
```

You can't meaningfully ask:

> "Can instructors moderate replies?"

until you've established things like:

> "Are instructors allowed to participate?"

So the skill doesn't dump 50 random questions on you.

It asks the questions whose **prerequisites are already settled**.

That's the **frontier**.

---

### 3. The frontier changes as you answer

Suppose the first round asks:

```text
Q1. Who can comment?
Q2. Flat or threaded?
Q3. Can users edit?
Q4. Can users delete?
```

You answer:

```text
Q1 → Students + instructors
Q2 → Threaded
Q3 → Yes
Q4 → Yes, their own comments
```

Now new questions become available:

```text
Threaded
   ↓
How deeply can replies nest?
   ↓
Can instructors pin replies?
   ↓
Can users reply to deleted comments?
```

And:

```text
Edit allowed
   ↓
Anytime or time-limited?
   ↓
Should edit history be visible?
```

And:

```text
Delete own comments
   ↓
Hard delete or soft delete?
   ↓
What happens to replies?
```

So the interview **expands the tree organically**.

---

# 4. Why not just give the agent a detailed prompt?

This is the key lesson.

You could write:

```text
Build a threaded comment system.

Students and instructors can comment.
Users can edit their comments.
Users can delete their comments.
Add notifications.
Add moderation.
...
```

But you probably forgot something.

For example:

```text
User deletes parent comment
        ↓
What happens to replies?
```

Or:

```text
Student edits comment
        ↓
Do we preserve edit history?
```

Or:

```text
Instructor deletes inappropriate comment
        ↓
Should students see "comment removed"?
```

Or:

```text
Student leaves course
        ↓
Do their old comments disappear?
```

These are **hidden requirements**.

The purpose of `/grill-me` is to expose them **before implementation**.

---

# 5. Then Execute ⚙️

Once the design tree has no unanswered decisions:

```text
        GRILL
          ↓
   Shared understanding
          ↓
       EXECUTE
          ↓
      Write code
          ↓
       Run tests
          ↓
   Verify in browser
```

Now the agent has much less room to invent requirements.

It knows:

```text
Who?
What?
When?
Where?
Permissions?
Data model?
UI behavior?
Edge cases?
Errors?
Notifications?
Moderation?
```

So implementation becomes:

> **Executing an agreed design**, rather than discovering the design while coding.

---

# 6. Then Clear 🧹

After finishing:

```text
Feature A
   ↓
Grill
   ↓
Execute
   ↓
Clear
   ↓
Feature B
   ↓
Grill
   ↓
Execute
   ↓
Clear
```

**Clear** means you don't carry assumptions from the previous feature into the next one.

You essentially reset your mental state:

> “That feature is done. Now let's understand the next feature independently.”

---

# The entire lesson in one diagram

```text
                    USER REQUEST
                         │
                         ▼
                "Add lesson comments"
                         │
                         ▼
                    /grill-me
                         │
                         ▼
                 ┌───────────────┐
                 │  DESIGN TREE  │
                 └───────────────┘
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
           Who?       Behavior?   Permissions?
              │          │          │
              ▼          ▼          ▼
          Students    Threaded    Edit/Delete
          Instructors   Replies    Moderation
              │          │          │
              └──────────┼──────────┘
                         ▼
                     FRONTIER
                         │
                         ▼
                 Ask next questions
                         │
                         ▼
                   Answer them
                         │
                         ▼
                 Expand design tree
                         │
                         ▼
                Frontier becomes empty
                         │
                         ▼
              SHARED UNDERSTANDING
                         │
                         ▼
                      EXECUTE
                         │
                         ▼
                  Implement feature
                         │
                         ▼
                    Test / Verify
                         │
                         ▼
                      CLEAR
                         │
                         ▼
                  Next feature
```

### The fundamental idea

The lesson is really saying:

> **Don't use the coding agent to figure out what you want while it is already writing code. Use the agent first to discover and settle the requirements, then use it to implement those requirements.**

This connects directly to the **Plan Mode problem** you shared earlier: the problem wasn't necessarily that the agent couldn't code—it was that it **started executing before alignment was established**.

`/grill-me` is a mechanism for moving that alignment step **before implementation**.
