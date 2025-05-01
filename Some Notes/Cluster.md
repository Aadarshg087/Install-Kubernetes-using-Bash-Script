## Manual Kubernetes Cluster Setup

### Update System

```bash
sudo apt update
```

---

### On Master Node

```bash
wget https://raw.githubusercontent.com/Aadarshg087/Install-Kubernetes-using-Bash-Script/refs/heads/main/masterSC.sh
bash masterSC.sh
```

### Important:

After running the master script:

- Copy the **IP address**, **token**, and **hash** from the output.
- You’ll need them to join worker nodes.

---

### On Worker Node

```bash
wget https://raw.githubusercontent.com/Aadarshg087/Install-Kubernetes-using-Bash-Script/refs/heads/main/workerSC.sh
bash workerSC.sh
```

After running:

- Copy the **kubeadm join** command shown.
- Replace placeholders (**IP address**, **token**, **hash**) with the ones you got from the master node.
- Run the updated command on the worker.

---

## Sample Application (Deployment + Service)

### `app.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx # (Must match labels below in the template)
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx # (Must match selector labels above)
    spec:
      containers:
        - name: nginx
          image: nginx:1.14.2 # (Change to your desired image)
          ports:
            - containerPort: 80 # (Ensure port matches service targetPort)
---
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: nginx # (MUST match Deployment labels correctly)
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80 # (Must align with containerPort or app logic)
```

---

## Apply the YAML

```bash
kubectl apply -f app.yaml
```

---

## Notes:

- **Highlight the label matching**:  
  In `Deployment`, the `matchLabels` under `selector` must match the `labels` inside `template`.  
  Similarly, the `selector` in the `Service` must match the app's deployment labels — else service won’t find pods.

- **Usual YAML Changes** (50 words max):  
  Change the image version, number of replicas, container ports, service ports, service type (NodePort/LoadBalancer/ClusterIP), and the matching labels. Labels must always align between deployment and service.

---

## Access the Application

- Edit the **Security Groups** of your EC2 instances to allow the NodePort port range (30000–32767).
- Hit the **public IP** of your node + **NodePort** to access your app.

---

# End of Setup
