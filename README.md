# 🤖 SmartCue: A Multi-Protocol Task and Meeting Automation Hub

## Introduction

SmartCue is a task management system designed to streamline project workflows by integrating with GitHub issues, Google Calendar, and an AI assistant. Its standout feature is the implementation of custom Model Context Protocol (MCP) servers—Calendar MCP, GitHub MCP, and Postgres MCP—which facilitate structured communication with external services for efficient data exchange.

Users can create tasks through:

* **Add Task Form**: Manual task creation with fields for task name, description, priority, assignee, and meeting details.
* **AI Assistant**: Natural language task creation, guided by AI prompts to ensure all task details are captured.

## Screenshot

<p align="center">
-  <img src="docs/screenshots/dashboard.png" alt="SmartCue Dashboard" width="100%" />
+  <img src="frontend/public/dashboard.png" alt="SmartCue Dashboard" width="100%" />
  <br/>
  <em>SmartCue Dashboard – Task overview and quick actions</em>
  
</p>

## Features

### Dual Task Creation

1. **Add Task Form**: Create tasks manually with required and optional fields.
2. **AI Assistant**: Use natural language prompts like "Create a high-priority task to write a report for John" to create tasks. The AI guides users through a multi-step confirmation process to ensure completeness.

### Custom MCP Servers

* **Calendar MCP Server**: Manages interactions with Google Calendar for scheduling meetings.
* **GitHub MCP Server**: Synchronizes tasks with GitHub issues and processes webhook events.
* **Postgres MCP Server**: Handles database operations with PostgreSQL.

### Integrations

* **GitHub Integration**: Syncs tasks with GitHub issues. Closing a GitHub issue updates the corresponding task status in SmartCue to "completed."
* **Google Calendar Integration**: Automatically schedules and manages meetings associated with tasks.
* **Custom Meeting Calendar**: A calendar view built with `react-big-calendar` to visualize meetings with dynamic styling and detailed tooltips.

### Interactive Dashboard

* Displays tasks with pagination and filtering options.
* Filters tasks by status ("open" or "closed") and assignee.
* Provides task statistics (e.g., total tasks, high-priority tasks, tasks per assignee).

### Responsive Frontend

* Built with React and Material-UI for a clean, intuitive UI.
* Snackbar notifications for user feedback.
* Tailored calendar experience for meeting management.

## Technologies Used

### Backend

* **Python 3.11+** with FastAPI
* **PostgreSQL** for database management
* **Custom MCP Servers** for communication:
  * Calendar MCP
  * GitHub MCP
  * Postgres MCP
* **Google Calendar API** for scheduling
* **GitHub API** for issue management
* **Gemini API** for AI-driven task creation

### Frontend

* **React 18**
* `react-big-calendar` for meeting visualization
* Material-UI for UI components
* `moment` for date handling
* React Router for navigation

### Other

* **Node.js 16+** for frontend development
* **npm** for package management
* **Git** for version control

## Use Cases

1. **Project Management**: Manage tasks, assign responsibilities, and track progress with GitHub synchronization.
2. **Meeting Scheduling**: Schedule and visualize meetings tied to tasks.
3. **AI-Driven Task Creation**: Quickly create tasks using natural language.
4. **Cross-Platform Collaboration**: Seamlessly manage tasks across technical and non-technical teams.
5. **Efficient Workflow**: Custom MCP servers ensure reliable communication with external services.

## Setup

### Prerequisites

* Python 3.11+
* Node.js 16+
* PostgreSQL database (optional if running without DB — see flags below)
* GitHub account and personal access token
* Google Cloud project with Calendar API enabled and credentials
* Gemini API key

### Backend Setup

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/namansingh15/SmartCue.git
   cd SmartCue
   ```

2. **Install Dependencies** (minimal, conflict-free):

   ```bash
   python -m venv venv
   # Windows PowerShell
   .\venv\Scripts\Activate.ps1
   python -m pip install --upgrade pip
   pip install -r requirements.txt
   ```

3. **Set Up the Database**:

   * Create a PostgreSQL database named `smartcue`.
   * Run the initialization script:

     ```bash
     psql -U postgres -d smartcue -f backend/init_db.sql
     ```

4. **Configure Environment Variables**:
   Create a `.env` file in the project root:
5. **Set Up GitHub Webhook**:

   * Navigate to your GitHub repository settings.
   * Go to **Settings > Webhooks > Add webhook**.
   * Set the payload URL to `http://<your-backend-url>/webhooks`.
   * Select `application/json` content type and issue events.

6. **Run the Backend**:

   ```bash
   python -m uvicorn backend.main:app --reload --host 0.0.0.0 --port 8000
   ```

### Run with a subset of MCP servers

Set flags in `.env` (or environment) and restart the backend:

- `SKIP_POSTGRES=1` → No DB writes; `/tasks` returns an empty list, task creation returns `task_id=-1` (GitHub issue still created), webhooks ignored, `/meetings` empty.
- `SKIP_GITHUB=1` → Task creation endpoints return 503 (GitHub disabled).
- `SKIP_CALENDAR=1` → Meetings aren’t created/fetched.
- `SKIP_AI=1` → `/ai-assistant` disabled.

This is useful to demo GitHub-only flows while DB/Calendar are offline.

### Start PostgreSQL quickly (Docker)

```bash
docker run --name smartcue-pg \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=your_password \
  -e POSTGRES_DB=smartcue \
  -p 5432:5432 -d postgres:16
```

Update `.env` to match (`DB_HOST=127.0.0.1`, `DB_PORT=5432`).

### Frontend Setup

1. **Navigate to the Frontend Directory**:

   ```bash
   cd frontend
   ```

2. **Install Dependencies**:

   ```bash
   npm install
   ```

3. **Run the Frontend**:

   ```bash
   npm start
   ```

   The frontend will run on `http://localhost:3000`.

## Usage Instructions

### Access the Dashboard

1. Open `http://localhost:3000` in your browser.
2. View tasks, filter by status or assignee, and check task statistics.

### Create a Task Manually

1. Click "Add New Task" on the dashboard.
2. Fill in details like task name, description, priority, assignee, and optional meeting details.
3. Submit the form to create the task, which also creates a GitHub issue and Google Calendar event.

### Create a Task with AI Assistant

1. Navigate to the "AI Assistant" page.
a2. Enter a natural language prompt.
3. Follow the AI’s guidance to confirm task creation.

### View Meetings

1. Go to the "Meeting Calendar" page.
2. View scheduled meetings in month, week, or day views.

### GitHub Integration

* Set `REPO_NAME` to a repository the token can access.
* The backend labels issues with `priority:low|medium|high`. If labels don’t exist and your token cannot create them, pre-create the labels or remove labels in code.
* Webhook (optional): point it to `http://<backend>/webhooks` for status sync.

## Troubleshooting

- `ImportError: attempted relative import` → run from project root with `python -m uvicorn backend.main:app ...`.
- `403 You do not have permission to create labels` → token lacks access or labels cannot be created; pre-create `priority:*` labels or remove labels.
- `connection refused 5432` → Postgres not running or `DB_HOST/PORT` mismatch; on Windows use `127.0.0.1`.
- Frontend shows 0 tasks after creating one → DB disabled/down; GitHub issue exists but not persisted.

## Contributing

Feel free to fork the repository, make improvements, and submit a pull request. Contributions are welcome!

## License

This project is licensed under the MIT License. See the `LICENSE` file for more details.

---

For further questions or support, please contact the repository owner or raise an issue in the GitHub repository.
