# Fleet Bundle Debug: Post-Render Issue 📝

## Context
Fleet GitRepo deployment failed with:
```text
Post-render failed: json: unsupported type: map[interface {}]interface {}
```

## Symptoms
- Repo deployed fine as raw Helm chart.  
- Fleet conversion (`yaml` → `json`) triggered error.  

## Debugging
Tested rendering locally:
```bash
helm template ./chart | kubectl apply --dry-run=client -f -
```
→ Same error confirmed.  

Root cause: A trailing slash in an Ingress route path (`path: "/auth/"`).  

## Fix
Changed:
```yaml
path: "/auth/"
```
to:
```yaml
path: "/auth"
```

## Lesson learned
Fleet applies strict JSON schema. Always `kubectl apply --dry-run` locally before Fleet rollout.
