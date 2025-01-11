# Deploying WordPress and MySQL on Kubernetes

This guide explains how to deploy WordPress and MySQL on a Kubernetes cluster using various manifests such as deployments, services, persistent volumes, and ingress. Follow the commands below step-by-step.

---

## Prerequisites
1. Kubernetes cluster up and running.
2. `kubectl` installed and configured to access your cluster.
3. Nginx Ingress Controller installed.
4. DNS configuration for your domain (e.g., `burhan.com`) pointing to the Ingress Controller's external IP.

---

## Namespace Creation
Create a dedicated namespace for WordPress and MySQL:
```bash
kubectl create namespace wordpress-namespace
```

---

## MySQL Deployment

### 1. Create Persistent Volume and Persistent Volume Claim

#### Apply the Persistent Volume (PV):
```bash
kubectl apply -f mysql/mysql-pv.yaml
```

#### Apply the Persistent Volume Claim (PVC):
```bash
kubectl apply -f mysql/mysql-pvc.yaml
```

### 2. Create MySQL Secrets
Store sensitive credentials:
```bash
kubectl apply -f mysql/mysql-secret.yaml
```

### 3. Create MySQL ConfigMap
Define environment-specific variables:
```bash
kubectl apply -f mysql/mysql-configmap.yaml
```

### 4. Deploy MySQL
```bash
kubectl apply -f mysql/mysql-deployment.yaml
```

### 5. Create MySQL Service
```bash
kubectl apply -f mysql/mysql-service.yaml
```

---

## WordPress Deployment

### 1. Create Persistent Volume and Persistent Volume Claim

#### Apply the Persistent Volume (PV):
```bash
kubectl apply -f wordpress/wordpress-pv.yaml
```

#### Apply the Persistent Volume Claim (PVC):
```bash
kubectl apply -f wordpress/wordpress-pvc.yaml
```

### 2. Create WordPress ConfigMap
Store WordPress-specific configurations:
```bash
kubectl apply -f wordpress/wordpress-configmap.yaml
```

### 3. Deploy WordPress
```bash
kubectl apply -f wordpress/wordpress-deployment.yaml
```

### 4. Create WordPress Service
Use NodePort for external access:
```bash
kubectl apply -f wordpress/wordpress-service.yaml
```

### 5. Apply Horizontal Pod Autoscaler (Optional)
```bash
kubectl apply -f wordpress/wordpress-hpa.yaml
```

### 6. Set Up Ingress for WordPress

#### Apply the Ingress Resource:
```bash
kubectl apply -f wordpress/wordpress-ingress.yaml
```

Ensure your `wordpress-ingress.yaml` file is configured with your domain, e.g., `burhan.com`.

---

## Port Forwarding (Optional Testing Without Ingress)
If you are not using Ingress or DNS is not configured yet, you can access services using port forwarding:

### MySQL Port Forwarding
```bash
kubectl port-forward svc/mysql 3306:3306 -n wordpress-namespace
```

### WordPress Port Forwarding
```bash
kubectl port-forward svc/wordpress 8080:80 -n wordpress-namespace
```

Access WordPress in your browser at `http://localhost:8080`.

---

## DNS Configuration
Update your DNS settings to point your domain (e.g., `burhan.com`) to the external IP of the Nginx Ingress Controller.

Check the Ingress Controller IP:
```bash
kubectl get svc -n ingress-nginx
```

Update DNS records:
```
A Record:
Host: burhan.com
Value: <Ingress-Controller-External-IP>
```

---

## Verifications
1. Verify all resources are created:
```bash
kubectl get all -n wordpress-namespace
```

2. Verify Ingress:
```bash
kubectl get ingress -n wordpress-namespace
```

3. Check logs for troubleshooting:
   - WordPress:
     ```bash
     kubectl logs deployment/wordpress -n wordpress-namespace
     ```
   - MySQL:
     ```bash
     kubectl logs deployment/mysql -n wordpress-namespace
     ```

---

## Cleanup
To delete all resources:
```bash
kubectl delete namespace wordpress-namespace
```

