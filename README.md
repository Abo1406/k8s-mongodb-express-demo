# MongoDB & Mongo Express Kubernetes Deployment
This project demonstrates a Kubernetes deployment of MongoDB with Mongo Express for database management. It includes configurations for secrets, ConfigMaps, Deployments, and Services to create a scalable and secure setup.

## 📂 Project Structure
├── README.md

├── mongo-configmap.yml # MongoDB connection URL configuration

├── mongo-express-service.yml # External access Service for Mongo Express

├── mongo-express.yml # Mongo Express Deployment configuration

├── mongo-service.yml # Internal MongoDB Service

├── mongo.yml # MongoDB Deployment configuration

└── secret.yml # Authentication secrets


## ✅ Prerequisites
- A Kubernetes cluster (Minikube recommended for local testing)  
- `kubectl` command-line tool  
- Basic Kubernetes knowledge

## 🚀 Deployment Steps
### 1. Create Secrets
Generate SHA-512 hashed credentials:
```bash
echo -n "your_username" | sha512sum | awk '{print $1}'
echo -n "your_password" | sha512sum | awk '{print $1}'
```
Update secret.yml with your hashed values (remove any newlines), then apply:
kubectl apply -f secret.yml

### 2. Apply Configurations
Deploy all components in order:
kubectl apply -f mongo-configmap.yml
kubectl apply -f mongo.yml
kubectl apply -f mongo-service.yml
kubectl apply -f mongo-express.yml
kubectl apply -f mongo-express-service.yml

### 3. Verify Deployment
Check running resources:

kubectl get all
kubectl get service mongo-express-service

### 4. Access Mongo Express

Minikube
minikube service mongo-express-service
Other clusters

kubectl get service mongo-express-service
Access using the EXTERNAL-IP or NodePort (default: 30000).

## 🔧 Configuration Details
Secret Management (secret.yml)

Stores SHA-512 hashed MongoDB root credentials

Secures sensitive authentication data

Note: Replace example secrets before production use.

MongoDB Deployment (mongo.yml)

Deployment with 2 replicas

Uses the official MongoDB image

Exposes port 27017

Pulls credentials from Kubernetes Secrets

Mongo Express Deployment (mongo-express.yml)

Web-based MongoDB admin interface

Connects to MongoDB using a ConfigMap URL

Uses credentials from Secrets

Runs 2 replicas for redundancy

Sets connection URL via environment variables

Services Configuration

mongo-service.yml: Internal ClusterIP for MongoDB (port 27017)

mongo-express-service.yml: External LoadBalancer/NodePort for web access (port 30000)

## 🔒 Security Notes
🛡️ Always replace default secrets before production use

🌐 Use namespace isolation for production environments

🌐 Prefer an Ingress controller over NodePort for external access

💾 Enable PersistentVolumeClaims for data storage

🔑 Regularly rotate credentials in production

🔍 Avoid committing real secrets to version control

## 🐞 Troubleshooting
### Check Pod status and events
kubectl describe pod 'pod-name'

### View Pod logs
kubectl logs 'pod-name'

### Verify secret values
kubectl get secret mongodb-secret -o jsonpath='{.data}' | sha512 --decode

### Test MongoDB internal connection
kubectl exec -it <mongo-pod> -- mongo --host mongodb-service \
  -u $(kubectl get secret mongodb-secret -o jsonpath='{.data.mongo-root-username}' | base64 --decode) \
  -p $(kubectl get secret mongodb-secret -o jsonpath='{.data.mongo-root-password}' | base64 --decode)

### Check Service endpoints
kubectl describe service mongodb-service

## 🧹 Cleanup
Remove all resources:
kubectl delete -f mongo-express-service.yml \
               -f mongo-express.yml \
               -f mongo-service.yml \
               -f mongo.yml \
               -f secret.yml \
               -f mongo-configmap.yml
## 📄 License
MIT

## 📚 Documentation
MongoDB Official Docs
Mongo Express on GitHub
Kubernetes Secrets Documentation

