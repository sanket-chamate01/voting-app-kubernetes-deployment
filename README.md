# Voting App Deployment

This voting app is designed with a microservices architecture, utilizing Docker containers and Kubernetes for easy deployment and scalability. The application consists of three main components: `vote-app` (Python), `result-app` (Node.js), and `worker-app` (C#), connected through Redis and PostgreSQL databases.

![Image of Voting App Architecture](architecture.png)

## Using Docker --link
Before running the application, make sure you have Docker installed on your system.

### Running Redis Container
```bash
docker run -d --name=redis redis
```
### Running PostgreSQL Container
This is not a secured approach, use username and password instead
```bash
docker run -d --name=db -e POSTGRES_HOST_AUTH_METHOD=trust postgres:9.4
```

### Running Vote App Container
This is not a secured approach, use username and password instead
```bash
docker run -d --name=vote -p 5000:80 --link redis:redis sanket01/voting-app-vote-app
```

### Running Result App Container
This is not a secured approach, use username and password instead
```bash
docker run -d --name=result -p 5001:80 --link db:db sanket01/voting-app-result-app
```

### Running Worker App Container
This is not a secured approach, use username and password instead
```bash
docker run -d --name=worker --link db:db --link redis:redis sanket01/voting-app-worker-app
```

### Accessing the Apps
Visit localhost:5000 to access the vote-app.
Visit localhost:5001 to access the result-app.

### Important Note
The usage of --link is not the recommended way for inter-container communication in Docker. For a production environment, consider using Docker networks instead.


## Deployment with Kubernetes

### Deployment Steps

1. Deploy the `vote-app` pod and service:

    ```bash
    kubectl create -f vote-app-pod.yaml
    kubectl create -f vote-app-service.yaml
    ```

2. Deploy the `redis` pod and service:

    ```bash
    kubectl create -f redis-pod.yaml
    kubectl create -f redis-service.yaml
    ```

3. Deploy the `postgres` pod and service:

    ```bash
    kubectl create -f postgres-pod.yaml
    kubectl create -f postgres-service.yaml
    ```

4. Deploy the `worker` pod:

    ```bash
    kubectl create -f worker-pod.yaml
    ```

5. Deploy the `result-app` pod and service:

    ```bash
    kubectl create -f result-app-pod.yaml
    kubectl create -f result-app-service.yaml
    ```

### Accessing the Apps

1. Get the URL for the `vote-app`:

    ```bash
    minikube service vote-app-service --url
    ```

    Access the `vote-app` using the provided URL.

2. Get the URL for the `result-app`:

    ```bash
    minikube service result-app-service --url
    ```

    Access the `result-app` using the provided URL.

### Important Note

- Ensure that Minikube is running and properly configured.
- The provided YAML files assume a local development environment. Modify configurations accordingly for a production setup.

### Clean Up

To clean up and delete all resources:

```bash
kubectl delete pods,services -l app=vote-app
kubectl delete pods,services -l app=result-app
kubectl delete pods,services -l app=redis
kubectl delete pods,services -l app=postgres
kubectl delete pods -l app=worker
