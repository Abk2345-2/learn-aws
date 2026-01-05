Awesome — **Phase 3 is where you become “production-grade”**.
This phase is exactly what you’ve already been touching (ECR, EKS, pods, image issues, NAT, logs), so I’ll structure it **cleanly, step-by-step, zero chaos**.

---

# ✅ Phase 3: Containers & Kubernetes on AWS (ECR + EKS) — TODO Checklist

⏱️ Target time: **2–3 weeks**
🎯 Goal: *You can design, deploy, debug, scale, and control containerized workloads on AWS like a real cloud engineer*

---

## 🧠 A. Container Mental Model (Before Touching AWS)

☐ Understand **what a container is**

* Process isolation, not a VM
* Shares host kernel

☐ Difference between:

* Docker image vs container
* ENTRYPOINT vs CMD
* Build-time vs runtime

☐ Understand **multi-arch images**

* `linux/amd64` vs `linux/arm64`
* Why EKS nodes fail with `no match for platform`

📌 You should explain:

> “Why my Mac image fails on EKS”

---

## 🐳 B. Docker Fundamentals (Must Be Fluent)

☐ Write a **clean Dockerfile**

* Small base image
* `.dockerignore`
* Layer ordering

☐ Build image:

```bash
docker build -t app:latest .
```

☐ Run locally:

```bash
docker run -p 8000:8000 app
```

☐ Inspect image size:

```bash
docker images
```

☐ Reduce image size:

* Slim base
* No build tools in runtime
* Multi-stage builds

📌 Target: **<300MB for Python apps**

---

## 🏗️ C. AWS ECR (Elastic Container Registry)

☐ Create ECR repository

```bash
aws ecr create-repository --repository-name my-app
```

☐ Authenticate correctly:

```bash
aws ecr get-login-password \
 | docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com
```

☐ Tag image properly:

```bash
docker tag app:latest <ECR_URI>:latest
```

☐ Push image:

```bash
docker push <ECR_URI>:latest
```

☐ List images:

```bash
aws ecr list-images --repository-name my-app
```

☐ Delete repo safely (when needed)

📌 You must know where ACCOUNT_ID comes from

---

## ☸️ D. Kubernetes Fundamentals (Local First)

☐ Understand core K8s objects:

* Pod
* Deployment
* Service
* ConfigMap
* Secret

☐ Understand:

* Desired vs current state
* Controllers
* Reconciliation loop

☐ Read YAML confidently

☐ Know:

* `imagePullPolicy`
* `restartPolicy`
* `replicas`

📌 Explain:

> “Why deleting a pod doesn’t kill the app”

---

## 🧪 E. EKS Cluster Creation (AWS Way)

☐ Create cluster with eksctl:

```bash
eksctl create cluster --name demo --region ap-southeast-2
```

☐ Understand what gets created:

* EKS control plane
* VPC
* Nodegroup
* IAM roles
* Security groups

☐ Update kubeconfig:

```bash
aws eks update-kubeconfig --name demo
```

☐ Verify:

```bash
kubectl get nodes
```

📌 Know which parts cost money

---

## 🧾 F. IAM & EKS Permissions (CRITICAL)

☐ Understand:

* EKS Cluster Role
* Nodegroup Role
* Pod IAM Role (IRSA)

☐ Attach ECR permissions to node role:

* `AmazonEC2ContainerRegistryReadOnly`

☐ Understand why pods fail with:

* `ErrImagePull`
* `403 Forbidden`

📌 IAM causes 70% of EKS failures

---

## 📦 G. Deploying Applications to EKS

☐ Create backend Deployment
☐ Create frontend Deployment
☐ Use **ECR images only**
☐ Set correct `imagePullPolicy`

☐ Expose services:

* ClusterIP (backend)
* LoadBalancer (frontend)

☐ Validate:

```bash
kubectl get pods
kubectl get svc
```

📌 You should predict pod status before running the command

---

## 🌐 H. Networking in Kubernetes (Very Important)

☐ Understand:

* Pod IP vs Service IP
* ClusterIP vs LoadBalancer
* DNS resolution in cluster

☐ Test service discovery:

```bash
curl http://backend:8000
```

☐ Debug:

* Connection refused
* 502 Bad Gateway
* DNS not resolving

📌 This explains your Nginx → backend errors

---

## 🔍 I. Debugging Like a Pro (Core Skill)

☐ Describe failing pods:

```bash
kubectl describe pod <pod>
```

☐ View logs:

```bash
kubectl logs pod/<pod>
kubectl logs deploy/<deploy>
```

☐ Exec into pod:

```bash
kubectl exec -it <pod> -- sh
```

☐ Identify:

* Image pull errors
* CrashLoopBackOff
* Env var misconfig

📌 Logs > guessing

---

## 🔐 J. Secrets & Config Management

☐ Move env vars out of YAML
☐ Create ConfigMap
☐ Create Secret
☐ Inject into pod

☐ Avoid hardcoding:

* DB URL
* API keys

📌 Explain:

> “Why my app works locally but not in EKS”

---

## 🗄️ K. Databases & Stateful Apps (Intro)

☐ Deploy PostgreSQL in K8s
☐ Use PVC + StorageClass
☐ Understand why pod was Pending

☐ Connect backend to DB via service name

☐ Learn why production DB ≠ in-cluster DB

📌 You already hit this pain — now you master it

---

## 📈 L. Scaling & Availability

☐ Scale deployment:

```bash
kubectl scale deploy backend --replicas=3
```

☐ Observe load balancing

☐ Understand:

* HPA (conceptually)
* Pod rescheduling

☐ Nodegroup scaling:

```bash
eksctl scale nodegroup --nodes 0
```

📌 Understand why EC2 may still appear running

---

## 💰 M. Cost Control (Real-World Survival)

☐ Identify cost drivers:

* EC2 nodes
* NAT Gateway
* LoadBalancer
* EBS volumes

☐ Pause cluster safely:

* Scale nodegroups to 0
* Delete LB services
* Remove NAT if unused

☐ Verify with AWS CLI

📌 Cost awareness = senior skill

---

## 🔥 N. Production-Level Hygiene

☐ Readiness & liveness probes
☐ Resource requests & limits
☐ Logs go to stdout
☐ No secrets in images
☐ Immutable deployments

📌 This separates toy projects from real ones

---

## 🧪 O. Phase 3 Capstone (Mandatory)

☐ Build multi-arch images
☐ Push to ECR
☐ Deploy backend + frontend
☐ Secure env vars
☐ Debug at least **5 failures**
☐ Access app via ALB
☐ Scale nodes to 0 safely

📌 If you can break and fix → you’re solid

---

## 🎯 Phase 3 Exit Criteria

✅ You are confident with EKS
✅ You can debug blindly
✅ You understand IAM + networking
✅ You control costs
✅ You can explain **why things fail**

---

## 🔜 What Comes After Phase 3?

👉 **Phase 4: Observability**

* CloudWatch
* Prometheus
* Grafana
* Logs & metrics

👉 **Phase 5: IaC**

* Terraform
* GitOps
* CI/CD pipelines

If you want, next I can:

* Create **Phase 3 daily schedule**
* Add **real failure labs**
* Convert this into **resume-ready projects**
* Map Phase 3 to **AWS SAA / DevOps exam**

You’re doing *real cloud engineering now* 🚀
