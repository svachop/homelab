# 1Password Connect Deployment

## Prerequisites

Before ArgoCD can deploy this chart, you must manually create the credentials secret:

```bash
# Create the secret from your credentials file
kubectl create secret generic onepassword-credentials \
  --from-file=1password-credentials.json=/Users/svachop/HomeLab/Praha/1Password/1password-credentials.json \
  --namespace onepassword-connect \
  --dry-run=client -o yaml | kubectl apply -f -
```

**Important:** The namespace must exist first. You can create it with:
```bash
kubectl create namespace onepassword-connect
```

Or apply the namespace template:
```bash
kubectl apply -f templates/namespace.yaml
```

## Verification

Verify the secret was created:
```bash
kubectl -n onepassword-connect get secret onepassword-credentials
```

## Deployment

Once the secret exists, ArgoCD will automatically deploy this chart based on the app-of-apps configuration.
