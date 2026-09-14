Here’s a **copy‑friendly summary** of the *Your Starting Context* lesson:

---

### 🧠 Context Paranoia
- Be mindful of every token in the **context window**.  
- Extra/bloated tokens reduce efficiency and reasoning quality.  
- Goal: maximize the **smart zone** (where the agent thinks).  

---

### ⚙️ Resetting Config
1. In `.claude` directory:  
   - Rename `settings.json` → `settings-backup.json`.  
   - Rename `skills` → `skills-backup`.  
2. This resets to defaults before starting the course.  

---

### 📊 Baseline Context
- Fresh session with defaults: **~23k tokens**.  
  - System prompt: 3k  
  - System tools: 17.9k  
  - MCP tools: 24.5k  
  - Skills: 2k  
  - Messages: 8  
- All this loads before any real prompts.  

---

### 🔄 After Custom Config
- With backup settings restored: **~6.6k tokens**.  
  - System prompt: 2k  
  - System tools: 3.5k  
  - MCP tools: 192  
  - Skills: 1.1k  
  - Messages: 8  
- **16k tokens saved** → more room in smart zone.  

---

### 🎯 Why It Matters
- Not about cost or speed.  
- It’s about **quality reasoning**: more space for the agent to think.  
- Trimming config = higher‑quality outputs.  

---

### ✅ Key Commands
- `/context` → Shows what’s in the context window.  
- `/clear` → Empties session (but doesn’t show usage).  
- `/config` → Opens settings.  
- `/compact` → Squeezes active session.  

---

### 📌 Next Steps
- Rename `settings.json` and `skills` as backups.  
- Use stripped config for the course.  
- If stuck, ask in Discord for help.  

---

### 📝 Quiz Answers
1. **Main gain from stripping config** → More room in the **smart zone**.  
2. **Command to inspect baseline context** → `/context`.  

---

This summary is concise enough to paste into your notes or terminal guide. Would you like me to also build a **combined cheat sheet** that merges this with the Bash Commands and Rewind sections, so you have all essential shortcuts and context tips in one place?
