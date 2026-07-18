🚀 Cloud-Native DevOps Pipeline with AWS EKS & Terraform

📌 Overview

Designed and implemented an end-to-end cloud-native DevOps pipeline on AWS, leveraging Terraform for infrastructure provisioning and GitHub Actions for CI/CD automation to deploy a containerised backend application on Kubernetes (EKS).

This project demonstrates real-world DevOps practices, including infrastructure automation, Kubernetes orchestration, and pipeline-driven deployments.

🏗️ Architecture

## Architecture

```mermaid
flowchart TB

    Developer[Developer]
    GitHub[GitHub Repository]
    Actions[GitHub Actions CI/CD]
    Terraform[Terraform]
    Manifests[Kubernetes Manifests]
    User[Internet User]

    Developer -->|Push code or manual trigger| GitHub
    GitHub --> Actions
    Actions -->|Terraform init plan apply| Terraform
    Actions -->|kubectl apply| Manifests

    subgraph AWS["AWS Cloud"]

        ECR["Amazon ECR<br/>Flask container image"]
        ControlPlane["Amazon EKS<br/>Managed control plane"]

        subgraph VPC["VPC 10.0.0.0/16"]

            IGW["Internet Gateway"]

            subgraph Public["Public Subnets<br/>eu-west-2a and eu-west-2b"]
                LB["AWS Load Balancer"]
                NAT["NAT Gateway"]
            end

            subgraph Private["Private Subnets<br/>eu-west-2a and eu-west-2b"]

                subgraph NodeGroup["EKS Managed Node Group"]
                    Node1["EC2 Worker Node 1"]
                    Node2["EC2 Worker Node 2"]
                    Pods["Flask Backend Pods<br/>3 replicas"]
                end

                Service["Kubernetes Service<br/>Type LoadBalancer<br/>Port 80 to 5000"]
            end
        end
    end

    Terraform -->|Creates AWS infrastructure| IGW
    Terraform -->|Creates EKS cluster| ControlPlane

    Manifests -->|Creates deployment and service| Service
    ControlPlane -->|Manages worker nodes| NodeGroup

    Node1 --> Pods
    Node2 --> Pods

    Node1 -->|Pulls image| ECR
    Node2 -->|Pulls image| ECR

    User -->|HTTP request| IGW
    IGW --> LB
    LB --> Service
    Service --> Pods

    Node1 -->|Outbound traffic| NAT
    Node2 -->|Outbound traffic| NAT
    NAT --> IGW
```
⚙️ Tech Stack
 
| Category      | Technology               |
| ------------- | ------------------------ |
| Cloud         | AWS (EKS, VPC, IAM, ELB) |
| IaC           | Terraform                |
| Container     | Docker                   |
| Orchestration | Kubernetes               |
| CI/CD         | GitHub Actions           |
| Backend       | Flask (Python)           |

🔄 CI/CD Pipeline

Implemented a two-stage CI/CD pipeline:

✅ CI (Continuous Integration)
    Terraform initialization (terraform init)
    Code formatting validation (terraform fmt)
    Configuration validation (terraform validate)

🚀 CD (Continuous Deployment - Manual Trigger)
    Infrastructure planning (terraform plan)
    Infrastructure provisioning (terraform apply)

    This ensures safe, controlled infrastructure changes while maintaining automation.

☁️ Infrastructure Provisioning

Provisioned entirely via Terraform:

    Custom VPC (10.0.0.0/16)
    Public & private subnets across AZs
    Amazon EKS cluster (nextwork-eks-cluster)
    Managed node group (t3.medium)
    IAM roles & policies for cluster access

🚢 Kubernetes Deployment

    Deployed Flask backend using Kubernetes manifests
    Exposed service using LoadBalancer
    Verified external access via AWS ELB endpoint

📸 Key Evidence

    ✅ EKS cluster active and healthy
    ✅ Worker nodes in Ready state
    ✅ Pods running successfully
    ✅ Service exposed via LoadBalancer
    ✅ API returning successful responses
    ✅ CI/CD pipeline execution (GitHub Actions)

🧠 Key Achievements

    Built a production-style DevOps pipeline from scratch
    Automated AWS infrastructure provisioning using Terraform
    Successfully deployed and exposed a Kubernetes-based application
    Resolved real-world issues (IAM auth, kubeconfig, service exposure)
    Integrated CI/CD workflows for infrastructure lifecycle management

🧹 Cleanup Strategy

    Infrastructure managed via Terraform for reproducibility
    Resources safely destroyed after validation to optimise cost

🔮 Future Enhancements

    Integrate Docker image build & push to ECR
    Add security scanning (e.g., Trivy) in CI pipeline
    Implement monitoring (Prometheus + Grafana)
    Use Helm for scalable Kubernetes deployments
    Introduce GitOps (ArgoCD)


