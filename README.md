## What this project is
**Automatic Cross-Check-Coding-Agent** is an agent-style LangChain pipeline that automates a research coding task.

You have an Excel file where each row is a **unit** (one participant sentence).  
The agent assigns each unit one label by comparing it to the ground-truth **materials** (paper abstract + press release).

Labels:
- **In text**
- **Not in text**
- **Wrong**
- **Incomplete**

To improve reliability,We uses a **two-pass cross-check**:
1. **Classifier pass** proposes a label.
2. **Verifier pass** validates the proposed label and either agrees or corrects it.

A small router then decides the final label + confidence and writes everything back to Excel.

---

## How the agent works (high level)
For each unit (sentence):

1) **Tool Call 1: Classify**
- Input: materials + coding rules + unit
- Output: one label (In text / Not in text / Wrong / Incomplete)

2) **Tool Call 2: Verify**
- Input: same materials + unit + proposed label
- Output:
  - `AGREE`
  - or `DISAGREE: <CorrectLabel>`

3) **Router Decision**
- If `AGREE` → accept label (**high confidence**)
- If `DISAGREE: <CorrectLabel>` → override label (**medium confidence**) and flag for review
- If malformed output from either step → keep best guess (**low confidence**) and flag for review

---
