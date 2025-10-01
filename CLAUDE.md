# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains n8n-based automation agent projects. n8n is a workflow automation tool that allows building automation flows using a visual node-based editor. All automation workflows are defined in JSON files that can be imported into n8n.

## Project Structure

The repository contains two main automation projects:

### 1. AI Weekly Report Generator (`01-weekly-report/`)
- **Purpose**: Automatically generates weekly work reports and KPT retrospectives from email content
- **Workflows**:
  - [mail-check-agent.json](01-weekly-report/mail-check-agent.json) - Main email processing workflow
  - [missing-email-reminder.json](01-weekly-report/missing-email-reminder.json) - Reminder workflow when no input detected
- **Tech stack**: n8n, Google Gemini, Google Sheets (for data storage), IMAP (for email)
- **Key features**:
  - Monitors email inbox (robertchoi@gachon.ac.kr) via IMAP trigger
  - Uses AI agent to extract and categorize work content from emails
  - Stores processed data in Google Sheets document (ID: `1raa1jSgJJ-foN0ILnL1wAKr55w-BBLSOjhTNPQe06aE`)
  - Scheduled to run automatically on Friday evenings
  - Sends reminder emails if no data input is detected (separate workflow)

### 2. Habit Tracker Agent (`02-habit-tracker/`)
- **Purpose**: Personalized habit tracking system for solopreneurs with variable schedules
- **Main workflow**: [Slack_baseline.json](02-habit-tracker/2.%20Baseline/Slack_baseline.json)
- **Tech stack**: n8n, Slack, Google Gemini/GPT/Claude, Notion, Google Calendar, Postgres/SQLite
- **Key features**:
  - Slack bot integration with app mention triggers
  - Thread-based conversation handling
  - Flexible goal adjustment based on Google Calendar schedule density
  - Periodic check-ins and reminders via Slack
  - Daily/weekly/monthly report generation to Notion

## Working with n8n Workflows

### Importing Workflows
1. Open n8n workflow editor
2. Click `…` (top right) > `Import from File`
3. Select the JSON workflow file
4. Update all credential references to match your n8n instance
5. Test workflow using n8n's "Execute Workflow" button before activating

### Workflow JSON Structure
- n8n workflows are stored as JSON files containing:
  - `name`: Workflow name
  - `nodes`: Array of workflow nodes (triggers, actions, AI agents, etc.)
  - Node structure includes:
    - `type`: Node type (e.g., `n8n-nodes-base.slackTrigger`, `@n8n/n8n-nodes-langchain.agent`)
    - `parameters`: Configuration for the node
    - `credentials`: References to credential IDs (not the actual credentials)
    - `position`: Visual position in the workflow editor

### Key Node Types Used
- **Triggers**: `emailReadImap`, `slackTrigger` (event-based execution)
- **AI Agents**: `@n8n/n8n-nodes-langchain.agent` (LLM-powered processing)
- **LLM Models**: `@n8n/n8n-nodes-langchain.lmChatGoogleGemini`
- **Data Operations**: `n8n-nodes-base.set` (edit fields), `n8n-nodes-base.code` (JavaScript transformations)
- **Integrations**: `googleSheets`, `slack` (for channel operations)

### Credential Management
- Credentials (API keys, OAuth tokens) are referenced by ID in workflow JSON but stored separately in n8n
- When modifying workflows, credential IDs must be updated to match the target n8n instance
- **IMPORTANT**: Never commit actual credentials to the repository - only workflow JSON files with credential references
- See [Base Model 세팅 가이드라인.md](02-habit-tracker/2.%20Baseline/Base%20Model%20세팅%20가이드라인.md) for detailed Slack setup guide

## Setting Up Projects

### Weekly Report Agent Setup
1. Configure IMAP credentials for email monitoring (target email: robertchoi@gachon.ac.kr)
2. Set up Google Sheets API credentials
3. Configure Google Gemini API key
4. Import [mail-check-agent.json](01-weekly-report/mail-check-agent.json) into n8n
5. Update credential references in imported workflow
6. Verify Google Sheet document ID in workflow matches target sheet

### Habit Tracker Agent Setup
Follow the detailed guide in [Base Model 세팅 가이드라인.md](02-habit-tracker/2.%20Baseline/Base%20Model%20세팅%20가이드라인.md):
1. Create Slack app at https://api.slack.com/apps/
2. Configure OAuth scopes: `app_mentions:read`, `channels:history`, `chat:write`, `commands`, `files:write`, `users:read`
3. Set up webhook URL and event subscriptions
4. Update bot_id in Edit Fields node
5. Configure Gemini/GPT credentials
6. Invite bot to Slack channel
7. Import workflow and update all credential references

## AI Agent System Messages

### Weekly Report Agent
- System message: "이메일 내용에서 업무 내용을 정리해 주세요\n업무내용만 출력해주세요"
- Extracts work content from email body (from, date, subject, textHtml fields)

### Habit Tracker Agent
- System message: "당신은 n8n 자동화 전문 봇입니다.\n사용자의 질문에 친근하고 바로 도움이 되는 방식으로 답변해주세요.(추가정보는 되도록 묻지 마세요)"
- Handles thread-based conversations
- JavaScript code transforms Slack messages into role-based format (user/assistant)

## Data Flow Patterns

### Email Processing Flow (Weekly Report)
Email (IMAP) → AI Agent (Gemini) → Text Classification → Google Sheets Append

### Slack Bot Flow (Habit Tracker)
Slack Trigger (app_mention) → Edit Fields (bot_id) → Get Thread → JavaScript Transform → AI Agent → Response

## Modifying Workflows

### Editing Existing Workflows
1. **Read the existing JSON** to understand node structure and data flow
2. **Make changes in n8n editor** (not directly in JSON) for complex modifications
3. **Export updated workflow** via `…` > `Export Workflow` (exports as JSON)
4. **Replace the old JSON file** in the repository
5. **Test thoroughly** using "Execute Workflow" in n8n before committing
6. **Document changes** in PRD.md files if requirements change

### Adding New Integrations
1. Check n8n documentation for available node types
2. Add required OAuth scopes (for Slack: see setup guide; for Google APIs: configure in Google Cloud Console)
3. Update setup documentation if new credentials are needed
4. Test webhook/trigger functionality before activating workflow

### Debugging Workflows
- Use n8n's "Execute Workflow" button to test manually
- Check execution history in n8n for failed runs
- Enable "Always Output Data" in node settings to debug data flow
- Use "Edit Fields" or "Code" nodes to log intermediate values