# 🚀 Zero Downtime GitOps with ArgoCD & Argo Rollouts

[![Argo CD](https://img.shields.io/badge/GitOps-ArgoCD-blue?logo=argo)](https://argo-cd.readthedocs.io)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Production--Ready-326CE5?logo=kubernetes)](https://kubernetes.io/)
[![Redis](https://img.shields.io/badge/Redis-Session%20Store-DC382D?logo=redis)](https://redis.io/)
[![Prometheus](https://img.shields.io/badge/Monitoring-Prometheus-orange?logo=prometheus)](https://prometheus.io/)
[![Istio](https://img.shields.io/badge/ServiceMesh-Istio-blue?logo=istio)](https://istio.io/)

This project demonstrates a **GitOps workflow** using **ArgoCD**, **Argo Rollouts**, and **progressive delivery strategies** (Blue-Green and Canary) for achieving **zero downtime deployments** in Kubernetes.

---

## 📁 Directory Structure

```bash
.
├── argo
│   ├── applications              # ArgoCD application definitions
│   │   └── app.yaml
│   ├── blue_green                # Blue-Green deployment rollout & job
│   │   ├── rollout.yaml
│   │   └── db_job.yaml
│   ├── canary                   # Canary rollout spec
│   │   └── rollout.yaml
│   └── revert                   # Prometheus automation
│       └── prometheus_revert_automation.yaml
├── istio                        # Istio service mesh configs
│   ├── destination_rule.yaml
│   └── virtual_service.yaml
└── prometheus                   # Alerting rules
    └── alert_rules.yaml
````

---

## ⚙️ Technologies Used

* **ArgoCD** – GitOps continuous delivery
* **Argo Rollouts** – Blue-Green and Canary deployments
* **Istio** – Service mesh for advanced traffic routing
* **Redis** – Used internally by ArgoCD
* **Prometheus** – Alerting and monitoring
* **Kubernetes** – Orchestration platform

---

## 🚀 How to Deploy

1. **Install ArgoCD** on your cluster
   Follow: [https://argo-cd.readthedocs.io/en/stable/getting\_started/](https://argo-cd.readthedocs.io/en/stable/getting_started/)

2. **Port-forward ArgoCD dashboard**:

   ```powershell
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```

   Access via [https://localhost:8080](https://localhost:8080)

3. **Login using CLI (optional):**

   ```bash
   argocd login localhost:8080 --username admin --password <your-password> --insecure
   ```

4. **Create the Application:**
   In the ArgoCD UI:

   * Repository URL: `https://github.com/kunal-1207/Zero_Downtime_GitOps`
   * Path: `argo/blue_green`
   * Namespace: `default`
   * Sync Policy: Manual or Auto

5. **Sync the Application**

   After creating the app, click **Sync** to deploy.

6. **Watch the rollout:**

   ```powershell
   kubectl argo rollouts get rollout app-rollout -n default --watch
   ```

---

## 🛠️ Problems Faced & Solutions

| Problem                                       | Cause                                        | Solution                                                                          |
| --------------------------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------- |
| `argocd-redis` authentication errors          | Secret was missing or wrong                  | Recreated with correct password: `kubectl create secret generic argocd-redis ...` |
| `localhost sent an invalid response`          | Accessing ArgoCD over HTTPS via port-forward | Use `http://localhost:8080` instead of HTTPS                                      |
| Jenkins UI showing on Argo port               | Port conflict                                | Verified that Jenkins was also using port 8080. Changed Jenkins or Argo port.     |
| ArgoCD App Sync shows **Missing / OutOfSync** | Repo path was incorrect                      | Corrected path to `argo/blue_green` (not `k8s/`)                                  |
| `alert_rules.yaml` caused sync failure        | Prometheus CRD not installed                 | Installed Prometheus stack or removed Prometheus folder for testing               |
| `Rollout:app-rollout not found`               | Resource not yet deployed                    | Synced ArgoCD again after ensuring rollout YAML is correct                        |

---

## 📌 Future Improvements

* Add ArgoCD auto-sync policies
* Integrate GitHub Actions for PR-based rollout promotion
* Add custom rollback automation via Prometheus + K8s job

---

## 🧠 Author

**Kunal Waghmare**
🔗 [GitHub: @kunal-1207](https://github.com/kunal-1207)

---

## 📜 License

This project is licensed under the MIT License.

```

