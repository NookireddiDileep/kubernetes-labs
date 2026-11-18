# 01 — GCP Project, Billing and Budget

## Objective

A dedicated Google Cloud project for the labs, so costs, permissions and clean-up stay separate from anything else.

## Steps

1. **Create a project** (Console → IAM & Admin → Create project, or CLI):

   ```bash
   gcloud projects create <project-id> --name="kubernetes-labs"
   gcloud config set project <project-id>
   ```

2. **Link billing** (Console → Billing → link the project to a billing account).
3. **Enable the Compute Engine API:**

   ```bash
   gcloud services enable compute.googleapis.com
   ```

4. **Set a budget alert** (Console → Billing → Budgets & alerts) with email alerts at 50%, 90% and 100% of a monthly amount you are happy to spend.
5. **Set the default region:**

   ```bash
   gcloud config set compute/region asia-south1
   ```

## Verification

```bash
gcloud config list                 # account, project, region
gcloud services list --enabled | grep compute
```

## Notes

- The project ID is not published here; use your own.
- Deleting the project is the fastest full clean-up: it removes the VM, disk and firewall rules together.
