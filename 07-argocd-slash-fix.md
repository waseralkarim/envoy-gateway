cat <<EOF | kubectl apply -f -
apiVersion: gateway.envoyproxy.io/v1alpha1
kind: ClientTrafficPolicy
metadata:
  name: argocd-keep-slashes
  namespace: envoy-gateway-system
spec:
  path:
    escapedSlashesAction: KeepUnchanged
  targetRefs:
  - group: gateway.networking.k8s.io
    kind: Gateway
    name: eg
EOF
