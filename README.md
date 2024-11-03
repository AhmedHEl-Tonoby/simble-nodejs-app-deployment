# Test Node.js Application with MongoDB

This project is a simple Node.js application that connects to a MongoDB database. The purpose is to demonstrate deployment using Docker, Docker Compose, and Kubernetes.

## Project Overview

The application is built with Node.js and MongoDB, designed for easy deployment and scalability. It has been containerized using Docker and deployed on Kubernetes for high availability and load balancing.

### Key Features
- **Node.js**: The application backend is built using Node.js, making it efficient for handling HTTP requests.
- **MongoDB**: The database used for storing application data.
- **Docker**: The app is containerized to ensure consistency across different environments.
- **Docker Compose**: Automates the linking of the Node.js app with MongoDB.
- **Kubernetes**: Manages the deployment with multiple replicas for load balancing and scaling.

## Docker Setup

### Step 1: Pull the Docker Image
The application Docker image is available on Docker Hub. You can pull it using:
```bash
docker pull ahmedheltonoby/test-node-app:v1
```

### Step 2: Run the Docker Container
To run the application directly with Docker:
```bash
docker run -p 3000:3000 ahmedheltonoby/test-node-app:v1
```

This command will start the application and expose it on port `3000`.

## Docker Compose Setup

### Step 1: Create a `docker-compose.yml` file
Use the following `docker-compose.yml` file to link the Node.js application with MongoDB.

```yaml
version: '3'
services:
  app:
    image: ahmedheltonoby/test-node-app:v1
    ports:
      - '3000:3000'
    depends_on:
      - db

  db:
    image: mongo:latest
    ports:
      - '27017:27017'
```

### Step 2: Run Docker Compose
Use the following command to start the application and MongoDB together:
```bash
docker-compose up
```

This will start both the application and the MongoDB service, linking them together.

## Kubernetes Setup

### Step 1: Create a Kubernetes Deployment
The deployment file `deployment.yaml` below defines the Node.js application deployment on Kubernetes with 3 replicas for load balancing and high availability.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-node-app
  labels:
    app: node
spec:
  replicas: 3
  selector:
    matchLabels:
      app: node
  template:
    metadata:
      labels:
        app: node
    spec:
      containers:
      - name: test-node-app
        image: ahmedheltonoby/test-node-app:v1
        ports:
        - containerPort: 3000
      - name: mongo-db
        image: mongo:latest
        ports:
        - containerPort: 27017
```

This `Deployment` resource:
- Defines 3 replicas of the application, ensuring high availability.
- Uses `matchLabels` to connect this deployment with the service.
- Sets the container image to `ahmedheltonoby/test-node-app:v1` and exposes it on port 3000.

### Step 2: Create a Kubernetes Service
To make the application accessible outside the Kubernetes cluster, define a `NodePort` service in `service.yaml`.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: node
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 30002
```

This `Service` resource:
- Uses `NodePort` to expose the app outside the cluster on port `30002`.
- Maps the service port `3000` to the application's `targetPort` `3000`.
- Uses `selector` to link the service with the deployment's `app: test-node-app` label.

### Step 3: Apply the Kubernetes Configurations
To deploy the application and service on Kubernetes, run:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

This will create the necessary Kubernetes resources and launch the application with 3 replicas.

### Step 4: Access the Application
The application is now accessible through the `NodePort` on `NodeIP:30002`. Use this command to verify the application is running:

```bash
curl http://<NodeIP>:30002
```

> **Note**: If you're using Minikube, you can get the Node IP by running:
> ```bash
> minikube ip
> ```

This completes the Kubernetes setup, making the Node.js app accessible and scalable.

## Note
This project is still a work in progress. I plan to continue developing it and adding more tools to improve the DevOps cycle.

