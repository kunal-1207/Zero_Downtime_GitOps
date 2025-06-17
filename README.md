# 🚀 Zero Downtime GitOps with ArgoCD & Argo Rollouts - Simplified Guide

[![Argo CD](https://img.shields.io/badge/GitOps-ArgoCD-blue?logo=argo)](https://argo-cd.readthedocs.io)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Production--Ready-326CE5?logo=kubernetes)](https://kubernetes.io/)
[![Redis](https://img.shields.io/badge/Redis-Session%20Store-DC382D?logo=redis)](https://redis.io/)
[![Prometheus](https://img.shields.io/badge/Monitoring-Prometheus-orange?logo=prometheus)](https://prometheus.io/)
[![Istio](https://img.shields.io/badge/ServiceMesh-Istio-blue?logo=istio)](https://istio.io/)

## 🌟 Introduction

This project demonstrates how to implement **zero-downtime deployments** in Kubernetes using modern GitOps tools:

- **ArgoCD**: Keeps your cluster in sync with your Git repository
- **Argo Rollouts**: Manages advanced deployment strategies
- **Istio**: Handles intelligent traffic routing
- **Prometheus**: Monitors your application health

Perfect for production environments where uptime is critical!

---

## 📂 Project Structure Explained

```bash
.
├── argo/                       # All ArgoCD-related configurations
│   ├── applications/           # Defines what apps ArgoCD should manage
│   ├── blue_green/            # Blue-Green deployment strategy files
│   │   ├── rollout.yaml       # Defines how the rollout should progress
│   │   └── db_job.yaml        # Database migration job (runs during deployment)
│   ├── canary/                # Canary deployment strategy files
│   └── revert/                # Auto-rollback configurations
├── istio/                     # Traffic management configurations
└── prometheus/                # Alerting rules for monitoring
```

---

## 🛠️ Prerequisites

Before you begin, make sure you have:

1. A running Kubernetes cluster (Minikube, EKS, GKE, etc.)
2. `kubectl` configured to access your cluster
3. Basic understanding of Kubernetes concepts

---

## 🚀 Step-by-Step Deployment Guide

### 1. Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Access the ArgoCD Dashboard

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Now open [http://localhost:8080](http://localhost:8080) in your browser.

**First-time login:**
- Username: `admin`
- Password: Get it with:
  ```bash
  kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
  ```

### 3. Install Argo Rollouts

```bash
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```

### 4. Install the Argo Rollouts kubectl plugin

```bash
curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64
chmod +x kubectl-argo-rollouts-linux-amd64
sudo mv kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts
```

### 5. Deploy Your Application

In the ArgoCD UI:
1. Click "New App"
2. Fill in:
   - Application Name: `my-app`
   - Project: `default`
   - Sync Policy: `Automatic`
   - Repository URL: `https://github.com/kunal-1207/Zero_Downtime_GitOps`
   - Path: `argo/blue_green` (or `argo/canary` for canary deployment)
3. Click "Create"

### 6. Monitor Your Deployment

Watch the rollout progress:

```bash
kubectl argo rollouts get rollout app-rollout -n default --watch
```

---

## 🎯 Key Concepts Explained

### 🔵🔴 Blue-Green Deployment
- Maintains two identical environments (Blue and Green)
- Only one serves live traffic at a time
- Switch traffic after new version is fully deployed
- Zero downtime during switch

### 🐦 Canary Deployment
- Gradually shifts traffic to new version
- Start with small percentage (e.g., 5%)
- Increase gradually if metrics look good
- Minimizes impact of bad deployments

### 🔄 GitOps Workflow
1. Developers push code to Git
2. ArgoCD detects changes
3. New version deploys using chosen strategy
4. Automated health checks validate deployment
5. Traffic shifts when verified

---

## 🚨 Common Issues & Fixes

| Symptom | Likely Cause | Solution |
|---------|--------------|----------|
| ArgoCD login fails | Incorrect password | Use `kubectl -n argocd get secret` to retrieve password |
| Rollout stuck | Health checks failing | Check pod logs with `kubectl logs <pod-name>` |
| Sync issues | Wrong repository path | Verify path in ArgoCD application settings |
| Traffic not switching | Istio not properly configured | Check VirtualService and DestinationRule |

---

## 📈 Monitoring & Alerts

The project includes Prometheus alert rules that can:
- Detect failed deployments
- Monitor application health
- Trigger automatic rollbacks if metrics degrade

View alerts with:
```bash
kubectl port-forward svc/prometheus-server 9090:80
```
Then visit [http://localhost:9090/alerts](http://localhost:9090/alerts)

---

## 🏆 Best Practices

1. **Start with Blue-Green** - Simpler to understand
2. **Add Canary Later** - When you need more granular control
3. **Monitor Key Metrics** - Error rates, latency, CPU usage
4. **Small Frequent Deploys** - Reduces risk per deployment
5. **Automate Rollbacks** - Configure Prometheus alerts to trigger rollbacks

---

## 📚 Learning Resources

- [ArgoCD Documentation](https://argo-cd.readthedocs.io)
- [Argo Rollouts Tutorial](https://argoproj.github.io/argo-rollouts/)
- [Istio Traffic Management](https://istio.io/latest/docs/tasks/traffic-management/)

---

## 💡 About the Author

**Kunal Waghmare**  
Cloud Native Engineer | DevOps Enthusiast  
🔗 [GitHub: @kunal-1207](https://github.com/kunal-1207)  
📧 [Email Me](mailto:kunalwaghmare1207@gmail.com.com)

---

## 📜 License

This project is open-source under the [MIT License](https://opensource.org/licenses/MIT).
