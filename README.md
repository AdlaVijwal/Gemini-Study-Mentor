# Gemini Study Mentor – Multi-Agent Exam Prep Assistant

**Track:** Agents for Good (Education)  

---

## 1. Problem Statement

Students preparing for competitive exams and campus placements face a few common problems:

- They don't know **what to study and in what order**.  
- They lack **personalized feedback** on mistakes.  
- They struggle to track **progress over time** across topics like Quant, Reasoning, and Verbal.  
- Existing resources (PDFs, random videos, static notes) are not adaptive and not interactive.

There is a need for a **personal AI mentor** that can plan, quiz, explain, and adapt based on the student’s performance.

---

## 2. Solution Overview

**Gemini Study Mentor** is a **multi-agent, Gemini-powered exam prep assistant** that helps students:

- Set goals and study constraints (exam, time per day, etc.)  
- Generate a **personalized study plan**  
- Practice with **auto-generated quizzes**  
- Get **step-by-step explanations** for questions  
- Analyze **progress over time** and suggest what to focus on next  
- Discover **types of resources** to use for weak topics  

The system is built using the **Google Agent Development Kit (ADK)** and the **Gemini** model, with a **LoopAgent** handling repeated quiz practice rounds.

---

## 3. Architecture

### 3.1 High-Level Design

The system is composed of several agents and tools:

- **Root Agent** (`gemini_study_mentor_root`)  
  - Talks directly to the student.  
  - Coordinates sub-agents.  
  - Orchestrates the overall workflow:
    1. Study plan  
    2. Quiz practice loop  
    3. Progress analysis  
    4. Resource suggestions  

- **Study Planner Agent**  
  - Creates a personalized, day-wise study plan for the student.  
  - Reads & updates the student's profile (goals, exam, time per day, history summary).

- **Quiz Generator Agent**  
  - Generates multiple-choice questions (MCQs) for selected topics and difficulty.  
  - Produces 5 questions in a consistent Markdown format.

- **Explanation Agent**  
  - Takes quiz content and/or a specific question + answer.  
  - Provides step-by-step explanations and tips.  
  - Logs quiz attempts using tools.

- **Progress Analyst Agent**  
  - Reads quiz history.  
  - Identifies strong and weak topics.  
  - Summarizes performance and recommends next topics.

- **Resource Finder Agent**  
  - Suggests types of resources (e.g., playlists, book chapters, practice sites) for weak topics.  
  - Does not fabricate URLs, instead suggests what to search for.

- **Quiz Practice LoopAgent**  
  - Runs a small loop of:  
    - Generate quiz → Explain → repeat  
  - Demonstrates iterative learning and multi-step agent workflows.

### 3.2 Tools & Memory

The project uses simple **Python tool functions** (which can later be backed by a database):

- `save_user_profile(user_id, goals, exam, time_per_day_hours)`
- `load_user_profile(user_id)`
- `save_quiz_result(user_id, topic, question, user_answer, correct_answer, is_correct)`
- `load_quiz_history(user_id)`
- `compact_study_history(user_id)`

These tools act as a **basic memory mechanism**, storing:

- Student’s profile and goals  
- Quiz results over time  
- A compact summary of study history

Currently, the implementation uses in-memory Python dictionaries (`USER_DB`, `QUIZ_DB`) for simplicity. In a production system, these would be replaced with a persistent database.

---

## 4. Key Agent Concepts Demonstrated

This project deliberately showcases several **key agent features** required by the capstone rubric:

1. **Multi-Agent System**  
   - Root agent + multiple specialized sub-agents.  
   - A `LoopAgent` handles repeated quiz practice cycles.

2. **Tools**  
   - Custom tools for user profile and quiz history.  
   - Agents use these tools to read/write state (simulated memory).

3. **Sessions & Memory**  
   - User profile and quiz history are stored in an in-memory “database”.  
   - `compact_study_history` demonstrates basic **context compaction** and long-term state.

4. **Context Engineering**  
   - Prompts are designed so each agent only focuses on its specific sub-task.  
   - Quiz markdown is reused by the explanation agent to avoid overloading context.

5. **Loop Agent / Workflow Orchestration**  
   - A `LoopAgent` runs multiple rounds of quiz + explanation.  
   - A `SequentialAgent`-style root orchestrates study planner → loop → analysis → resources.

6. **(Optional) Observability & Evaluation**  
   - Print logs in tools (e.g., `[TOOL] save_quiz_result...`) for debugging.  
   - Manual evaluation can be done via curated scenarios and observing outputs.

---

## 5. How It Works – Example Flow

1. The student sends a message like:  
   > “My user_id is `student_01`. I’m preparing for TCS NQT in 45 days and can study 2 hours per day. Help me plan and give me practice.”

2. The **Root Agent**:
   - Extracts context (user_id, exam, time per day).  
   - Delegates to **Study Planner Agent**, which:
     - Calls `load_user_profile` / `save_user_profile`.  
     - Produces a Markdown study plan for several days.

3. The **Quiz Practice LoopAgent**:
   - In each iteration:
     - **Quiz Generator Agent** generates 5 MCQs for a topic.  
     - **Explanation Agent** reads the generated quiz and explains at least one question in detail.

4. After several iterations, the **Progress Analyst Agent**:
   - Calls `load_quiz_history` to inspect performance.  
   - Calls `compact_study_history` to create a short summary.  
   - Outputs a progress report with strong/weak topics and recommendations.

5. Finally, the **Resource Finder Agent**:
   - Suggests types of resources and search queries for weak topics.

---

## 6. Setup & Running Instructions

### 6.1 Prerequisites

- Python 3.10+  
- A working **Gemini API / ADK setup** (API key or Vertex AI config)  
- `google-adk` installed

### 6.2 Installation

```bash
# Clone this repository
git clone <YOUR_REPO_URL>.git
cd gemini-study-mentor

# (Optional) create a virtual environment
python -m venv .venv
source .venv/bin/activate  # Mac/Linux
# .venv\Scripts\activate   # Windows

# Install dependencies
pip install google-adk
