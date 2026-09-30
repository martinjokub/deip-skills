# DEIP Item Types

Detailed descriptions of all nine item types in DEIP.

## Independent Types

These types exist on their own without requiring a parent item.

### Software
Desktop applications and installed software.
- Examples: Microsoft Office, Adobe Photoshop, VS Code
- Typically has one-time or annual licensing
- May spawn modules/features

### Service
SaaS platforms and cloud services.
- Examples: Slack, Notion, Salesforce, AWS
- Usually subscription-based (monthly/annual)
- Often has modules/features and mobile apps

### Hardware
Physical devices and equipment.
- Examples: Servers, laptops, networking equipment
- Often one-time purchase with optional support
- Can have software modules

### Asset
Digital assets and licenses.
- Examples: Domain names, SSL certificates, API keys, patents
- May be one-time or recurring
- Often linked to services

### Human
Team members, contractors, and external personnel.
- Examples: Full-time employees, freelancers, consultants
- Cost represents salary/rate
- Can manage tools or be responsible for services

### AI
Artificial intelligence tools and services.
- Examples: ChatGPT, GitHub Copilot, Anthropic Claude
- Usage-based or subscription pricing
- Often integrates with other tools

## Child Types (Require Parent)

These types MUST have a `parentItemId` linking to an independent item.

### Module
Sub-component or add-on of a parent tool.
- Examples: Salesforce CPQ, HubSpot Marketing Hub, Jira Service Management
- Extends parent functionality
- May have separate pricing

### Feature
Feature add-on that enhances a parent tool.
- Examples: Slack AI, Notion AI, GitHub Copilot seats
- Usually tied to parent subscription
- Often per-user or usage-based pricing

### Mobile App
Mobile application companion to a parent tool.
- Examples: Slack Mobile, Notion Mobile, Teams Mobile
- Usually included with parent subscription
- Represents mobile access capability

## Type Selection Guide

| Scenario | Type |
|----------|------|
| Standalone desktop application | `software` |
| Cloud-based tool with subscription | `service` |
| Physical equipment | `hardware` |
| License or certificate | `asset` |
| Team member or contractor | `human` |
| AI-powered tool | `ai` |
| Add-on to existing tool | `module` |
| Optional feature upgrade | `feature` |
| Mobile companion app | `mobileapp` |

## Canvas Behavior

When child items are placed on a canvas with their parent:
- System automatically creates a `parent_child` edge
- Visual grouping shows the relationship
- Helps visualize tool ecosystem structure
