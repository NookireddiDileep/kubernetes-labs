## Key Learnings

- Pod IPs are ephemeral.
- Services provide a stable virtual IP.
- Services discover Pods using labels.
- EndpointSlices maintain the current list of healthy backend Pods.
- EndpointSlices are automatically updated when Pods are added or removed.
- Service definitions remain unchanged while EndpointSlices evolve.
