Here’s a **copy‑friendly summary** of the *Killing Bloat* lesson:

---

### 🎯 Core Idea
- Every request to your agent carries a **payload**: system prompt, tool schemas, skills, and instructions.  
- You’re billed for every **token** in that payload, even before typing a real prompt.  
- `/context` shows totals but not which tools are the biggest offenders → use the **request logger** for visibility.  

---

### ⚙️ Setup Request Logger
1. **Start logger** → `npm run request-logger`  
2. **Point agent at logger** → copy the printed command into a new terminal.  
3. **Clear old logs** → delete files in `request-logger/logs/`.  
4. **Send test message** → e.g., `Hello!` → new log files appear (`.md`, `.request.txt`, `.response.txt`).  

---

### 🔍 Examine Payload
- **System prompt** → search `<system-prompt>` in logs.  
  - Check environment, context management, recent git commits.  
- **Tools** → search `<tools>`.  
  - Often 70+ definitions, some very large and redundant.  
- **MCP tools** → search `mcp__`.  
  - Figma, Gmail, Slack, Google Drive, etc. → all shipped on every request.  
- **Skills** → search “following skills are available”.  
  - See built‑in vs project‑specific skills.  

---

### 💰 Understand the Cost
- Run `/context` → see token usage breakdown.  
- A single “Hello!” request can be **150–200 KB** and 4,000+ lines.  
- All of it is sent and billed **every turn**.  

---

### ✅ Key Takeaways
- Payload bloat = wasted tokens and reduced smart zone space.  
- Request logger gives **visibility** into what’s actually sent.  
- Cutting unused tools/skills improves **quality reasoning**, not just cost.  

---

This summary is concise enough to paste into your notes or terminal guide.  

👉 Do you want me to also **answer the quiz questions** (like I did for the previous sections) so you have the correct responses ready?
