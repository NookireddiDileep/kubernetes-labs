# Troubleshooting

## Issue 1

### Problem

GitHub SSH authentication failed.

### Error

Permission denied (publickey).

### Root Cause

Public SSH key was not added to GitHub.

### Resolution

Added SSH public key under:

GitHub → Settings → SSH and GPG Keys

### Status

Resolved

---

## Issue 2

### Problem

Git commit failed.

### Error

Author identity unknown.

### Root Cause

Git user.name and user.email were not configured.

### Resolution

Configured global Git identity.

### Status

Resolved
