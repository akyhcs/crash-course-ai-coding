Here’s a **copy‑friendly summary** of the *Killing Bloat Solution* lesson:

---

### 🎯 Goal
- Reduce unnecessary tokens in your agent’s **system prompt** and payload.  
- Free up space in the **smart zone** for better reasoning.  

---

### ⚙️ Workflow
1. Edit `~/.claude/settings.json`.  
2. Quit agent → relaunch.  
3. Send `Hello!`.  
4. Run `/context`.  
5. Check logs for token usage.  

Baseline: **68.3k tokens**.  

---

### 🔧 Step‑by‑Step Reductions
- **Disable MCP connectors**  
  ```json
  { "disableClaudeAiConnectors": true }
  ```  
  → **47k tokens** (‑21k).  

- **Disable workflows**  
  ```json
  { "disableWorkflows": true }
  ```  
  → **39k tokens**.  

- **Disable bundled skills**  
  ```json
  { "disableBundledSkills": true }
  ```  
  → **37.1k tokens**.  

- **Disable artifacts**  
  ```json
  { "disableArtifact": true }
  ```  
  → **33.1k tokens**.  

- **Deny unused tools** (NotebookEdit, DesignSync, Cron*, PlanMode, PushNotification, RemoteTrigger, ReportFindings, ScheduleWakeup)  
  ```json
  { "permissions": { "deny": [ ... ] } }
  ```  
  → **21.6k tokens**.  

- **Optionally deny AskUserQuestion**  
  → **~19.9k tokens**.  

---

### 🧠 Key Principle
- **permissions.deny** removes tool definitions entirely from the system prompt → saves tokens every request.  
- Only keep tools/features you actually use.  

---

### ✅ Takeaway
- Start from a clean `settings.json`.  
- Disable connectors, workflows, bundled skills, artifacts.  
- Deny unused tools.  
- Result: **from 68.3k → under 20k tokens**.  
- More efficient reasoning, less bloat, higher quality outputs.  

---

Would you like me to also create a **ready‑to‑use JSON template** (with all the recommended disables and denies included) so you can copy it straight into your `settings.json`?





Here’s the **copy‑friendly text** extracted from your uploaded `settings.json` file:

```json
{
  "permissions": {
    "deny": [
      "NotebookEdit",
      "DesignSync",
      "CronCreate",
      "CronDelete",
      "CronList",
      "EnterPlanMode",
      "ExitPlanMode",
      "PushNotification",
      "RemoteTrigger",
      "ReportFindings",
      "ScheduleWakeup"
    ]
  },
  "disableClaudeAiConnectors": true,
  "disableWorkflows": true,
  "disableBundledSkills": true,
  "disableArtifact": true
}
```

---

This configuration disables Claude AI connectors, workflows, bundled skills, and artifacts, while explicitly denying access to several unused tools. It’s clean and ready to paste into your `~/.claude/settings.json`.  

👉 Do you want me to also prepare a **ready‑to‑use extended version** that includes the optional `AskUserQuestion` deny entry, so you can drop token usage further under 20k?
