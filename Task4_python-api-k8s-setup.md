
## Step 4: Install Docker on the Worker Node

SSH into the worker node and install Docker.

- **Update the package index**  
  ```bash
  sudo apt update
  ```

- **Install Docker**  
  ```bash
  sudo apt install -y docker.io
  ```

- **Enable and start the Docker service**  
  ```bash
  sudo systemctl enable docker
  sudo systemctl start docker
  ```

- **Verify Docker installation**  
  ```bash
  docker --version
  ```

---

## Step 5: Create the Python API Project

- **Create a directory named `python-api`**  
  ```bash
  mkdir python-api && cd python-api
  ```

- **Create a simple Flask API**  
  ```bash
  vi app.py
  ```

- **Add the following script to `app.py`**  
  ```python
  from flask import Flask
  app = Flask(__name__)

  @app.route("/")
  def hello():
      return "Hello from Kubernetes Python API!"

  if __name__ == "__main__":
      app.run(host="0.0.0.0", port=5000)
  ```

---

## Step 6: Create a Dockerfile

- **Create the Dockerfile**  
  ```bash
  vi Dockerfile
  ```

- **Add the following content**  
  ```Dockerfile
  FROM python:3.9-slim
  WORKDIR /app
  COPY app.py .
  RUN pip install flask
  EXPOSE 5000
  CMD ["python", "app.py"]
  ```

---

## Step 7: Build & Push Docker Image to DockerHub

- **Login to DockerHub**  
  ```bash
  docker login
  ```

- **Build the Docker image**  
  ```bash
  docker build -t sameeravurity/python-api .
  ```

- **Push it to DockerHub**  
  ```bash
  docker push sameeravurity/python-api
  ```

---

## Step 8: Create Kubernetes YAML Files

- **Create a deployment file**  
  ```bash
  vi python-api-deployment.yaml
  ```

- **Add the following script**  
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: python-api
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: python-api
    template:
      metadata:
        labels:
          app: python-api
      spec:
        containers:
        - name: python-api
          image: sameeravurity/python-api
          ports:
          - containerPort: 5000
  ```

- **Create a service file**  
  ```bash
  vi python-api-service.yaml
  ```

- **Add the following script**  
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: python-api-service
  spec:
    selector:
      app: python-api
    type: NodePort
    ports:
    - port: 80
      targetPort: 5000
      nodePort: 30001
  ```

---

## Step 9: Deploy to KOPS Kubernetes Cluster

- **Apply the deployment and service**  
  ```bash
  kubectl apply -f python-api-deployment.yaml
  kubectl apply -f python-api-service.yaml
  ```

- **Verify the deployment**  
  ```bash
  kubectl get pods
  kubectl get svc
  kubectl get nodes -o wide
  ```

- **Access the app**  
  Open in your browser:  
  ```
  http://<worker-node-public-ip>:30001
  ```
