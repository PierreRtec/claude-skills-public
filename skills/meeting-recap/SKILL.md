---
name: meeting-recap
description: Use after a meeting (prospect call, client call, internal sync, partnership discussion) to create a structured recap in Notion, draft a follow-up email, and track action items.
user-invocable: true
license: MIT
compatibility: Requires Notion MCP server for creating recap pages.
metadata:
  author: PierreRtec
  version: "1.0.0"
  openclaw:
    emoji: "📋"
    homepage: https://github.com/PierreRtec/claude-skills-public
allowed-tools: Read Edit Write Glob Grep
---

# Meeting Recap

Create structured meeting notes in Notion, draft follow-up emails, and track action items after any professional meeting.

## Configuration

Before first use, set these values in your project's CLAUDE.md or pass them inline:

- **Notion parent page ID**: The page where meeting recaps will be created
- **Your name**: Your display name for the recap
- **Your title**: Your role/title (e.g. "CTO", "AI Consultant", "Founder")
- **Your company**: Your company name
- **Pipeline DB ID** (optional): Notion database ID for tracking deals/opportunities

## Page Format

Title: `Meeting [Company] x [First Last] - DD/MM/YYYY`

```
---
## Participants
- **[First Last]** - [Title], [Company] ([email if available](mailto:email))
- **[Your Name]** - [Your Title], [Your Company]
- [Other participants...]

---

## Company Context

<table header-row="true">
<tr><td>Info</td><td>Detail</td></tr>
<tr><td>Industry</td><td>...</td></tr>
<tr><td>Revenue</td><td>...</td></tr>
<tr><td>Headcount</td><td>...</td></tr>
<tr><td>Website</td><td>...</td></tr>
<tr><td>Key info</td><td>...</td></tr>
</table>

---

## Identified Needs

### Need 1 - [Title]
...

### Need 2 - [Title]
...

---

## What Was Proposed

<table header-row="true">
<tr><td></td><td></td></tr>
<tr><td>**Model**</td><td>Consulting / SaaS / Training / ...</td></tr>
<tr><td>**Pricing**</td><td>Daily rate / Fixed price / Subscription</td></tr>
<tr><td>**Estimated scope**</td><td>X days</td></tr>
<tr><td>**Estimated amount**</td><td>X,000 EUR</td></tr>
<tr><td>**Deliverable**</td><td>...</td></tr>
<tr><td>**Decision maker**</td><td>...</td></tr>
</table>

---

## Follow-up Email Draft

```
Subject: Following up on our conversation - [topic]
```

```
[First name],

[Email body - concise, professional, referencing key points discussed and next steps]

Best regards,
[Your Name]
[Your Company]
```

---

## Post-Meeting Debrief

### What went well
- ...

### Points of attention
- ...

### Strategic insight
...

---

## Next Steps
- [ ] **[Your Name]**: send follow-up email
- [ ] **[Prospect]**: [expected action]
- [ ] **[Your Name]**: [technical action]
- [ ] **[Team]**: [shared action]

---

## Links
- Transcript: `[local path or URL]`
- Recording: `[URL if available]`
```

## Workflow

### Step 1: Gather information

Ask the user for:
- Who was on the call? (names, titles, company)
- What was discussed? (or provide a transcript/notes)
- What was proposed/agreed upon?
- Any follow-up actions?

If a transcript is available (file path or pasted text), extract all information from it.

### Step 2: Create the Notion page

Create a new page under the configured parent page ID using the Notion MCP server. Follow the format above strictly.

### Step 3: Draft the follow-up email

Write a professional, concise follow-up email that:
- References the key points discussed
- Confirms any agreements or next steps
- Includes a clear call to action
- Matches the tone of the meeting (formal/casual)

### Step 4: Update pipeline (if configured)

If a pipeline database ID is configured, update the relevant entry with:
- New status
- Meeting date
- Notes summary
- Next follow-up date

### Step 5: Output

Display:
- Link to the Notion page
- The follow-up email (ready to copy/send)
- Summary of next steps with owners

## Checklist

- [ ] Create the Notion page with the correct format under the configured parent
- [ ] Fill all sections (context, needs, proposal, email, debrief, next steps)
- [ ] Draft the follow-up email ready to send
- [ ] Update pipeline if configured
- [ ] Update task board if applicable

## Common Mistakes

- Forgetting `---` separators between sections
- Not using the table format (`<table header-row="true">`)
- Omitting the drafted follow-up email (not just bullet points)
- Forgetting to assign owners to each next step
- Creating a recap without validating key facts with the user
