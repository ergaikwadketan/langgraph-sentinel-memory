# 🧠 LangGraph Sentinel: Memory & Human Approval

> A Production-Grade Agent that remembers context across sessions ("Memory") and requests permission before taking expensive actions ("Human-in-the-Loop").

## 📉 The Problem: "Smart but Dangerous"
In Part 1, we built an agent that could check risks. But it had two major flaws:
1.  **Amnesia:** If a user asked *"What about those GPUs?"* two hours later, the AI had no idea what they were talking about.
2.  **Recklessness:** If it found stock in a different country (e.g., Germany), it might auto-ship from there, ignoring the **30% cost markup** and causing financial loss.

## 🛡️ The Solution
This V3 Agent solves these problems using **LangGraph Persistence** and **Interrupts**.

### 1. Long-Term Memory (Session Persistence)
* **Technology:** `MemorySaver` with a `thread_id`.
* **Benefit:** The agent remembers the entire conversation history. You can close the script, come back hours later, and it still knows you are looking for "GPU-X100".

### 2. The "Critical Tool" Pattern (Human Approval)
* **Technology:** `interrupt` inside a Tool.
* **Benefit:** The `finalize_order` tool is "locked".
    * If the AI tries to use it, the code **pauses execution**.
    * It warns the human: *"Warning: Sourcing from Germany is 30% more expensive."*
    * It waits for a manual "yes/no" before proceeding.

## 🧩 Architecture
`State` (Messages) ↔ `MemorySaver` (Database/RAM)
       ↓
`Agent` (Gemini)
       ↓
`Tools` (Inventory, Risks, **Finalize Order**)
       ↓
    🛑 **INTERRUPT** (If Finalize Order is called)

## 🚀 How to Run

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/ergaikwadketan/langgraph-sentinel-memory.git](https://github.com/ergaikwadketan/langgraph-sentinel-memory.git)
    cd langgraph-sentinel-memory
    ```

2.  **Install dependencies:**
    ```bash
    pip install langgraph langchain-google-genai langchain-community faiss-cpu pandas
    ```

3.  **Run the Notebook:**
    Open `sentinel_v3.ipynb` and run all cells.
    * **Session 1:** Ask about Taiwan (Stock: 0).
    * **Session 2:** Ask about "those GPUs" (It remembers!).
    * **Session 3:** Ask to ship from Germany. Watch it **PAUSE** and ask for your permission to pay the extra cost.

## 🛠️ Tech Stack
* **LangGraph:** Checkpointers & Interrupts.
* **Gemini 2.5:** Reasoning engine.
* **FAISS:** Vector Search for risks.
* **Pandas:** Simulated ERP with cost modifiers.
---
Created By Ketan Dilip Gaikwad
