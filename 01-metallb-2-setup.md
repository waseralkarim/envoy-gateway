# Install Metallb-2

**Steps 1: Enable strict ARP mode**

```bash
kubectl edit configmap -n kube-system kube-proxy
```

```
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
mode: "ipvs"
ipvs:
  strictARP: true
```

**Steps 2: Install MetalLB CRD & Controller using the official manifests by MetalLB**

[Reference](https://metallb.universe.tf/installation/)

```jsx
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.12/config/manifests/metallb-native.yaml
```

**Steps 3: Layer 2 Configuration for to advertise the IP Pool**

[Reference](https://metallb.universe.tf/configuration/#layer-2-configuration)

```jsx
vim metallb-ipadd-pool.yaml
```

```
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.240-192.168.1.250
```

```jsx
kubectl apply -f metallb-ipadd-pool.yaml
```

**Steps 4: Advertise the IP Address Pool**

```jsx
vim metallb-pool-advertise.yaml
```

```
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: example
  namespace: metallb-system
spec:
  ipAddressPools:
  - first-pool
```

```jsx
kubectl apply -f metallb-pool-advertise.yaml
```
