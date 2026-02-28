# Adding New Domains and TLS Certificates to Envoy Gateway

## Current Setup

- **Gateway:** `eg` in `envoy-gateway-system` namespace

---

## Step 1: Create TLS Secret

Save as `secret.yaml`:

```bash
# Replace with your domain name and cert/key file names
apiVersion: v1
kind: Secret
metadata:
  name: new-domain-cert
  namespace: envoy-gateway-system
type: kubernetes.io/tls
data:
  tls.crt: <base64-encoded-cert>
  tls.key: <base64-encoded-key>
```

Or create directly via command:

```bash
kubectl create secret tls new-domain-cert \
  --cert=fullchain.pem \
  --key=privkey.pem \
  -n envoy-gateway-system
```

---

## Step 2: Update Gateway

Save as `gateway.yaml`:

```bash
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: eg
  namespace: envoy-gateway-system
spec:
  gatewayClassName: eg
  listeners:
  - allowedRoutes:
      namespaces:
        from: All
    name: http
    port: 80
    protocol: HTTP
  - allowedRoutes:
      namespaces:
        from: All
    name: https
    port: 443
    protocol: HTTPS
    tls:
      certificateRefs:
      - kind: Secret
        name: oslrnd-com
      - kind: Secret
        name: osl-team
      - kind: Secret
        name: new-domain-cert    # add new certs here
      mode: Terminate
```

Apply:

```bash
kubectl apply -f gateway.yaml
```

---

---

## Step 3: Create HTTPRoute

Save as `httproute.yaml`:

```bash
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: my-app-route
  namespace: my-app-namespace
spec:
  parentRefs:
  - name: eg
    namespace: envoy-gateway-system
    sectionName: https
  hostnames:
  - "myapp.newdomain.com"
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: my-app-service
      port: 80
```

Apply:

```bash
kubectl apply -f httproute.yaml
```

---

## Step 4: Update DNS

Add an A record pointing your domain to the Gateway IP:

```bash
myapp.newdomain.com  →  192.168.6.19
```

---

## Step 5: Verify

```bash
# Check Gateway is healthy
kubectl get gateway eg -n envoy-gateway-system

# Check HTTPRoute is attached
kubectl get httproute -A

# Test TLS cert (use --resolve to send correct SNI)
curl -vk --resolve myapp.newdomain.com:443:192.168.6.19 https://myapp.newdomain.com/
```

---

## Important Notes

- HTTPRoute `port` must match the **Service port**, not the container port
- Always include `namespace: envoy-gateway-system` in HTTPRoute `parentRefs`
- Always use `sectionName: https` in HTTPRoute `parentRefs`
- Envoy uses SNI to automatically serve the correct certificate
- Use `-resolve` flag with curl to test SNI-based cert selection
- When testing with `curl -H "Host: ..."` against the IP directly, Envoy serves the default cert (this is normal)
