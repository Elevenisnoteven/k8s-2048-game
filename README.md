# k8s-2048-game
2048 game

extract the file app1-2048.zip

## Tutorial: Deploying the 2048 Game Application Using Docker and Kubernetes

This tutorial will guide you through the steps to deploy the 2048 game application using Docker and Kubernetes. You'll learn how to build a Docker image, push it to Docker Hub, create Kubernetes deployment and service files, and finally, deploy the application to a Kubernetes cluster.

### Prerequisites
- Docker installed on your machine
- Kubernetes installed (Minikube or any other Kubernetes setup)
- `kubectl` command-line tool installed
- Basic understanding of Docker and Kubernetes concepts

### Step 1: Build the Docker Image

1. **Navigate to your project directory** where your Dockerfile is located.

    ```sh
    cd ~/docker-fundamentals/build-a-simple-containerized-application/app1-2048
    ```

2. **Create a Dockerfile** with the following content if not already present:

    ```Dockerfile
    FROM nginx:latest
    COPY 2048 /usr/share/nginx/html
    ```

3. **Build the Docker image** using the following command:

    ```sh
    docker build -t elevenisnoteven/dockerized-k8s-2048 .
    ```

    This command will output something similar to:

    ```
    [+] Building 2.5s (8/8) FINISHED
    => [internal] load build definition from Dockerfile 0.0s
    => => transferring dockerfile: 186B 0.0s
    => [internal] load metadata for docker.io/library/nginx:latest 2.3s
    => [auth] library/nginx:pull token for registry-1.docker.io 0.0s
    => [internal] load .dockerignore 0.0s
    => => transferring context: 2B 0.0s
    => [internal] load build context 0.0s
    => => transferring context: 1.69kB 0.0s
    => [1/2] FROM docker.io/library/nginx:latest@sha256:0f04e4f646a3f14bf31d8bc8d885b6c951fdcf42589d06845f64d18aec6a3c4d 0.0s
    => CACHED [2/2] COPY 2048 /usr/share/nginx/html 0.0s
    => exporting to image 0.0s
    => => exporting layers 0.0s
    => => writing image sha256:8db7501d947b58bc52e323d71296eb02413bf1f39dbd86d5c94efbe95b8444e3 0.0s
    => => naming to docker.io/elevenisnoteven/dockerized-k8s-2048 0.0s
    ```

### Step 2: Push the Docker Image to Docker Hub

1. **Log in to Docker Hub** if you are not already logged in:

    ```sh
    docker login
    ```

2. **Push the Docker image** to Docker Hub:

    ```sh
    docker push elevenisnoteven/dockerized-k8s-2048
    ```

    You should see output indicating the image is being pushed:

    ```
    Using default tag: latest
    The push refers to repository [docker.io/elevenisnoteven/dockerized-k8s-2048]
    196002bdb2ce: Pushed
    3f6a3d22b9ce: Pushed
    261a5dc153b4: Pushed
    7da4ba4a0030: Pushed
    10988c108f66: Pushed
    d58e4a0f2971: Pushed
    37719940dcaa: Pushed
    5d4427064ecc: Pushed
    latest: digest: sha256:8257f1c8259b2a25916ebcd98fe4cabad937095441dbee9e2da0d354f0aae526 size: 1988
    ```

### Step 3: Create Kubernetes Deployment and Service

1. **Create a `2048-deployment.yml` file** with the following content:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: game-2048-deployment
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: game-2048
      template:
        metadata:
          labels:
            app: game-2048
        spec:
          containers:
          - name: game-2048
            image: elevenisnoteven/dockerized-k8s-2048:latest
            ports:
            - containerPort: 80
    ```

2. **Create a `2048-service.yml` file** with the following content:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: game-2048-service
    spec:
      type: LoadBalancer
      selector:
        app: game-2048
      ports:
        - protocol: TCP
          port: 80
          targetPort: 80
    ```

### Step 4: Deploy the Application to Kubernetes

1. **Apply the deployment file**:

    ```sh
    kubectl apply -f 2048-deployment.yml
    ```

    Output:

    ```
    deployment.apps/game-2048-deployment created
    ```

2. **Apply the service file**:

    ```sh
    kubectl apply -f 2048-service.yml
    ```

    Output:

    ```
    service/game-2048-service created
    ```

3. **Verify the deployment and service**:

    ```sh
    kubectl get deployment,svc
    ```

    Output:

    ```
    NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/game-2048-deployment   3/3     3            3           18s

    NAME                        TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
    service/game-2048-service   LoadBalancer   10.109.179.187   <pending>     80:32624/TCP   13s
    ```

### Step 5: Access the 2048 Game

1. **Start Minikube service tunnel** (if using Minikube):

    ```sh
    minikube service game-2048-service
    ```

    Output:

    ```
    |-----------|-------------------|-------------|---------------------------|
    | NAMESPACE |       NAME        | TARGET PORT |            URL            |
    |-----------|-------------------|-------------|---------------------------|
    | default   | game-2048-service |          80 | http://192.168.49.2:32624 |
    |-----------|-------------------|-------------|---------------------------|
    ```

2. **Open the provided URL** in your browser to access the 2048 game.

### Troubleshooting

- **Error when applying deployment or service**:
    - Check the YAML files for syntax errors.
    - Ensure that the metadata names conform to Kubernetes naming conventions.

- **Service External IP is pending**:
    - It might take some time for the external IP to be assigned. You can use `minikube tunnel` if using Minikube to expose the service.

By following these steps, you should be able to successfully deploy the 2048 game application using Docker and Kubernetes.
