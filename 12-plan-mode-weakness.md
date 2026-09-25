### Copy-friendly summary

````md
# Why Plan Mode Can Still Fail

## 1. The problem with normal agent mode

A typical coding agent may:

1. Receive the feature request
2. Explore the codebase
3. Immediately start implementing

The problem is the missing **alignment step**.

The agent may understand the request differently from the human, yet it starts building immediately.

This creates a dangerous failure mode:

> The agent may build the wrong thing very efficiently.

---

## 2. What Plan Mode is supposed to do

Plan mode is intended to create a buffer between:

```text
Exploration → Planning → Human Review → Implementation
````

Instead of immediately writing code, the agent:

1. Explores the codebase
2. Creates a plan
3. Shows the plan to the human
4. Human reviews/modifies it
5. Agent implements it

This sounds much safer.

---

## 3. The problem with Plan Mode

The issue is that the agent often **rushes to create the plan itself**.

For example, instead of simply discussing the problem, it may produce:

```text
Database table
    ↓
Service functions
    ↓
API routes
    ↓
UI components
    ↓
Tests
```

The plan can contain very specific implementation decisions:

* Database schema
* Service names
* Function names
* Components
* Routes
* Testing strategy

The problem isn't that the plan is too vague.

The problem is that it is **too specific too early**.

The agent has already made many design decisions before establishing a shared understanding with the human.

So:

```text
Normal mode:

Request
  ↓
Explore
  ↓
Implement
```

Plan mode:

```text
Request
  ↓
Explore
  ↓
Create implementation plan
  ↓
Implement
```

Both approaches can skip the important step:

```text
        ALIGNMENT
            ↓
"What exactly are we trying to build?"
```

---

## 4. The "asset rush"

The key idea is:

> Agents have a tendency to rush toward producing an asset.

In normal mode:

```text
Asset = Code
```

In plan mode:

```text
Asset = Plan
```

So Plan Mode may not actually solve the underlying problem.

It changes:

```text
"I'll build the thing."
```

into:

```text
"I'll create a detailed document describing how I'll build the thing."
```

But the agent can still be prematurely committing to decisions.

---

## 5. Sycophancy

The author describes this behavior as a form of **sycophancy**.

Here, sycophancy means the agent tends to accept the user's requested direction and move toward producing the requested result rather than stopping to challenge assumptions or establish shared understanding.

Example:

```text
Human:
"I want a course rating system."

Agent:
"Sure!"

    ↓

Explore codebase

    ↓

"Here's the database schema,
services, routes, components,
and tests I'll implement."
```

What is missing?

```text
"Before deciding how to implement this,
let's make sure we agree on what
the feature should actually be."
```

---

## 6. The deeper problem: no shared design concept

The important concept from the lesson is the **design concept**.

A design concept is not necessarily:

* code
* a document
* a specification
* a plan

Instead, it is the **shared mental model of what is being built**.

Imagine two developers discussing a feature:

```text
Developer A: "I think users should rate courses
              from the course page."

Developer B: "Yes, and ratings should only be
              allowed after enrollment."

Developer A: "And the list page should show the
              average rating."

Developer B: "Right, but courses with no ratings
              shouldn't display 0 stars."
```

After this conversation, both people have a much clearer picture of the feature.

That shared understanding is the:

```text
DESIGN CONCEPT
```

It can exist before anything is written down.

---

## 7. Design concept vs Plan

Important distinction:

```text
Design Concept
    ↓
Shared understanding
"What are we actually building?"
```

versus

```text
Plan
    ↓
Implementation strategy
"How are we going to build it?"
```

The lesson argues that agents often jump to:

```text
Problem
  ↓
How should I implement it?
```

before sufficiently establishing:

```text
Problem
  ↓
What exactly are we trying to build?
  ↓
Do we agree?
  ↓
How should we implement it?
```

---

## 8. The ideal workflow

The lesson is moving toward a different workflow:

```text
User Request
     ↓
Exploration
     ↓
Understand the problem
     ↓
Discuss / clarify / align
     ↓
Shared Design Concept
     ↓
Implementation Plan
     ↓
Human Review
     ↓
Implementation
```

The important new step is:

```text
             ALIGNMENT
                 ↓
       Shared Design Concept
```

The goal isn't simply to make the agent produce a better plan.

The goal is to prevent the agent from **committing to implementation decisions before the human and agent share the same understanding of the problem**.

---

# Core takeaway

> Plan Mode solves "don't implement immediately," but it does not necessarily solve "don't decide immediately."

The agent can stop coding but still rush into designing.

So the deeper problem is not:

```text
Code too early
```

It is:

```text
Commitment too early
```

The desired behavior is:

```text
Explore → Understand → Align → Decide → Implement
```

rather than:

```text
Explore → Decide → Implement
```

## Quiz answers

1. **Asset rush**
   The plan itself has become the implementation asset. The agent has already made detailed decisions before shared understanding was established.

2. **Sycophancy**
   The agent accepts the requested direction and moves toward producing the requested thing instead of stopping to establish alignment.

3. **Design concept**
   The shared mental model that emerges through discussion is the design concept. It is not necessarily a written artifact.

```

**One sentence to remember:**  
**Plan Mode slows down coding, but it doesn't necessarily slow down decision-making.**
```
