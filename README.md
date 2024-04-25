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

| NAME                    | PROMPT                                                                                                                                                                                                                                                                                                                                                                                                                                                               | DESCRIPTION                                                               | EXAMPLE                                                                                                          |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| app.yaml                | Create application config YAML to run a simple Kubernetes application with image gcr.io/devops-courses-419309/demo:v1.0.0, with labels app=demo and run=demo, and container port set to 8000 and port name to HTTP                                                                                                                                                                                                                                                   | YAML for creating a Pod to run Kubernetes application                     | [app.yaml](yaml/app.yaml)                               |
| app-livenessProbe.yaml  | Create a YAML Pod manifest specified as follows: Pod Name: app-livenessprob; namespace demo; container based on the image gcr.io/devops-courses-419309/demo:v1.0.0 with the name app; Liveness probe HTTP on /:8000 with delay 5s, period 10s, timeout 1s, max. unsuccessful attempts 3; open port 8080 with name http                                                                                                                                               | YAML to define a liveness probe for Kubernetes application                | [app-livenessProbe.yaml](yaml/app-livenessProbe.yaml)   |
| app-readinessProbe.yaml | Create a YAML Pod manifest specified as follows: Pod Name: app-readinessprob; container based on the image gcr.io/devops-courses-419309/demo:v1.0.0 with the name app; Liveness probe HTTP on /:8000 with delay 5s, period 10s, timeout 1s, max. unsuccessful attempts 3; Readiness probe HTTP on /ready:8000, period 2s, delay 0s, max. 3 unsuccessful, 1 successful; open port 8000 with name http                                                                 | YAML to define a readiness probe for Kubernetes application               | [app-readinessProbe.yaml](yaml/app-readinessProbe.yaml) |
| app-volumeMounts.yaml   | Create a YAML Pod manifest specified as follows:: Pod name: app-volume; container based on the image gcr.io/devops-courses-419309/demo:v1.0.0 ; Liveness probe HTTP on /healthy:8080 with delay 5s, period 10s, timeout 1s, max. unsuccessful attempts 3; Readiness probe HTTP on /ready:8080, period 2s, delay 0s, max. 3 unsuccessful, 1 successful; open port 8080 with name http ; data volume "data" with hostPath /var/lib/app is mounted in /data             | YAML to define and configure storage volumes for Kubernetes application   | [app-volumeMounts.yaml](yaml/app-volumeMounts.yaml)     |
| app-cronjob.yaml        | Create a YAML CronJob manifest specified as follows: CronJob name: app-cronjob; the main task is to output the expression 'Hello world' every 5 minutes using a container with the name hello and the image bash                                                                                                                                                                                                                                                     | YAML to define a cron job within Kubernetes application                   | [app-cronjob.yaml](yaml/app-cronjob.yaml)               |
| app-job.yaml            | Create a YAML Job manifest specified as follows: Job name: app-job-rsync; mount GCP volume glow-data-disk-200 type ext4 to container named init with image google/cloud-sdk:275.0.0-alpine running gsutil -m rsync -dr gs://glow-sportradar/ /data/input using sh; mount path /data/input; mount name data-input; the container is never reloaded and the backoff limit is 0                                                                                         | YAML to define a job within Kubernetes application                        | [app-job.yaml](yaml/app-job.yaml)                       |
| app-multicontainer.yaml | Create a YAML Pod manifest fspecified as follows: Pod name: app-multi-containers; the first container based on the nginx image with the name 1st and by mounting /usr/share/nginx/html; a second container based on the debian image with the name 2nd, the same volume and mount path /html; the second container should add date information to the /html/index.html file every second in an endless loop                                                          | YAML to define a pod that runs more than one container                    | [app-multicontainer.yaml](yaml/app-multicontainer.yaml) |
| app-resources.yaml      | Create a YAML pod manifest specified as follows: Pod name: app-resource; a container based on the image gcr.io/devops-courses-419309/demo:v1.0.0 with the name app; Liveness probe HTTP on /healthy:8080 with delay 5s, period 10s, timeout 1s, max. failed attempts 3; Readiness probe HTTP on /ready:8080, period 2s, delay 0s, max. 3 unsuccessful, 1 successful; open port 8080 with name http; and determination of resources and limits of CPU 100m, RAM 256Mi | YAML to configure resource requests and limits for Kubernetes application | [app-resources.yaml](yaml/app-resources.yaml)           |
| app-secret-env.yaml     | Create a YAML Pod manifest specified as follows: Pod name: app-secret-env; container based on the redis image with the name mycontainer; two variables SECRET_USERNAME and SECRET_PASSWORD of the secret key mysecret1 are transferred to the environment; the container does not restart                                                                                                                                                                            | YAML to define environment variables using secrets                        | [app-secret-env.yaml](yaml/app-secret-env.yaml)         |