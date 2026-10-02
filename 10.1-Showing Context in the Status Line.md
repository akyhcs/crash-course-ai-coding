This lesson is about **making Claude Code’s context-window usage visible all the time**.

### What problem is it solving?

Claude Code has a limited **context window**. As your session gets longer, more of that context is consumed.

Normally, Claude Code doesn't continuously show you something like:

```text
186.2k (17.3%)
```

So you may not realize that your session is getting large.

The solution is to configure **`ccstatusline`**, which displays context usage in Claude Code's status bar.

---

## 1. What you'll see

After setup, your status line will look roughly like:

```text
186.2k (17.3%)
```

Meaning:

```text
186.2k       → tokens currently being used
17.3%        → percentage of context window consumed
```

So you can immediately see:

```text
Context usage
     ↓
186.2k (17.3%)
^^^^^   ^^^^^^
tokens  percentage
```

This is useful because **context usage should influence how you manage your Claude Code session**.

For example:

```text
10%   → plenty of room
40%   → normal
60%   → start paying attention
80%   → consider compaction/handoff
90%+  → session is getting very constrained
```

Those thresholds are conceptual; the important thing is to watch the trend rather than treat a particular percentage as a universal cutoff.

---

# 2. What is `ccstatusline`?

`ccstatusline` is a community tool that takes the session information Claude Code provides and turns it into a readable status line.

The flow is basically:

```text
Claude Code
    │
    │ session/context information
    ↓
ccstatusline
    │
    │ formats the information
    ↓
Claude Code status bar
    │
    ↓
186.2k (17.3%)
```

So you aren't manually calculating context usage.

---

# 3. First configuration

You create:

```text
~/.config/ccstatusline/
```

with:

```bash
mkdir -p ~/.config/ccstatusline
```

Then create:

```text
~/.config/ccstatusline/settings.json
```

The JSON you were given controls **how the status line looks**.

The important part is these widgets:

```json
{
  "type": "context-length"
}
```

and:

```json
{
  "type": "context-percentage"
}
```

### `context-length`

Displays the number of tokens currently being used.

For example:

```text
186.2k
```

### `context-percentage`

Displays the percentage:

```text
17.3%
```

---

# 4. Why `rawValue: true`?

Normally a widget might display something like:

```text
Context: 186.2k
```

But:

```json
"rawValue": true
```

essentially tells it:

> Give me just the value, without the descriptive label.

So instead of:

```text
Context: 186.2k
```

you get:

```text
186.2k
```

Cleaner for a status bar.

---

# 5. Why are there `custom-text` widgets?

You have:

```json
{
  "type": "custom-text",
  "customText": "("
}
```

and:

```json
{
  "type": "custom-text",
  "customText": ")"
}
```

These simply add:

```text
(
)
```

around the percentage.

So the components are:

```text
context-length
      ↓
   186.2k

custom-text
      ↓
     (

context-percentage
      ↓
    17.3%

custom-text
      ↓
     )
```

Together:

```text
186.2k (17.3%)
```

---

# 6. What does `merge: "no-padding"` do?

Without it, the widgets might introduce spacing:

```text
186.2k ( 17.3% )
```

With:

```json
"merge": "no-padding"
```

the pieces are glued together:

```text
(
17.3%
)
```

giving:

```text
(17.3%)
```

while:

```json
"defaultSeparator": " "
```

adds the desired space between the token count and parentheses:

```text
186.2k (17.3%)
       ↑
     space
```

---

# 7. Claude Code's `settings.json`

The second configuration is more important for integration.

You edit:

```text
~/.claude/settings.json
```

and add:

```json
{
  "statusLine": {
    "type": "command",
    "command": "npx ccstatusline@latest"
  }
}
```

Conceptually:

```text
Claude Code
     │
     │ sends session data
     ↓
npx ccstatusline@latest
     │
     │ formats data
     ↓
status line
```

Claude Code automatically pipes the relevant session information into the command.

---

# 8. Why `npx`?

This:

```bash
npx ccstatusline@latest
```

allows `npx` to execute the package without you necessarily installing it globally yourself.

The `@latest` means it requests the latest published version.

---

