# Install Kubernetes using Bash Script

## Prerequisites

- Ubuntu OS (Xenial or later)
- t2.medium instance type or higher (Make sure it has at least 2 vCPUs)

## Setting up Security Groups

1. **Go to the EC2 Dashboard:** Navigate to the EC2 Dashboard in the AWS Management Console.

2. **Create a Security Group:**

   - In the left menu under _Network & Security_, click on _Security Groups_.
   - Click on _Create Security Group_.
   - Provide the following details:
     - **Name:** (e.g., `Kubernetes-Cluster-SG`)
     - **Description:** A brief description for the security group (mandatory)
     - **VPC:** Select the appropriate VPC for your instances (default is acceptable)

3. **Allow SSH Traffic (Port 22):**

   - **Type:** SSH
   - **Port Range:** 22
   - **Source:** `0.0.0.0/0` (Anywhere) or your specific IP address/range

4. **Allow Kubernetes API Traffic (Port 6443):**

   - **Type:** Custom TCP
   - **Port Range:** 6443
   - **Source:** `0.0.0.0/0` (Anywhere) or specific IP ranges

5. **Create Security Group:** Click on _Create Security Group_ to save the settings.

### AWS Setup

- Ensure that all instances are in the same Security Group (`Kubernetes-Cluster-SG`).
- Expose port 6443 in the Security Group to allow worker nodes to join the cluster.
- Expose port 22 in the Security Group to allow SSH access to manage the instances.

**Launching EC2 Instances:**

1. Under _Configure Security Group_, select the existing security group (`Kubernetes-Cluster-SG`).
2. Alternatively, you can add the instance to the security group after creation:
   - Select instance → _Networking_ → Select in _Network Interfaces_ → _Actions_ (Top Right) → _Change security groups_ → Select and add security group.

## Installing the Scripts

**Master Node:**

```bash
wget [https://raw.githubusercontent.com/Aadarshg087/Install-Kubernetes-using-Bash-Script/refs/heads/main/masterSC.sh](https://raw.githubusercontent.com/Aadarshg087/Install-Kubernetes-using-Bash-Script/refs/heads/main/masterSC.sh)
```
