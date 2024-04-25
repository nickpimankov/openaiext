# kubectl-ai plugin for AI generated YAML manifests     

1. Registered on [OpenAI](https://openai.com/)      

2. Created an [API Key](https://platform.openai.com/account/api-keys)       

3. Installed and configured [kubectl-ai plugin](https://github.com/sozercan/kubectl-ai)     

```bash
$ wget https://github.com/sozercan/kubectl-ai/releases/download/v0.0.12/kubectl-ai_linux_amd64.tar.gz
$ tar -zxvf kubectl-ai_linux_amd64.tar.gz
$ mv kubectl-ai /usr/local/bin/
$ chmod +x /usr/local/bin/kubectl-ai

$ kubectl plugin list
The following compatible plugins are available:

/home/nickpimankov/.krew/bin/kubectl-krew
/home/nickpimankov/.krew/bin/kubectl-ns
/usr/local/bin/kubectl-ai
/usr/local/bin/kubectl-kubeplugin

$ nano ~/.bashrc
export OPENAI_API_KEY="****"

$ source ~/.bashrc

$ export OPENAI_DEPLOYMENT_NAME="gpt-3.5-turbo-0125"
```     

4. Tried some actions:        
```bash
$ k ai "create an nginx deployment with 3 replicas"
✨ Attempting to apply the following manifest:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
Use the arrow keys to navigate: ↓ ↑ → ←
? Would you like to apply this? [Reprompt/Apply/Don't Apply]:
+   Reprompt
  ▸ Apply
    Don't Apply
```     

```bash
$ k ai "create a foo namespace then create nginx pod in that namespace"
✨ Attempting to apply the following manifest:
apiVersion: v1
kind: Namespace
metadata:
  name: foo
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: foo
spec:
  containers:
  - name: nginx
    image: nginx:latest
Use the arrow keys to navigate: ↓ ↑ → ←
? Would you like to apply this? [Reprompt/Apply/Don't Apply]:
+   Reprompt
  ▸ Apply
    Don't Apply
```

```bash
$ k ai "create a service with type LoadBalancer with selector as 'app:nginx'" --require-confirmation=false
✨ Attempting to apply the following manifest:
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
  ```       

5. Created a portfolio of own prompts for listed manifests:     

| NAME                        | PROMPT                             | DESCRIPTION                                                              | EXAMPLE                                     |
|-----------------------------|------------------------------------|--------------------------------------------------------------------------|---------------------------------------------|
| app.yaml                    | Create application config YAML to run a simple Kubernetes application with image gcr.io/devops-courses-419309/demo:v1.0.0, with labels app=demo and run=demo, and container port set to 8000 and port name to HTTP         | YAML to define the basic schema of a Kubernetes application              | [app.yaml](yaml/app.yaml)                 |
| app-livenessProbe.yaml      | Add Liveness Probe                 | YAML to define a liveness probe for your application                    | [app-livenessProbe.yaml](yaml/app-livenessProbe.yaml) |
| app-readinessProbe.yaml     | Add Readiness Probe                | YAML to define a readiness probe for your application                   | [app-readinessProbe.yaml](yaml/app-readinessProbe.yaml) |
| app-volumeMounts.yaml       | Configure Volume Mounts            | YAML to define and configure storage volumes for your application       | [app-volumeMounts.yaml](yaml/app-volumeMounts.yaml) |
| app-cronjob.yaml            | Create Cron Job                    | YAML to define a cron job within your application                       | [app-cronjob.yaml](yaml/app-cronjob.yaml) |
| app-job.yaml                | Create a Job                       | YAML to define a job within your application                            | [app-job.yaml](yaml/app-job.yaml) |
| app-multicontainer.yaml     | Set Up Multi-container Pods        | YAML to define a pod that runs more than one container                  | [app-multicontainer.yaml](yaml/app-multicontainer.yaml) |
| app-resources.yaml          | Configure Resource Usage           | YAML to configure resource requests and limits for your application     | [app-resources.yaml](yaml/app-resources.yaml) |
| app-secret-env.yaml         | Set Up Secrets as Env Variables    | YAML to define environment variables using secrets                      | [app-secret-env.yaml](yaml/app-secret-env.yaml) |