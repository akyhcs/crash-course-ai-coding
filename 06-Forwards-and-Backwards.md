Here’s a **copy‑friendly summary** of the “Going Forwards and Backwards in Time” lesson:

---

### 🔄 Reverting Changes
- Say **“revert that, please”** → agent undoes last change.  
- Save with **Ctrl+S**.  

---

### ⏪ Rewind Mode
- Enter with **Escape twice**.  
- Lets you restore to any earlier checkpoint.  
- Options:  
  - **Restore code + conversation** → full rewind.  
  - **Restore conversation only** → keep code edits.  
  - **Restore code only** → keep conversation history.  
- Cancel with **“nevermind”**.  

---

### 📂 Persisting & Resuming Sessions
- Sessions are saved locally.  
- Quit with **Ctrl+C twice**.  
- Resume methods:  
  1. **UUID** → `claude --resume [UUID]`  
  2. **Fresh session** → run `claude`, then `/resume` to pick past sessions.  
  3. **Continue last session** → `claude --continue`.  

---

### ✅ Quick Reference
- **Escape twice** → rewind mode  
- **Ctrl+S** → save diff  
- **Ctrl+C twice** → quit session  
- **claude --resume [UUID]** → resume exact state  
- **/resume** → browse/search past sessions  
- **claude --continue** → jump back into last session  

---

This condensed version is easy to paste into your notes or terminal guide. Would you like me to also create a **one‑page cheat sheet** with commands grouped by action (Rewind, Resume, Reset) so you can keep it handy while coding?
