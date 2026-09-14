Here’s a **copy‑friendly summary** of the “Running Bash Commands” lesson:

---

### ⚡ Three Approaches
1. **Run commands directly** → Agent sees the output.  
2. **Background long‑running processes** → Keep dev servers running while continuing work.  
3. **Suspend agent** → Run hidden commands without agent seeing them.  

---

### 🖥️ Bash Mode
- Enter with **!** prefix.  
- Example: `! npm run typecheck`  
- Output goes straight into agent’s context → agent can debug errors.  

---

### 🔧 Backgrounding Processes
- For commands like `! npm run dev`.  
- Press **Ctrl‑B** → background task starts.  
- Output logged to local file, visible in background panel.  
- Options: view logs, stop with **X**, or return to agent.  

---

### 📴 Suspending Agent
- Press **Ctrl‑Z** → agent suspended.  
- Run hidden commands (agent cannot see output).  
- Example: `echo foo`.  
- Bring agent back with **fg** → state preserved.  

---

### 📊 Decision Tree
| Goal                                | Approach               | Shortcut   |
|-------------------------------------|------------------------|------------|
| Agent needs to see output           | Bash mode              | `!` prefix |
| Long‑running process (dev servers)  | Background             | Ctrl‑B     |
| Command hidden from agent           | Suspend agent          | Ctrl‑Z → fg|

---

### ✅ Quick Reference
- **!** → run command in bash mode (agent sees output)  
- **Ctrl‑B** → background long‑running process  
- **Ctrl‑Z** → suspend agent (hide command)  
- **fg** → bring agent back  

---

This summary is concise enough to paste into your notes or terminal guide. Would you like me to also create a **flowchart‑style cheat sheet** showing when to use `!`, `Ctrl‑B`, or `Ctrl‑Z` depending on your goal?
