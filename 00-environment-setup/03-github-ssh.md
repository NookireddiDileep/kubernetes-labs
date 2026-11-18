# GitHub SSH Configuration

## Objective

Configure passwordless Git authentication using SSH.

## Steps

1. Generate SSH key
2. Copy public key
3. Add key to GitHub
4. Verify SSH connectivity

## Verification

```bash
ssh -T git@github.com
```

Expected:

```text
Hi dileepnookireddi!
You've successfully authenticated,
but GitHub does not provide shell access.
```

## Result

GitHub authentication completed successfully.
