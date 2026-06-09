# **🧠 Mastery Plan: AWS, Kubernetes, and ArgoCD (30 Days)**

  

A structured roadmap to help Omar Crosby master AWS, Kubernetes, and ArgoCD in 30 days.

---

## **📅 High-Level Structure**

|**Phase**|**Focus Area**|**Duration**|**Goal**|
|---|---|---|---|
|Phase 1|AWS Foundations|Days 1–10|Understand and deploy compute, network, IAM basics|
|Phase 2|Kubernetes Core|Days 11–20|Deploy, manage, and observe K8s apps|
|Phase 3|ArgoCD & GitOps|Days 21–30|GitOps workflows with K8s and ArgoCD|

---

## **🔹 Phase 1: AWS Core (Days 1–10)**

|**Day**|**Focus**|**Outcome**|
|---|---|---|
|1|What is AWS? Free tier, billing, global infra|Understand core services & regions/zones|
|2|IAM: Users, Roles, Policies|Set up least-privilege access|
|3|EC2: Launch Linux VM|SSH into VM, install tools|
|4|VPC: Subnets, IGW, routing|Understand private/public subnets|
|5|S3: Buckets, policies, lifecycle|Upload/download/manage data|
|6|Security Groups, NACLs|Network access control basics|
|7|CloudFormation basics|Intro to infrastructure as code (IaC)|
|8|EKS (Kubernetes on AWS) Overview|Understand managed K8s setup|
|9|Route 53 + ELB|DNS, health checks, load balancer configuration|
|10|Recap with Hands-on Project|Deploy an EC2-based app with a public IP|

---

## **🔹 Phase 2: Kubernetes Fundamentals (Days 11–20)**

|**Day**|**Focus**|**Outcome**|
|---|---|---|
|11|K8s Concepts: Pods, Nodes, Clusters|Core vocabulary of Kubernetes|
|12|Install kubectl, Minikube/Kind|Local K8s cluster working|
|13|Pods and Deployments|Create/manage resources via YAML|
|14|Services: ClusterIP, NodePort, LoadBalancer|Internal and external networking|
|15|Volumes & ConfigMaps|Inject configs and persistent storage|
|16|Namespaces & RBAC|Multi-tenancy & access control|
|17|Helm basics|Install apps like NGINX/Ingress with Helm|
|18|Troubleshooting: Logs, describe, events|Learn how to debug|
|19|Health checks: readiness/liveness|Improve app resilience|
|20|Recap with Mini Project|Build and expose a K8s-hosted web service|

---

## **🔹 Phase 3: ArgoCD + GitOps (Days 21–30)**

|**Day**|**Focus**|**Outcome**|
|---|---|---|
|21|What is GitOps? ArgoCD Overview|Understand the declarative workflow|
|22|Install ArgoCD|Setup in local or EKS cluster|
|23|Login & UI Overview|Interact with the web dashboard & CLI|
|24|Deploy first App (via Git)|Deploy app from Git repo|
|25|Sync, Diff, and Rollbacks|Learn how ArgoCD keeps cluster in sync|
|26|Auto-sync vs Manual|Fine-tune your deployment strategy|
|27|Helm & Kustomize in ArgoCD|Real-world templating support|
|28|ApplicationSets|Manage many apps at once|
|29|RBAC, SSO, and ArgoCD Projects|Secure and isolate team usage|
|30|Capstone Project|GitOps pipeline: Git → ArgoCD → K8s on AWS|

---

## **⏳ Daily Commitment**

- 📚 **20–30 min** learning (docs, tutorials, video)
    
- 🧪 **20–30 min** hands-on work (terminal, YAML, dashboard)
    
- 🧘 **Reflect**: What worked? What confused you? Write it down.
    

  

## **✅ Tips for Success**

- Use **Playgrounds** like Katacoda, Play-with-K8s, or CloudShell to avoid wasting time on setup.
    
- Track your learning in a GitHub repo, Notion, or Obsidian vault.
    
- Expect to be confused. **Deploy something broken, then fix it**.
    
- Repetition = clarity. Stick with the dailies!
    

---

Let me know if you’d like a printable version, daily task reminders, or the capstone project broken down further!