# Mr. French Conversational AI

## Overview
This project simulates realistic conversations between a Parent and Timmy (the Child), with Mr. French as an AI agent who observes, manages, and mediates tasks. The system uses FastAPI for REST endpoints, ChromaDB for chat history/context, Supabase for structured task storage, and OpenAI for LLM-driven responses.

## Main Endpoints

### 1. `/chat/{chat_type}` (POST)
- **Purpose:** Handles chat between Parent-MrFrench or Timmy-MrFrench (not for Parent-Timmy direct chat).
- **Path Parameter:**  
  - `chat_type`: `"parent-mrfrench"` or `"timmy-mrfrench"`
- **Request Body:**
  ```json
  {
    "user_input": "Mr. French, add a task for Timmy: Clean the kitchen at 6pm today.",
    "user_type": "Parent"
  }
  ```
- **Response:**
  ```json
  {
    "user_type": "Parent",
    "user_input": "Mr. French, add a task for Timmy: Clean the kitchen at 6pm today.",
    "ai_response": "Noted. I've added the task for Timmy: 'Clean the kitchen' at 6:00 PM today.",
    "final_state_summary": {
      "mr_french_analysis": {
        "intent": "ADD_TASK",
        "task": "Clean the kitchen",
        "Due_Time": "6pm",
        "is_completed": "Pending"
      }
    }
  }
  ```
  - For `timmy-mrfrench`, the structure is similar, but `user_type` should be `"Timmy"`.

---

### 2. `/chat/{chat_type}/history` (GET)
- **Purpose:** Fetches full message history for a chat type.
- **Path Parameter:**  
  - `chat_type`: `"parent-timmy"`, `"parent-mrfrench"`, or `"timmy-mrfrench"`
- **Response:**  
  ```json
  [
    { "role": "user", "content": "Timmy, clean your room.", "sender": "Parent", "timestamp": "..." },
    { "role": "assistant", "content": "Okay Mom, I'll clean it.", "sender": "Timmy", "timestamp": "..." }
  ]
  ```
  - Each message includes `role`, `content`, `sender`, and `timestamp`.
  - For frontend: Display messages in chronological order, using `role` and `sender` to style bubbles.

---

### 3. `/parent-timmy/message` (POST)
- **Purpose:** Directly save a message to Parent-Timmy chat and trigger Mr. French's analysis.
- **Request Body:**
  ```json
  { "sender": "Parent", "message": "Timmy, do your homework." }
  ```
- **Response:**  
  ```json
  {
    "status": "ok",
    "message_saved": true,
    "mrfrench_action": "ADD_TASK",
    "mrfrench_analysis": { ... }
  }
  ```

### 4. `/tasks` (GET)
- **Purpose:** Fetches tasks, optionally filtered by status.
- **Query Params:** `status=Pending|Progress|Completed`
- **Response:**  
  ```json
  {
    "tasks": [
      { "id": "task123", "task": "Do Math Homework", "status": "Pending", "due_time": "19:00", "reward": "Ice cream" }
    ]
  }
  ```

### 5. `/reset-conversation` (DELETE)
- **Purpose:** Clears all chat history and tasks.
- **Response:**  
  ```json
  { "message": "All conversation data and tasks have been reset." }
  ```

### 6. `/mrfrench-logs` (GET)
- **Purpose:** Fetches Mr. French's internal analysis logs.
- **Response:**  
  ```json
  { "mrfrench_logs": [ ... ] }
  ```

### 7. `/timmy-zone` (GET/POST)
- **GET Purpose:** Returns Timmy's current zone (Red/Green/Blue).
- **POST Purpose:** Updates Timmy's zone.
- **Request Body (POST):**
  ```json
  { "zone": "Red" }
  ```
- **Response:**  
  ```json
  { "zone": "Red" }
  ```

### 8. `/logs/{chat_type}` (GET)
- **Purpose:** Fetches logs for a specific chat type.
- **Response:**  
  ```json
  { "logs": [ ... ] }
  ```

## Technologies Used
- FastAPI (REST API)
- ChromaDB (Vector store for chat/context)
- Supabase (Relational DB for tasks)
- OpenAI (LLM for persona responses)
- APScheduler (Reminders/cron jobs)

## How to Run

1. **Create a virtual environment:**
   ```sh
   python -m venv venv
   ```
2. **Activate the virtual environment:**
   - On Windows:
     ```sh
     venv\Scripts\activate
     ```
   - On macOS/Linux:
     ```sh
     source venv/bin/activate
     ```
3. **Install dependencies:**
   ```sh
   pip install -r requirements.txt
   ```
4. **Set up `.env` file** with your OpenAI and Supabase credentials.
5. **Run the API server:**
   ```sh
   uvicorn main:app --reload
   ```

## Frontend Integration
- Use `/chat/{chat_type}` for all chat flows.
- Use `/tasks` to display sidebar tasks.
- Use `/chat/{chat_type}/history` for conversation history.
- Use `/timmy-zone` to display/update Timmy's zone.
- Use `/mrfrench-logs` for Mr. French's analysis and thoughts.

## See Also
- [project_description.md](project_description.md) for a detailed project overview.
