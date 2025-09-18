# Cert-Manager: Stuck CertificateRequest 📝

## Context
Namespace: `twenty`  
Cert-manager created a `CertificateRequest` which stayed in `Approved=True` but the TLS secret was never created.

## Symptoms
```bash
kubectl -n twenty get certificaterequest
NAME                APPROVED   READY   ISSUER             AGE
twenty-n8s-tls-1    True       True    letsencrypt-prod   30s
```

But:

```bash
kubectl -n twenty get secret twenty-n8s-tls
Error from server (NotFound): secrets "twenty-n8s-tls" not found
```

## Investigation
Checked cert-manager logs:
```bash
kubectl -n cert-manager logs deploy/cert-manager | grep twenty-n8s
E0918 12:04:21 ... "Failed to finalize order: context deadline exceeded"
```

## Fix
1. Deleted stuck request:
   ```bash
   kubectl -n twenty delete certificaterequest twenty-n8s-tls-1
   ```
2. Re-applied certificate.  
3. New request succeeded → secret issued.

## Lesson learned
Always check `CertificateRequest` + cert-manager logs.  
Deleting + re-issuing is faster than chasing phantom states.
