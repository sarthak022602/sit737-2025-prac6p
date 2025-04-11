
# SIT737 – Cloud Native Application Development  
## Practical Task 6.1P – Kubernetes Cluster for a Containerized Node.js App

---

## 👋 Introduction


This README outlines everything I did to complete Task 6.1P, where I deployed a containerized Node.js application to a Kubernetes cluster using Docker Desktop. I followed each step from app creation to successful deployment, testing, and interaction through a NodePort service. Below, I’ve explained the entire process clearly, just as if I were walking you through it in person.

---

## 🧱 Step-by-Step Process

### 1. Project Setup

I began by creating a folder called `sit737-6.1p`, where I initialized a basic **Node.js + Express** web application. The file `app.js` contains a single route that sends a welcome message. This simple setup allowed me to focus more on the Kubernetes and Docker components of the task.

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
  res.send('Welcome to SIT737 Kubernetes Node.js App!');
});

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

I also created a `package.json` file and installed **express** via `npm`.

---

### 2. Dockerization of the App

Next, I wrote a `Dockerfile` to containerize the application. I used the lightweight `node:18-alpine` base image and ensured the container listens on port `3000`.

Here’s the Dockerfile I used:

```Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

I built the image using:

docker build -t sit737-task6p-app .
```

Then I tested it locally using:

docker run -p 3000:3000 sit737-task6p-app
```

This confirmed the containerized app worked as expected on [http://localhost:3000](http://localhost:3000).

---

### 3. Kubernetes Deployment

With the app working, I moved on to creating the **Kubernetes Deployment YAML**.

**deployment.yaml**:


apiVersion: apps/v1
kind: Deployment
metadata:
  name: sit737-task6p-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sit737-task6p-app
  template:
    metadata:
      labels:
        app: sit737-task6p-app
    spec:
      containers:
      - name: sit737-task6p-container
        image: sit737-task6p-app
        imagePullPolicy: Never
        ports:
        - containerPort: 3000
```

> I used `imagePullPolicy: Never` since I’m using a local image (not from DockerHub).

I deployed it using:

kubectl apply -f deployment.yaml
```

Then verified the pods using:

kubectl get pods
```

---

### 4. Exposing the App Using a Kubernetes Service

To access the app externally, I created a **NodePort service** that forwards traffic from port 30036 to 3000.

**service.yaml**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: sit737-task6p-service
spec:
  type: NodePort
  selector:
    app: sit737-task6p-app
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 30036
```

I applied it using:
```bash
kubectl apply -f service.yaml
```

And verified the service:
```bash
kubectl get svc
```

---

### 5. Final Testing

After deployment, I accessed the app via browser at:

👉 `http://localhost:30036`

Everything worked as expected, and the app responded with:

```
Welcome to SIT737 Kubernetes Node.js App!
```

---

## 📸 Screenshots

Along the way, I captured these key steps attached in the submitted pdf on ontrack:
- `kubectl get nodes`
- `kubectl get pods`
- `kubectl get svc`
- Final browser output from `http://localhost:30036`

---

## 💬 What I Learned

Through this task, I gained a strong understanding of:
- Building Docker images from scratch
- Creating a functional `Dockerfile`
- Writing and applying Kubernetes deployment & service YAMLs
- Using Docker Desktop's local Kubernetes environment
- Mapping ports and ensuring correct image references with `imagePullPolicy: Never`

This hands-on experience helped me connect theory from lectures with real-world development and deployment workflows. I now feel confident deploying apps using Kubernetes.

---

## 📁 Final Project Files

- `app.js` – Express app
- `package.json` – Dependencies
- `Dockerfile` – Container definition
- `deployment.yaml` – Kubernetes deployment spec
- `service.yaml` – Kubernetes service (NodePort)
- `README.md` – Detailed report of the task

---

## 👨‍🎓 Student Info

**Name:** Sarthak Dutta  
**Unit:** SIT737 – Cloud Native Application Development  
**Trimester:** T1 2025  
**Task:** Practical 6.1P  


---

Thank you for reviewing my task. I’ve learned a lot from this hands-on deployment process and I look forward to your feedback.

`