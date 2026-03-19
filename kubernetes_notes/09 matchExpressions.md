# matchExpressions in Kubernetes

## Overview
The **matchExpressions** field allows more advanced label selection logic compared to `matchLabels`.  
It supports operators such as **In**, **NotIn**, **Exists**, and **DoesNotExist** for flexible resource selection.

---

## Supported Operators
- **In** – Matches if the label value is in the specified list
- **NotIn** – Matches if the label value is NOT in the specified list (label value is NOT in the list)
- **Exists** – Select resources if the label key is present (value doesn’t matter) (any value)
- **DoesNotExist** – Matches if the label key does not exist

  ## Even Simpler (1-line each)
- In → Allow only these values   👉  Matches: environment=prod ✅
- NotIn → Exclude these values   👉  environment=dev ❌
- Exists → Label key must be there (- key: environment) , Value doesn’t matter (values: production, staging)
- DoesNotExist → Label key must NOT be there  👉 environment=prod ❌
  
---

## Example Selector Using matchExpressions

```yaml
selector:
  matchExpressions:
  - key: environment            # Look for the "environment" label
    operator: In                # Match if value is one of the listed values
    values:
    - production               # Match if environment is production
    - staging                  # OR if environment is staging    

  - key: version                # Look for the "version" label
    operator: Exists            # Match if the version label exists (any value)

template:
  metadata:
    labels:
      app: nginx                # Label to identify the app (optional for this selector)
      environment: production   # Must match environment expression
      version: v1               # Must exist to match the second expression
```
## Key Points
- matchExpressions provides ***advanced filtering logic for label selection***
- Multiple expressions are combined using **AND** logic (all conditions must match).
```
matchExpressions:
  - key: env
    operator: In
    values: [prod]

  - key: tier
    operator: In
    values: [frontend]
```
✔️ env must be prod ✅ AND tier must be frontend ✅
✔️ Only if both match → pod/node selected  ❌ If any one fails → not selected

- Useful when selecting resources based on multiple label conditions.

