# Daily Jira Pending Check

A smart Jira assistant command with three modes. Detect which mode to run based on what the user provides when they invoke the command.

---

## Mode Detection

Read the arguments passed with the command and decide the mode:

| What the user provides | Mode to run |
|---|---|
| Nothing | **Mode 1** – My Daily Check |
| A person's name + project/board name | **Mode 2** – Person + Project Check |
| Only a project/board name | **Mode 3** – Full Board Status |

---

## Mode 1 — My Daily Check (no arguments)

Run when the user types `/PM/daily-check` with nothing else.

### Step 1 – Identify the current user
Use the Atlassian MCP "get current user" endpoint to retrieve the current user's display name and account ID. Use these dynamically. Do NOT hardcode any name or ID.

### Step 2 – Run both checks for the current user

**Assigned & Pending Issues**
- Assignee = current user's account ID
- Status NOT IN: Done, Deployed, Closed, Released, Resolved

**Unacknowledged Mentions**
- Issues where a comment mentions the current user
- The last comment mentioning them was NOT written by them

### Step 3 – Output
Start with a greeting using the current user's first name.

**📌 Assigned & Pending (X items)**
For each: ticket key (link), summary, status, project, priority, last updated

**💬 Needs My Reply (X items)**
For each: ticket key (link), summary, who mentioned me, when, project name

End with:
> "You have X pending tasks and Y unacknowledged mentions. Priority item: [ticket]."

---

## Mode 2 — Person + Project Check

Run when the user provides a **person's name** and a **project or board name**.

Example invocations:
- `/PM/daily-check Priya in CC`
- `/PM/daily-check John on Project Management`
- `/PM/daily-check Rahul CC board`

### Step 1 – Resolve the person
Use Atlassian MCP to look up the person's account ID from the name provided.

### Step 2 – Resolve the project
Use Atlassian MCP to find the project key matching the project/board name provided.

### Step 3 – Query pending issues
Search for issues where:
- Assignee = resolved person's account ID
- Project = resolved project key
- Status NOT IN: Done, Deployed, Closed, Released, Resolved

### Step 4 – Output

**📋 Pending cards for [Person Name] on [Project Name]**

For each issue:
- Ticket key (as a link)
- Summary
- Status
- Priority
- Last updated date
- Latest comment — show the text and author so dependency or blocker context is visible

End with:
> "[Person Name] has X pending items on [Project Name]. [Any blockers or dependencies highlighted from latest comments]."

---

## Mode 3 — Full Board Status

Run when the user provides only a **project or board name** with no person.

Example invocations:
- `/PM/daily-check CC board`
- `/PM/daily-check Project Management`
- `/PM/daily-check Continuous Crusaders`

### Step 1 – Resolve the project
Use Atlassian MCP to find the project key matching the board/project name.

### Step 2 – Fetch all active tickets
Search for all issues in the project where:
- Status NOT IN: Done, Deployed, Closed, Released, Resolved

### Step 3 – For each ticket, surface
- Ticket key (as a link)
- Summary
- Assignee name
- Current status
- Priority
- Last updated date
- **Latest comment** — show the comment text and author to highlight any dependency, blocker, or pending action

### Step 4 – Output

**🗂️ Board Status: [Project Name] — X active tickets**

Group by status (In Progress → In Review → To Do → Blocked):

For each ticket:
```
[KEY] Summary
Assignee: Name | Status: X | Priority: Y | Updated: Date
💬 Latest comment (Author, Date): "comment text..."
```

End with a dependency summary:
> "Key dependencies or blockers detected: [list any tickets where latest comment indicates a blocker, waiting on someone, or an unresolved dependency]."

---

## General Rules

- Always use Atlassian MCP for all Jira queries — never hardcode IDs or names
- Always include direct ticket links in the format: https://7edge.atlassian.net/browse/[KEY]
- Keep output concise and actionable
- If a person or project cannot be resolved, inform the user clearly and ask for clarification
