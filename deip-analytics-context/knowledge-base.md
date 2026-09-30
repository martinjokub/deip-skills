# Knowledge Base Format Guide

Structured format for AI knowledge base entries.

## Template

```markdown
### [CATEGORY] Title

**Date:** YYYY-MM-DD
**Related Items:** Tool names if applicable

Description of the learning, rule, or insight.

- Bullet points for specific details
- Include actionable information
```

## Category Examples

### Integration Entry
```markdown
### [INTEGRATION] HubSpot → Mailchimp Sync

**Date:** 2024-12-18
**Related Items:** HubSpot CRM, Mailchimp

Contacts sync via Zapier webhook every 6 hours. Marketing uses this for newsletter segmentation.

- Webhook URL managed in HubSpot settings
- Only "Marketing Qualified" contacts sync
- Owner: marketing@company.com
```

### Rule Entry
```markdown
### [RULE] Annual Billing Policy

**Date:** 2024-12-18

Company policy: All tools >€500/year must use annual billing to maximize discounts.

- Exceptions require CFO approval
- Aim for 15-20% annual billing discount
- Finance team reviews quarterly
```

### Exception Entry
```markdown
### [EXCEPTION] Slack Free Tier

**Date:** 2024-12-18
**Related Items:** Slack

Using free tier intentionally - team is small and message history limit is acceptable.

- Do not suggest upgrading unless team grows >15 people
- User explicitly declined Pro tier on 2024-11-15
```

### Insight Entry
```markdown
### [INSIGHT] Marketing Tool Overlap

**Date:** 2024-12-18
**Related Items:** Mailchimp, HubSpot Marketing, Constant Contact

Three email marketing tools with overlapping functionality.

- Consolidation could save ~€400/month
- HubSpot Marketing is primary; others are legacy
- Review during Q1 tool rationalization
```

### Renewal Entry
```markdown
### [RENEWAL] Salesforce Enterprise Agreement

**Date:** 2024-12-18
**Related Items:** Salesforce

Enterprise agreement renews March 2025.

- Current rate: $150/user/month
- Negotiated 18% discount in 2023
- Contact: John Smith, john@salesforce.com
- 90-day notice required for changes
```

### Contact Entry
```markdown
### [CONTACT] AWS Account Manager

**Date:** 2024-12-18
**Related Items:** AWS

Primary contact for AWS enterprise support.

- Name: Sarah Johnson
- Email: sarah.johnson@aws.amazon.com
- Phone: +1-555-0123
- Best for: Reserved instance pricing, support tier upgrades
```

### Preference Entry
```markdown
### [PREFERENCE] Tool Vendor Preferences

**Date:** 2024-12-18

Team preferences for tool selection.

- Prefer established vendors over startups for critical infrastructure
- Open source alternatives welcome for non-critical tools
- SSO/SAML required for any tool with user data
- GDPR compliance mandatory for European customer data
```

### Relationship Entry
```markdown
### [RELATIONSHIP] HubSpot → Slack Integration Purpose

**Date:** 2024-12-18
**Related Items:** HubSpot CRM, Slack

Connection type: integration. HubSpot syncs new deal updates to Slack #sales channel via Zapier.

- Direction: HubSpot → Slack (one-way notification)
- Trigger: When deal stage changes to "Qualified"
- Purpose: Sales team gets real-time deal alerts
- Confirmed by user on 2024-12-18
```

### Relationship Insight Entry
```markdown
### [RELATIONSHIP] AWS Dependency Chain

**Date:** 2024-12-18
**Related Items:** Web App, AWS, Cloudflare

Web App depends on AWS (hosting) and Cloudflare (CDN/DNS). AWS is the critical dependency — without it, the app goes down entirely. Cloudflare is a performance dependency — app works without it but slower.

- Web App → AWS: hard dependency (type: dependency)
- Web App → Cloudflare: soft dependency (type: dependency, label: "CDN/DNS")
- User confirmed: "AWS is mission-critical, Cloudflare is nice-to-have"
```

## Best Practices

1. **Always include date** for context
2. **Link to related items** when applicable
3. **Be specific** with actionable details
4. **Use consistent categories** for searchability
5. **Include owner/contact** for accountability
6. **Note exceptions** clearly
7. **Record relationship confirmations** — when a user confirms or corrects a relationship meaning, store it in the knowledge base for future reference
8. **Distinguish hard vs soft dependencies** — note whether a relationship is critical or optional
