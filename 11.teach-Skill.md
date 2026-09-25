Sure — here’s a **copy-friendly summary** you can paste directly into your notes or `.md` file:

````markdown
# /teach Skill — Summary

The `/teach` skill helps you understand an unfamiliar codebase by analyzing the repository and creating a personalized learning path based on your existing experience.

## 1. Create a Fresh Workspace

Exit the current agent session and create a new directory next to the course repository:

```bash
mkdir ../ai-coding-learning
````

Open this new directory in your code editor.

## 2. Install the `/teach` Skill

From the new directory, run:

```bash
npx skills@latest add mattpocock/skills
```

During installation:

1. Select only the `teach` skill.
2. Choose the coding agent you use, such as Claude Code.
3. Install it for the **current directory only**.
4. Choose **symlink** when asked for the installation method.
5. Confirm the installation.

You should then see:

```text
.claude/
└── skills/
    └── teach/
```

## 3. Start the Agent

Clear the terminal:

```bash
clear
```

Start the agent:

```bash
claude
```

## 4. Run `/teach`

Use:

```text
/teach Teach me about this repo: ../ai-coding-crash-course

Describe your actual experience honestly.

For example:

I've done some vibe coding before.
I know the basics of TypeScript.
I've never worked with React before.
I'm not very clear about client vs server.
I don't understand the database layer very well.
```

The skill will inspect the repository and create a learning plan tailored to your knowledge level.

## 5. What `/teach` Does

The basic workflow is:

Repository
↓
Analyze codebase
↓
Understand architecture
↓
Identify libraries and patterns
↓
Understand data flow
↓
Compare with your existing knowledge
↓
Create personalized learning plan
↓
Teach the repository progressively

## 6. What You Should Ask It To Teach

A good `/teach` prompt should ask for:

* High-level architecture
* Application entry points
* Major components
* Client vs server responsibilities
* Database layer
* Request/data flow
* Important libraries
* Important files
* Design patterns
* Dependencies between components
* How the pieces work together

Most importantly:

> Teach me the actual repository, not generic programming concepts.

For unfamiliar concepts, ask the agent to explain:

1. What it is
2. Why it exists
3. Where it appears in the repository
4. How it interacts with other components
5. A concrete example from the code

## 7. Recommended Prompt

For someone with existing software-engineering experience:

```text
/teach Teach me about this repo: ../ai-coding-crash-course

My background:
- I have software engineering experience.
- I understand Java and Spring Boot reasonably well.
- I understand programming, APIs, databases, SQL, REST, and backend architecture.
- I have some experience with Python and TypeScript.
- I have very little practical React experience.
- I understand client-server concepts at a high level, but I want to understand how they are implemented in this repository.
- I am learning AI coding agents and agent skills, so I also want to understand how the repository is structured from a codebase-navigation perspective.

Don't assume I am a beginner in programming.

Teach me the repository rather than teaching me generic programming.

Start by creating a mental model of the entire repository:

1. What is the application?
2. What are the major components?
3. Where is the entry point?
4. How does a user action/request flow through the system?
5. Which parts run on the client?
6. Which parts run on the server?
7. How does data move between them?
8. Where is the database accessed?
9. What are the important libraries/frameworks and why are they used?
10. Which files should I read first and why?

For every important concept, connect it to actual files and code in the repository.

Don't dump the entire codebase on me.
Teach progressively.

Before explaining implementation details, give me a high-level architecture and mental model.

Then teach me one subsystem at a time.

When introducing something unfamiliar, explain:
- what it is
- why it exists
- where it appears in this repository
- how it interacts with other components
- a small concrete example from the code

Give me small exercises or questions periodically to check whether I actually understand the architecture.

My goal is not merely to understand what the code does.

I want to eventually be able to navigate the repository independently and make changes safely.
```

## Core Idea

The purpose of `/teach` is not:

> "Explain every file in this repository."

It is:

> **"Build a mental model of this codebase and teach me how to navigate and reason about it independently."**

```
```
