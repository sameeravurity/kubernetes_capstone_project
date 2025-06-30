
# Two-Tier Architecture: Deploy Nextcloud with PostgreSQL on Kubernetes

Deploying Nextcloud with PostgreSQL in a two-tier architecture on Kubernetes means separating:

- **Tier 1:** The Application Layer (Nextcloud)  
- **Tier 2:** The Database Layer (PostgreSQL)

---

## Step 1: Create Kubernetes Namespace

```bash
kubectl create namespace nextcloud
```

---

## Step 2: Create Persistent Volumes (PV/PVC)

### Create `nextcloud-pvc.yaml`

```bash
vi nextcloud-pvc.yaml
```

**Content:**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nextcloud-pvc
  namespace: nextcloud
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

### Create `postgres-pvc.yaml`

```bash
vi postgres-pvc.yaml
```

**Content:**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-pvc
  namespace: nextcloud
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

---

## Step 3: PostgreSQL Deployment and Service

### Create `postgres-deployment.yaml`

```bash
vi postgres-deployment.yaml
```

**Content:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  labels:
    app: postgres
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: postgres:15
          ports:
            - containerPort: 5432
          env:
            - name: POSTGRES_DB
              value: nextcloud
            - name: POSTGRES_USER
              value: nextcloud
            - name: POSTGRES_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: postgres-secret
                  key: POSTGRES_PASSWORD
          volumeMounts:
            - mountPath: /var/lib/postgresql/data
              name: postgres-storage
      volumes:
        - name: postgres-storage
          persistentVolumeClaim:
            claimName: postgres-pvc
```

### Create `postgres-service.yaml`

**Content:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres
  namespace: nextcloud
spec:
  selector:
    app: postgres
  ports:
    - port: 5432
      targetPort: 5432
```

---

## Step 4: Nextcloud Deployment and Service

### Create `nextcloud-deployment.yaml`

**Content:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nextcloud
  namespace: nextcloud
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nextcloud
  template:
    metadata:
      labels:
        app: nextcloud
    spec:
      containers:
        - name: nextcloud
          image: nextcloud
          ports:
            - containerPort: 80
          env:
            - name: POSTGRES_HOST
              value: postgres
            - name: POSTGRES_DB
              value: nextcloud
            - name: POSTGRES_USER
              value: ncuser
            - name: POSTGRES_PASSWORD
              value: ncpass
          volumeMounts:
            - mountPath: /var/www/html
              name: nextcloud-storage
      volumes:
        - name: nextcloud-storage
          persistentVolumeClaim:
            claimName: nextcloud-pvc
```

### Create `nextcloud-service.yaml`

**Content:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nextcloud
  namespace: nextcloud
spec:
  selector:
    app: nextcloud
  ports:
    - port: 80
      targetPort: 80
  type: NodePort
```

---

## Step 5: Apply All YAML Files

```bash
kubectl apply -f postgres-pvc.yaml
kubectl apply -f nextcloud-pvc.yaml
kubectl apply -f postgres-deployment.yaml
kubectl apply -f postgres-service.yaml
kubectl apply -f nextcloud-deployment.yaml
kubectl apply -f nextcloud-service.yaml
```

---

## Step 6: Access Nextcloud

- Run the command to get the service:
  ```bash
  kubectl get svc -n nextcloud
  ```

- Run the command to get the pods:
  ```bash
  kubectl get pods -n nextcloud
  ```

- Access the Nextcloud UI by browsing to:
  ```
  http://<node-ip>:<nodeport>
  ```
