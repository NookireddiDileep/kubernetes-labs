# Observations

- Kubernetes Secrets store confidential information.
- Secrets are stored as Base64 encoded values (not encrypted by default).
- Applications consume Secrets as:
  - Environment Variables
  - Mounted Volumes
- stringData automatically converts plaintext into Base64.
- Secrets should never be committed with production credentials.
