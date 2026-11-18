# 03 — GitHub SSH Access

## Objective

Push lab work from the VM to GitHub without passwords or tokens.

## Steps

```bash
ssh-keygen -t ed25519 -C "<github-email>"     # accept the default path, set a passphrase
cat ~/.ssh/id_ed25519.pub                     # copy this public key
```

Add the key in GitHub → **Settings → SSH and GPG keys → New SSH key**.

## Verification

```bash
ssh -T git@github.com
```

Expected output (with your own username):

```text
Hi <github-username>! You've successfully authenticated, but GitHub does not provide shell access.
```

Then clone over SSH:

```bash
git clone git@github.com:NookireddiDileep/kubernetes-labs.git
```

## Notes

- Only the **public** key (`.pub`) ever leaves the VM.
- If `ssh -T` says `Permission denied (publickey)`, see [troubleshooting](troubleshooting.md#issue-1-github-ssh-authentication-failed).
