---
name: deip-reminders
description: Creates and manages automated notifications for payments, renewals, and reports. Use when users want alerts about upcoming events or periodic summaries.
---

# DEIP Reminders

Configure automated notifications for payment tracking, renewals, and periodic reports.

## REST API Endpoints

Base URL: `https://api.deip.app`

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces/{id}/reminders` | List all reminders |
| `GET` | `/workspaces/{id}/reminders/{reminder_id}` | Get reminder details |
| `POST` | `/workspaces/{id}/reminders` | Create reminder |
| `PUT` | `/workspaces/{id}/reminders/{reminder_id}` | Update reminder |
| `DELETE` | `/workspaces/{id}/reminders/{reminder_id}` | Delete reminder |
| `POST` | `/workspaces/{id}/reminders/{reminder_id}/test` | Send test notification |

### Example REST Call

```bash
# Create a payment reminder
curl -X POST -H "X-API-Key: deip_xxx" -H "Content-Type: application/json" \
  -d '{"name":"Payment Alerts","reminder_type":"payment","notification_method":"email","trigger_days_before":[7,30],"is_active":true}' \
  https://api.deip.app/workspaces/{id}/reminders
```

## MCP Tools (Alternative)

| Tool | Description |
|------|-------------|
| `list_reminders` | List all reminders in workspace |
| `get_reminder` | Get single reminder details |
| `create_reminder` | Create new reminder |
| `update_reminder` | Modify reminder settings |
| `delete_reminder` | Remove reminder |
| `test_reminder` | Send test notification |

## Reminder Types

| Type | Description |
|------|-------------|
| `payment` | Upcoming payment alerts |
| `renewal` | Subscription renewal notices |
| `overdue_alert` | Overdue payment notifications |
| `weekly_report` | Weekly ecosystem summary |
| `monthly_report` | Monthly ecosystem summary |
| `custom` | Custom date reminder |

## Notification Methods

| Method | Use Case |
|--------|----------|
| `email` | Direct email to user |
| `webhook` | Slack, Teams, Zapier, etc. |

## Creating Reminders

### Payment Alert
```json
{
  "workspace_id": "uuid",
  "name": "Payment Alerts",
  "reminder_type": "payment",
  "notification_method": "email",
  "trigger_days_before": [7, 30],
  "is_active": true
}
```

### Weekly Report
```json
{
  "workspace_id": "uuid",
  "name": "Weekly Digest",
  "reminder_type": "weekly_report",
  "notification_method": "webhook",
  "webhook_url": "https://hooks.slack.com/...",
  "weekly_day": 1
}
```

### Custom Reminder
```json
{
  "workspace_id": "uuid",
  "name": "Contract Review",
  "reminder_type": "custom",
  "custom_date": "2025-03-15",
  "notification_method": "email"
}
```

## Key Fields

| Field | Description |
|-------|-------------|
| `name` | Reminder name |
| `reminder_type` | Type (see above) |
| `notification_method` | `email` or `webhook` |
| `webhook_url` | URL for webhook notifications |
| `trigger_days_before` | Array of days before event |
| `weekly_day` | Day of week (0=Sunday, 1=Monday, etc.) |
| `monthly_day` | Day of month (1-28) |
| `custom_date` | Specific date for custom reminders |
| `is_active` | Enable/disable reminder |

## Recognizing Requests

| User Says | Action |
|-----------|--------|
| "Remind me when payments are due" | `create_reminder` (type: payment) |
| "Alert me before renewals" | `create_reminder` (type: renewal) |
| "Send me weekly reports" | `create_reminder` (type: weekly_report) |
| "I want a monthly summary" | `create_reminder` (type: monthly_report) |
| "Stop payment alerts" | `update_reminder` (is_active: false) or `delete_reminder` |
| "Change to notify 3 days before" | `update_reminder` (trigger_days_before) |
| "Send to Slack instead" | `update_reminder` (notification_method, webhook_url) |

## Testing

Always offer to test after creating:
```json
{
  "workspace_id": "uuid",
  "reminder_id": "reminder-uuid"
}
```

Sends a test notification to verify configuration.

## Best Practices

1. **Confirm notification method** before creating
2. **Offer sensible defaults** (7 days, 30 days before payment)
3. **Test webhooks** after configuration
4. **Suggest relevant reminders** proactively
5. **Offer pause vs delete** when stopping reminders
