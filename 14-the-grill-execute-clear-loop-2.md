The file contains the **solution/walkthrough for the “Grill → Execute → Clear” workflow**. 

### In simple terms

The main idea is:

```text
Loose Requirement
       ↓
     GRILL
       ↓
Clarify decisions
Find contradictions
Find edge cases
Find security issues
       ↓
    EXECUTE
       ↓
Build the feature
       ↓
     CLEAR
       ↓
Start next task with fresh context
```

### 1. Grill = understand before coding

Instead of telling the agent:

> "Build a comments feature"

and letting it immediately code, you use `/grill-me`.

The agent asks **strategic questions first**:

* What is the feature actually for?
* Who can use it?
* What happens when there are zero comments?
* Should comments be threaded?
* How should instructors find unanswered questions?
* What permissions should users have?

The important point is that these aren't merely coding questions. They are **product and design decisions**. 

---

### 2. You answer in one larger response

You don't have to answer:

```text
Q1 → answer
Q2 → answer
Q3 → answer
```

Instead, you give the agent a paragraph explaining your thinking. The agent then uses those answers to continue the discussion. 

So the interaction becomes:

```text
Human
  ↓
"I think comments are mainly for Q&A..."
  ↓
Agent understands
  ↓
Agent asks deeper questions
  ↓
Human clarifies
```

---

### 3. Grill is not just asking questions — it challenges your decisions

This is one of the most important parts.

The agent discovered a contradiction:

> You wanted a **flat comment list**, but also wanted an **unanswered-question queue**.

The problem:

```text
Comment A
Comment B
Comment C
```

If everything is flat, how does the system know that:

```text
Comment A = unanswered
```

versus:

```text
Comment A
   └── Instructor reply
```

The agent therefore presents alternatives:

```text
Option 1 → Allow one-level replies
Option 2 → Keep flat + define unanswered at lesson level
Option 3 → Keep flat + add explicit seen-state
```

The user chooses **one-level replies**. 

This is the real value of grilling:

> **The agent exposes contradictions before they become code.**

---

### 4. Grill can discover problems you didn't know existed

While the conversation was happening, the agent explored the codebase.

It found that:

```text
renderMarkdown()
      ↓
raw HTML
      ↓
no sanitization
```

That creates an XSS vulnerability.

A student could potentially put malicious HTML/JavaScript into a comment, which could execute when an instructor views it. 

So the requirement becomes:

```text
Student comment
      ↓
renderComment()
      ↓
restricted/sanitized Markdown
```

This is a great example of why **Grill ≠ simple clarification**.

It is also:

```text
Clarification
+
Contradiction detection
+
Edge-case discovery
+
Security discovery
```

---

### 5. Eventually the agent produces a spec

After several rounds, the agent has enough information to create a written specification.

But the interesting point is:

**The spec isn't where the thinking happens.**

The thinking happened during the grilling conversation.

The spec is essentially a record of the decisions that were already made. 

Think of it like:

```text
GRILL SESSION
     ↓
Decisions
     ↓
Constraints
     ↓
Edge cases
     ↓
Security requirements
     ↓
SPEC
```

---

### 6. Then Execute — without clearing the context

Once the user says:

> "Okay, let's start building."

the agent starts implementation.

Crucially, **you don't start a new session**.

The agent still has all the decisions from the Grill phase:

```text
User requirements
        +
Agent exploration
        +
Decisions
        +
Contradictions resolved
        +
Security requirements
        ↓
     EXECUTE
```

So the agent doesn't have to rediscover everything. 

---

### 7. The result is more than just code

The implementation produced:

* `comments` database table
* access-control helper
* sanitized `renderComment()`
* `commentService`
* comment UI
* empty state
* instructor questions queue
* seed data
* tests
* type checking

The important thing is that these aren't random implementation decisions.

They came from the earlier **Grill phase**. 

---

## Why Grill is powerful

The document gives a very useful comparison.

Without grilling, you might have ended up with:

```text
❌ No empty state
❌ No instructor question queue
❌ XSS vulnerability
❌ Flat comments + complicated seen-state
❌ Unsafe HTML rendering
```

With grilling:

```text
Requirement
     ↓
Question
     ↓
Clarification
     ↓
Contradiction
     ↓
Decision
     ↓
Security discovery
     ↓
Specification
     ↓
Implementation
```

So **the coding itself becomes easier because the difficult thinking happened beforehand**. 

---

# The important concept: Grill → Execute → Clear

The whole lesson reduces to three phases:

### 🧠 1. GRILL

```text
"What exactly are we building?"
```

Agent asks questions and challenges assumptions.

### 🔨 2. EXECUTE

```text
"Now build what we agreed on."
```

The agent implements using the still-warm context.

### 🧹 3. CLEAR

```text
"Feature is finished → commit → start fresh."
```

Once the feature is complete, clear the context before beginning an unrelated task. 

---

## One important distinction from the previous "Plan Mode" lesson

You can think about the difference like this:

```text
Traditional agent
─────────────────────────────
Prompt
  ↓
Explore
  ↓
Plan
  ↓
Code
```

The **Grill approach** is:

```text
Prompt
  ↓
GRILL
  ├── Ask "why?"
  ├── Find ambiguity
  ├── Find contradictions
  ├── Find edge cases
  ├── Find security problems
  └── Confirm decisions
        ↓
      SPEC
        ↓
    EXECUTE
        ↓
      CODE
        ↓
      CLEAR
```

The biggest shift is:

> **Don't let the agent treat an ambiguous requirement as permission to make important product decisions silently.**

Instead, make those decisions explicit **before implementation**.

And that's why the name **“Grill”** is appropriate: the agent keeps putting pressure on the requirement until there are fewer hidden assumptions.
