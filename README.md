
# Introduction
This project is designed to simplify the deployment of [oobabooga/text-generation-webui](https://github.com/oobabooga/text-generation-webui) into a kubernetes cluster (with nvidia utils/gpus).

# Quick-Start
- Clone the repo: `git clone https://github.com/larkinwc/text-generation-webui-k8s`
- Navigate to the cloned directory: `cd text-generation-webui-k8s`
- *(Optional) Modify the Kubernetes configuration files to suit your requirements*
- Deploy the pvc: `kubectl apply -f k8s/pvc.yaml`
- Deploy the application: `kubectl apply -f k8s/app.yaml`
- Deploy the service: `kubectl apply -f k8s/service.yaml`
- Access the web UI through the external IP provided by your Kubernetes service.

# Usage
The repository provides a set of Kubernetes manifest files under the `k8s/` directory which include:
- Deployment: For creating the necessary pods.
- Service: To expose the application to an external IP.
- ConfigMap/Secret: To manage the application's configuration and secrets.

The project officially targets Kubernetes environments and should work on most Kubernetes-conformant clusters including minikube and cloud-based services like Google Kubernetes Engine (GKE), Amazon EKS, and Azure AKS.

## Pre-Requisites
- A Kubernetes cluster or minikube
- kubectl command-line tool
- Persistent storage (e.g. NFS, Ceph, etc.) for storing the model and generated text
- A GPU-enabled node (for running the model)

## Configuration
Adjust the Kubernetes manifests to configure your deployment. This includes setting resource limits, number of replicas, and persistent storage options.

## Standalone Pod
Running a single-instance pod without replication or services (not recommended for production use):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: text-generation-webui
spec:
  containers:
  - name: text-generation-webui
    image: larkinwc/text-generation-webui-k8s:latest
    ports:
    - containerPort: 5000
    - containerPort: 5001
    - containerPort: 5005
    - containerPort: 7860
    volumeMounts:
    - name: text-gen-pvc
      mountPath: /app/loras
      subPath: loras
    - name: text-gen-pvc
      mountPath: /app/models
      subPath: models
    resources:
      limits:
        nvidia.com/gpu: 1  # requesting 1 GPU
    volumes:
    - name: text-gen-pvc
        persistentVolumeClaim:
        claimName: text-gen-pvc
```