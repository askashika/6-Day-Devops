
#  Devops Training - Day 4 : Kubernetes Deployment with Namespace, Deployment, Service & Jenkins Setup

##  Step 1: Run the following command to check nodes
```bash
kubectl get nodes
```



##  Step 2: Create a Namespace
```bash
kubectl create ns ash-2
```



##  Step 3: Check the Created Namespace
```bash
kubectl get ns
```

---

##  Step 4: Create a YAML File
```bash
vi ash-deployment.yaml
```

Paste the below YAML code with proper indentation:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ash-deployment
  namespace: ash-2
spec:
  replicas: 5
  selector:
    matchLabels:
      app: ipl
  template:
    metadata:
      labels:
        app: ipl
    spec:
      containers:
        - name: c-1
          image: daviddocker526/ipl-srh:latest
          ports:
            - containerPort: 80
```

---

##  Step 5: Run the Deployment YAML File
```bash
kubectl apply -f ash-deployment.yaml
```

---

##  Step 6: Check the Deployment Status
```bash
kubectl get deployment -n ash-2  
kubectl get rs -n ash-2  
kubectl get pods -n ash-2
```

---

##  Step 7: Types of Kubernetes Services
1. ClusterIP  
2. NodePort  
3. LoadBalancer  
4. ExternalName

---

##  Step 8: View All Resources in the Namespace
```bash
kubectl get all -n ash-2
```

---

##  Step 9: Create a Service YAML File
```bash
vi ash-service.yaml
```

Paste the following YAML:

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: ash-service
  namespace: ash-2
spec:
  selector:
    app: ipl
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```


##  Step 10: Apply the Service File
```bash
kubectl apply -f ash-service.yaml
```



##  Step 11: Check the Service
```bash
kubectl get all -n ash-2
```



##  Step 12: Delete a Pod
```bash
kubectl delete pod ash-deployment-8568d84476-25t2r -n ash-2  
kubectl get pods -n ash-2
```



##  Step 13: Delete Deployment
```bash
kubectl delete deployment ash-deployment -n ash-2
```



##  Step 14: Delete Service
```bash
kubectl delete service ash-service -n ash-2
```



##  Step 15: Check if Deployments and Services Still Exist
```bash
kubectl get all -n ash-2
```


##  Jenkins Installation Guide

### Step 1: Install Jenkins
- Go with the **Long-Term Support (LTS)** release.
- Paste the command from the official Jenkins site into your server terminal.

### Step 2: Install Java
- Jenkins is built using Java, so install Java by copying the command from the internet or Jenkins site.

### Step 3: Start Jenkins Server
- Start the Jenkins service and follow setup instructions in the web interface.
