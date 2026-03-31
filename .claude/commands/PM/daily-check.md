# Daily Jira Pending Check

Run a daily Jira pending check for whoever is currently running this command and give them a clear summary of everything that needs their attention today.

## Step 1 — Identify the Current User

Before doing anything else, use the Atlassian MCP to call the "get current user" endpoint to retrieve:
- The current user's **display name**
- The current user's **account ID**

Use these values dynamically in all queries below. Do NOT hardcode any name or account ID.

## Step 2 — What to Check

Using the Jira MCP tools, do the following for the current user:

### Assigned & Pending Issues
Search for all Jira issues where:
- Assignee = current user's account ID
- Status is NOT in: Done, Deployed, Closed, Released, Resolved

### Unacknowledged Mentions
Search for Jira issues where:
- A comment mentions the current user by name or account ID
- The last comment mentioning them was NOT written by them
(i.e., someone is waiting for their response)

## Step 3 — Output Format

Start with a greeting using the current user's first name.

Then present results in two clear sections:

**📌 Assigned & Pending (X items)**
For each issue: ticket key (as a link), summary, status, project name, priority, last updated date

**💬 Needs My Reply (X items)**
For each issue: ticket key (as a link), summary, who mentioned me, when they mentioned me, project name

End with a one-line summary like:
> "You have X pending tasks and Y unacknowledged mentions. Priority item: [highest priority ticket]."

Keep the tone concise and actionable.
