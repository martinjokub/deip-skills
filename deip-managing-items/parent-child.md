# Parent-Child Relationships

How to structure hierarchical item relationships in DEIP.

## Relationship Model

```
Parent Item (software, service, hardware, asset, human, ai)
├── Module (functional component)
├── Feature (enhancement/add-on)
└── Mobile App (mobile companion)
```

## Creating Child Items

Child types (`module`, `feature`, `mobileapp`) require `parentItemId`:

```json
{
  "workspace_id": "uuid",
  "items": [
    {
      "name": "HubSpot Marketing Hub",
      "type": "module",
      "parentItemId": "hubspot-service-uuid",
      "priceValue": 800,
      "billingCycle": "monthly"
    }
  ]
}
```

## Common Patterns

### SaaS with Modules
```
Salesforce (service)
├── Sales Cloud (module)
├── Service Cloud (module)
├── Marketing Cloud (module)
└── Salesforce Mobile (mobileapp)
```

### Software with Features
```
Slack (service)
├── Slack AI (feature)
├── Slack Connect (feature)
└── Slack Mobile (mobileapp)
```

### Human with Tools
```
Developer (human)
├── GitHub Copilot (feature - assigned seat)
└── IDE License (module - assigned license)
```

## Canvas Visualization

When both parent and child are on a canvas:
- System auto-creates `parent_child` edge
- Edge is marked as `is_system: true`
- Dashed blue line connects them

## Best Practices

1. **Use modules for major components** that have separate pricing or functionality
2. **Use features for add-ons** that enhance the parent without being standalone
3. **Use mobileapp for companion apps** representing mobile access
4. **Keep hierarchy shallow** - one level of children is usually sufficient
5. **Consider cost rollup** - child costs aggregate to parent in summaries

## Validation

- Child types cannot exist without `parentItemId`
- Parent must exist before creating children
- Circular references are prevented
- Deleting parent does NOT auto-delete children (they become orphaned)
