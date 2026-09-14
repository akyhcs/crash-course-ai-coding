Here’s a **summary of the whole “Statelessness” page** from the *AI Coding Crash Course* you’re viewing:

---

### 🔑 Core Idea
- The **model itself is stateless** — it processes each request independently and doesn’t remember past interactions  [Current page](citation-section://1395323855/7).
- **State lives in the harness**, which carries the conversation history across turns within a session  [Current page](citation-section://1395323855/6).
- The **environment** (like a file system) is always stateful, persisting files and data beyond sessions  [Current page](citation-section://1395323855/9).

---

### 📊 Comparison Table (from the page)
- **Model** → Stateless, processes one request at a time.  
- **Harness** → Stateful within a session, remembers all messages until cleared.  
- **Environment** → Stateful indefinitely, persists files and changes on disk  [Current page](citation-section://1395323855/9).

---

### 🧠 Why This Matters
- Many people want agents to be stateful, but the model’s statelessness is by design.  
- To add memory, developers build **memory systems** that augment the environment, allowing agents to recall information across sessions  [Current page](citation-section://1395323855/17).  
- The recommended approach: **save important state in the environment** (e.g., codebase, files) rather than bolting memory directly onto the agent  [Current page](citation-section://1395323855/19).

---

### 💡 Practical Takeaways
- Clearing a session wipes the harness’s memory, but files in the environment remain  [Current page](citation-section://1395323855/21).  
- If you want an agent to remember something long‑term, **write it into the environment** (like saving to a file or codebase)  [Current page](citation-section://1395323855/20).  
- Defaulting to statelessness keeps systems simpler and more reliable  [Current page](citation-section://1395323855/29).

---

### 📝 Quiz Examples (from the page)
- If you clear the session, only the **environment** (files on disk) still holds past state  [Current page](citation-section://1395323855/34).  
- To make an agent remember a decision after the session ends, the best option is to **write it into the codebase/environment**  [Current page](citation-section://1395323855/36).

---

👉 In short:  
- **Model = stateless brain**  
- **Harness = short‑term memory for a session**  
- **Environment = long‑term memory**  

Would you like me to create a **visual flow diagram** (Model → Harness → Environment → Agent) so you can see how these layers interact and where state is actually stored?
