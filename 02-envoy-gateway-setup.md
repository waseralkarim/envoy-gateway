# **Helm From Apt (Debian/Ubuntu)**

Official Page: https://helm.sh/docs/intro/install/

```bash
sudo apt-get install curl gpg apt-transport-https --yes
curl -fsSL https://packages.buildkite.com/helm-linux/helm-debian/gpgkey | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/helm.gpg] https://packages.buildkite.com/helm-linux/helm-debian/any/ any main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

## **Install with Helm**

Install the Gateway API CRDs and Envoy Gateway:

```bash
helm install eg oci://docker.io/envoyproxy/gateway-helm --version v1.6.3 -n envoy-gateway-system --create-namespace
```
