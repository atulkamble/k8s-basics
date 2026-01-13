# 🌐 K8s Application Access Guide

## ✅ Ingress Successfully Deployed!

### 📊 Current Configuration

- **Ingress Name**: `demo-ingress`
- **Ingress Class**: `nginx`
- **External IP**: `20.3.2.94`
- **Host**: `demo.example.com`
- **Backend Service**: `demo-service:80`
- **Pod Endpoints**: `10.244.0.156:80`, `10.244.0.179:80`

---

## 🚀 How to Access Your Application

### Method 1: Using curl with Host Header (Quick Test)

```bash
curl -H "Host: demo.example.com" http://20.3.2.94
```

**Expected Output:**
```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

---

### Method 2: Browser Access (Add to /etc/hosts)

#### Step 1: Edit hosts file
```bash
sudo nano /etc/hosts
```

#### Step 2: Add this line
```
20.3.2.94 demo.example.com
```

#### Step 3: Access in browser
```
http://demo.example.com
```

---

### Method 3: Direct IP Access (Without Host Header)

```bash
curl http://20.3.2.94
```

**Note:** This may show a default backend or 404 error since ingress expects the host header.

---

## 🔍 Verify Deployment Status

### Check All Resources
```bash
# View all resources
kubectl get all

# Check ingress status
kubectl get ingress demo-ingress

# Get detailed ingress info
kubectl describe ingress demo-ingress

# Check ingress controller
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

### Test Backend Service
```bash
# Check service endpoints
kubectl get endpoints demo-service

# Port forward for direct testing
kubectl port-forward svc/demo-service 8080:80
# Then visit: http://localhost:8080
```

### Check Logs
```bash
# Application logs
kubectl logs -l app=demo

# Ingress controller logs
kubectl logs -n ingress-nginx -l app.kubernetes.io/component=controller

# Follow logs in real-time
kubectl logs -n ingress-nginx -l app.kubernetes.io/component=controller -f
```

---

## 🛠️ Troubleshooting

### Ingress Not Working?

1. **Verify Ingress Controller is Running**
   ```bash
   kubectl get pods -n ingress-nginx
   ```
   Expected: All pods should be in `Running` or `Completed` state

2. **Check Ingress Has an Address**
   ```bash
   kubectl get ingress
   ```
   Expected: ADDRESS column should show an IP

3. **Verify Service Endpoints**
   ```bash
   kubectl get endpoints demo-service
   ```
   Expected: Should list pod IPs

4. **Test Direct Service Access**
   ```bash
   kubectl run curl-test --image=curlimages/curl -it --rm -- curl http://demo-service
   ```

### Common Issues

#### Issue: Ingress ADDRESS is empty
**Solution:** Wait a few minutes for the LoadBalancer to provision
```bash
kubectl get ingress -w  # Watch for updates
```

#### Issue: 404 Not Found
**Solution:** Verify Host header matches ingress rules
```bash
curl -v -H "Host: demo.example.com" http://20.3.2.94
```

#### Issue: Connection Refused
**Solution:** Check if ingress controller is running
```bash
kubectl get svc -n ingress-nginx
kubectl logs -n ingress-nginx -l app.kubernetes.io/component=controller
```

---

## 📝 Quick Command Reference

```bash
# Apply all configurations
kubectl apply -f deploymenyt.yaml
kubectl apply -f service.yaml
kubectl apply -f configmap.yaml
kubectl apply -f ingress.yaml
kubectl apply -f hpa.yaml

# Check status
kubectl get all
kubectl get ingress
kubectl get hpa

# Scale deployment
kubectl scale deployment demo-deployment --replicas=3

# Delete resources
kubectl delete -f ingress.yaml
kubectl delete -f deploymenyt.yaml
kubectl delete -f service.yaml
kubectl delete -f configmap.yaml
kubectl delete -f hpa.yaml

# Delete ingress controller (if needed)
kubectl delete namespace ingress-nginx
```

---

## 🎯 Testing Scenarios

### 1. Test Load Balancing
```bash
# Make multiple requests
for i in {1..10}; do
  curl -s -H "Host: demo.example.com" http://20.3.2.94 | grep -o "<title>.*</title>"
done
```

### 2. Test Auto-Scaling (HPA)
```bash
# Check HPA status
kubectl get hpa

# Generate load (requires hey or similar tool)
hey -z 60s -c 50 -H "Host: demo.example.com" http://20.3.2.94

# Watch pods scale
kubectl get pods -w
```

### 3. Test Rolling Update
```bash
# Update image
kubectl set image deployment/demo-deployment nginx=nginx:alpine

# Watch rollout
kubectl rollout status deployment/demo-deployment

# Check pods transitioning
kubectl get pods -w
```

---

## 📊 Architecture Flow

```
Client/Browser
    ↓
    ↓ (HTTP Request with Host: demo.example.com)
    ↓
Ingress Controller (20.3.2.94)
    ↓
    ↓ (Routes based on host/path rules)
    ↓
demo-ingress (Ingress Resource)
    ↓
    ↓ (Forwards to backend service)
    ↓
demo-service (ClusterIP 10.0.73.67:80)
    ↓
    ↓ (Load balances across pods)
    ↓
├─→ Pod 1 (10.244.0.156:80) - nginx
└─→ Pod 2 (10.244.0.179:80) - nginx
```

---

## 🔐 Security Notes

- Current configuration uses **HTTP only** (no TLS)
- Secret `demo-secret` contains base64 encoded credentials
- To enable HTTPS, add TLS certificate to ingress

### Add HTTPS (Optional)

1. Create TLS secret:
```bash
kubectl create secret tls demo-tls \
  --cert=path/to/cert.crt \
  --key=path/to/cert.key
```

2. Update ingress.yaml:
```yaml
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - demo.example.com
    secretName: demo-tls
  rules:
  # ... rest of configuration
```

---

## 📞 Support

For issues or questions:
- Check logs: `kubectl logs -l app=demo`
- Describe resource: `kubectl describe ingress demo-ingress`
- View events: `kubectl get events --sort-by=.metadata.creationTimestamp`

---

**Last Updated:** January 13, 2026  
**Ingress External IP:** 20.3.2.94  
**Status:** ✅ Operational
