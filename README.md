**Istio Installation Guide** 
---
This guide walks you through installing **Istio** on any standard Kubernetes cluster (GKE, EKS, AKS, Minikube, OpenShift, etc.) using the `istioctl` CLI.

---

## 🧩 Prerequisites

- A working Kubernetes cluster
- `kubectl` configured to access the cluster
- Optional: `helm` (for advanced setups)

## 🔹 Step 1: Download Istio CLI
use my repo 
```
git clone https://github.com/ckadarla/istio.git
cd istio/istio-1.25.1
export PATH=$PWD/bin:$PATH

```
or 
```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-*
export PATH=$PWD/bin:$PATH
```

Verify Istio CLI:

```
istioctl version

```


## 🔹 Step 2: Install Istio with Default Profile

```bash
istioctl install --set profile=demo -y
```

> **Available profiles**:
> - `demo` - Full-featured (for dev/testing)
> - `default` - Lightweight (for production)
> - `minimal` - Control plane only
> - `remote` / `external` - Multi-cluster setups

---

## 🔹 Step 3: Enable Sidecar Injection

Label the namespace you want to inject Envoy sidecars into:

```bash
kubectl label namespace default istio-injection=enabled
```

> Replace `default` with your actual app namespace if needed.


## 🔹 Step 4: Deploy the Sample App (Optional)

Deploy the Bookinfo sample application:

```bash
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

Check status:

```bash
kubectl get pods
```


## 🔹 Step 5: Expose App Using Istio Gateway

```bash
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
```

Get external IP:

```bash
kubectl get svc istio-ingressgateway -n istio-system
```

Access the app:

```bash
http://<EXTERNAL-IP>/productpage
```


## 🔹 Step 6: Verify the Installation

```bash
istioctl verify-install
```


## 🔹 Step 7: Access Kiali Dashboard (Optional)

Install observability tools:

```bash
kubectl apply -f samples/addons
kubectl rollout status deployment/kiali -n istio-system
```

Port forward to Kiali UI:

```bash
kubectl port-forward svc/kiali -n istio-system 20001:20001
```

Visit in your browser:

```
http://localhost:20001
```

---

## 🧹 Uninstall Istio

To uninstall Istio:

```bash
istioctl uninstall --purge
kubectl delete namespace istio-system
kubectl label namespace default istio-injection-
```

---
## ⚠️ Notes for OpenShift Users

- Grant required SCC permissions:
  ```bash
  oc adm policy add-scc-to-group anyuid system:serviceaccounts:istio-system
  oc adm policy add-scc-to-user privileged -z istio-ingressgateway -n istio-system
  ```
- Use OpenShift `Route` instead of `Ingress`.

---

## 📎 References

- [Istio Official Docs](https://istio.io/latest/docs/)
- [Bookinfo Sample](https://istio.io/latest/docs/examples/bookinfo/)
- [Kiali Dashboard](https://kiali.io/)

