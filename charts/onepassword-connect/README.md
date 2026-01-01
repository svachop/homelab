# 1Password Connect Deployment

## Prerequisites

Before ArgoCD can deploy this chart, you must manually create the credentials secret.

The credentials file must be base64-encoded in the secret:

```bash
# Create the secret with base64-encoded credentials
kubectl create secret generic onepassword-credentials \
  --from-literal=1password-credentials.json=$(base64 -i /Users/svachop/HomeLab/Praha/1Password/1password-credentials.json) \
  --namespace onepassword-connect
```

Or if you already created it with `--from-file`, delete and recreate it:

```bash
# Delete the existing secret
kubectl delete secret onepassword-credentials -n onepassword-connect

# Create with base64-encoded content
kubectl create secret generic onepassword-credentials \
  --from-literal=1password-credentials.json=$(base64 -i /Users/svachop/HomeLab/Praha/1Password/1password-credentials.json) \
  --namespace onepassword-connect
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