# 9. Why restart Claude Code?

The status-line configuration is loaded when Claude Code starts.

So after changing:

```text
~/.claude/settings.json
```

you should completely restart Claude Code.

Then you should see something similar to:

```text
186.2k (17.3%)
```

and it should change as your conversation grows.

---

# 10. The bigger lesson

This isn't really about pretty formatting.

It connects to the earlier topics you've been studying:

```text
Context Window
      ↓
Context Usage
      ↓
Status Line
      ↓
Continuous Awareness
      ↓
Better Session Decisions
```

Instead of discovering **too late** that your context is almost full, you continuously see its usage.

For example:

```text
Session starts

 25k (2%)
       ↓
 90k (9%)
       ↓
 250k (25%)
       ↓
 500k (50%)
       ↓
 700k (70%)
       ↓
 Context getting large
       ↓
 Consider compaction / handoff
```

So the status line becomes a kind of **context fuel gauge**.

### The key idea

> **Don't wait until Claude's context is exhausted to think about context management. Make context usage visible while you work.**

That ties directly into the previous lessons you shared about **compaction, handoffs, phase boundaries, and steering**: the status line gives you the signal that tells you *when* those techniques may become useful.
-----


# Showing Context in the Status Line

Claude Code doesn't show your [context window](https://www.aihero.dev/ai-coding-dictionary/context-window) usage by default. You need to set this up yourself if you want to monitor it constantly while you're coding.

Having that number right in your status line, visible at a glance, gives you the feedback you need to make good decisions about your [session](https://www.aihero.dev/ai-coding-dictionary/session). We'll use [`ccstatusline`](https://www.npmjs.com/package/ccstatusline), a community tool that formats Claude Code's session data into a clean status line.

## Steps To Complete

### Configure ccstatusline

- [ ] Create the config directory

```bash
mkdir -p ~/.config/ccstatusline
```

- [ ] Create `~/.config/ccstatusline/settings.json` with this content:

```json
{
  "version": 3,
  "lines": [
    [
      {
        "id": "1",
        "type": "context-length",
        "color": "yellow",
        "bold": true,
        "rawValue": true
      },
      {
        "id": "2",
        "type": "custom-text",
        "customText": "(",
        "color": "brightBlack",
        "merge": "no-padding"
      },
      {
        "id": "3",
        "type": "context-percentage",
        "color": "brightBlack",
        "rawValue": true,
        "merge": "no-padding"
      },
      {
        "id": "4",
        "type": "custom-text",
        "customText": ")",
        "color": "brightBlack",
        "merge": "no-padding"
      }
    ],
    [],
    []
  ],
  "flexMode": "full-minus-40",
  "compactThreshold": 60,
  "colorLevel": 2,
  "defaultSeparator": " ",
  "inheritSeparatorColors": false,
  "globalBold": false,
  "powerline": {
    "enabled": false,
    "separators": [" "],
    "separatorInvertBackground": [false],
    "startCaps": [],
    "endCaps": [],
    "autoAlign": false
  }
}
```

This configures four widgets on one line: a bold yellow [token](https://www.aihero.dev/ai-coding-dictionary/token) count (`context-length`), followed by a dimmed percentage in parentheses. The `"merge": "no-padding"` on the parentheses and percentage widgets glues them together without extra spacing, while `"defaultSeparator": " "` adds a space between the token count and the opening parenthesis. `"rawValue": true` strips the labels so you get clean numbers.

### Update Claude Code Settings

- [ ] Open `~/.claude/settings.json`

If this file doesn't exist yet, create it.

- [ ] Add the status line configuration

```json
{
  "statusLine": {
    "type": "command",
    "command": "npx ccstatusline@latest"
  }
}
```

Preserve any other settings that might already exist in this file. Claude Code pipes session data to this command automatically - `ccstatusline` reads it and outputs the formatted status line.

### Test Your Setup

- [ ] Restart Claude Code completely

Close the application fully and reopen it.

- [ ] Check your status line

You should now see a bold yellow token count followed by a dimmed percentage (like `186.2k (17.3%)`) displayed in your Claude Code status line. These numbers update as your context window fills during your session.
