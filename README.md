# meetup-argo-stack

```
export KUBECONFIG=~/dev/meetup/civo-meetup-kubeconfig

#
kustomize build kustomize/argo-cd | kubectl apply -f -

kubectl get secret -n argo-cd argocd-initial-admin-secret -o jsonpath="{.data.password}"| base64 -d && echo
```