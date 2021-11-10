# meetup-argo-stack

## Hack (reusing secrets)

**If you don't have encryption keys yet, go to the bottom section of the README (Preparing encryption keys)**

```
export PRIVATEKEY=".hack/pre-seed-tls.key"
export PUBLICKEY=".hack/pre-seed-tls.crt"
export NAMESPACE="kube-system"
export SECRETNAME="pre-seed-customkeys"

kubectl -n "$NAMESPACE" create secret tls "$SECRETNAME" \
    --cert="$PUBLICKEY" \
    --key="$PRIVATEKEY"
kubectl -n "$NAMESPACE" label secret "$SECRETNAME" \
    sealedsecrets.bitnami.com/sealed-secrets-key=active
```

## Bootstrap
```
export KUBECONFIG=~/dev/meetup/civo-meetup-kubeconfig

# Bootstrap ArgoCD
kustomize build kustomize/argo-cd | kubectl apply -f -

# Get ArgoCD Server admin password
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}"| base64 -d && echo

kubectl apply -f kustomize/argo-cd-meta-project/meta-app-project.yaml
kubectl apply -f kustomize/argo-cd-meta-project/meta-app.yaml

# Get Argo Server token
SECRET=$(kubectl -n argo get sa argo-server -o=jsonpath='{.secrets[0].name}')
echo "Bearer $(kubectl -n argo get secret ${SECRET} -o=jsonpath='{.data.token}' | base64 --decode)"
```

## Preparing encryption keys and generating sealed secrets for the first time

### Encryption keypair

```
export PRIVATEKEY=".hack/pre-seed-tls.key"
export PUBLICKEY=".hack/pre-seed-tls.crt"
export NAMESPACE="kube-system"
export SECRETNAME="pre-seed-customkeys"

mkdir .hack

openssl req -x509 -nodes \
    -newkey rsa:4096 \
    -keyout "$PRIVATEKEY" \
    -out "$PUBLICKEY" \
    -subj "/CN=sealed-secret/O=sealed-secret"

kubectl -n "$NAMESPACE" create secret tls "$SECRETNAME" \
    --cert="$PUBLICKEY" \
    --key="$PRIVATEKEY"
kubectl -n "$NAMESPACE" label secret "$SECRETNAME" \
    sealedsecrets.bitnami.com/sealed-secrets-key=active
```

### Sealed secrets

```
```


