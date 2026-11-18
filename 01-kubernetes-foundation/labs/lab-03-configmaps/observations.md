# Lab Observations

## Observations

- ConfigMaps allow application configuration to be separated from container images.
- Configuration changes can be managed without rebuilding images.
- Namespace consistency is important when creating and accessing resources.
- Environment variables from ConfigMaps are available inside running containers.

## Troubleshooting Performed

### Issue

Initially, ConfigMap was not visible.

### Root Cause

The ConfigMap was created in the `demo` namespace while verification commands were executed against the `default` namespace.

### Resolution

Verified resources using:

```bash
kubectl get configmaps -n demo
```

and deployed all resources into the same namespace.
