# Kubernetes Cluster Setup
---
# Prerequisites

Before you begin, make sure you have the following tools installed:

1. **kubectl** – A command-line tool for working with Kubernetes clusters. For more information, see [Installing or updating kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html).

2. **eksctl** – A command-line tool for working with EKS clusters that automates many individual tasks. For more information, see [Installing or updating eksctl](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html).

3. **AWS CLI** – A command-line tool for working with AWS services, including Amazon EKS. For more information, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the AWS Command Line Interface User Guide. After installing the AWS CLI, we recommend that you also configure it. For more information, see [Quick configuration](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config) with `aws configure` in the AWS Command Line Interface User Guide.

---

## Creating an EKS Cluster with Fargate

1. Create the cluster using `eksctl`:

   ```bash
   eksctl create cluster --name <cluster-name> --region us-east-1 fargate
   ```

2. Update your kubeconfig file:

   ```bash
   aws eks update-kubeconfig --name <cluster-name> --region us-east-1
   ```

3. Create a Fargate profile:
   ```bash
   eksctl create fargateprofile \
   --cluster <cluster-name> \
   --region us-east-1 \
   --name alb-sample-app \
   --namespace <namespace>
   ```

---

## Apply YAML Files for Deployment and Service

1. Apply the frontend deployment YAML:

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/Aadarshg087/GreenCure-ML/refs/heads/k8s_dep_env/frontend-eks-deployment.yaml
   ```

2. Apply the backend deployment YAML:

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/Aadarshg087/ML-Backend-Testing/refs/heads/main/backend-eks-deployment.yaml
   ```

3. Check the pods:
   ```bash
   kubectl get pods -n leaf
   ```

---

## Create IAM Policy for Ingress to Access AWS Resources

1. Download the IAM policy:

   ```bash
   curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
   ```

2. Create the IAM policy:

   ```bash
   aws iam create-policy \
   --policy-name AWSLoadBalancerControllerIAMPolicy \
   --policy-document file://iam_policy.json
   ```

3. Create the IAM service account for the load balancer controller:
   ```bash
   eksctl create iamserviceaccount \
   --cluster=<cluster-name> \
   --namespace=kube-system \
   --name=aws-load-balancer-controller \
   --role-name AmazonEKSLoadBalancerControllerRole \
   --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
   --approve
   ```

---

## Install the AWS Load Balancer Controller

1. Add the EKS Helm repository:

   ```bash
   helm repo add eks https://aws.github.io/eks-charts
   ```

2. Update the Helm repository:

   ```bash
   helm repo update eks
   ```

3. Install the AWS Load Balancer Controller:

   ```bash
   helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
   -n kube-system \
   --set clusterName=<your-cluster-name> \
   --set serviceAccount.create=false \
   --set serviceAccount.name=aws-load-balancer-controller \
   --set region=<region> \
   --set vpcId=<your-vpc-id>
   ```

4. Verify the deployment of the load balancer controller:
   ```bash
   kubectl get deployment -n kube-system aws-load-balancer-controller
   ```
