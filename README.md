# Django site for k8s experiments

This project is created for experiments with Django-based website, [Minikube](https://minikube.sigs.k8s.io/docs/start/) and [Kubernetes](https://kubernetes.io/).
The Django is started with [Nginx Unit](https://unit.nginx.org/).

## Prerequisites

- [Docker Desktop](https://docs.docker.com/desktop/) or
- [Docker Engine](https://docs.docker.com/engine/install/) and [the Compose plugin](https://docs.docker.com/compose/install/linux/);
- [kubectl](https://kubernetes.io/docs/tasks/tools/);
- Virtualization software, for example, HyperV, Docker (go [here](https://minikube.sigs.k8s.io/docs/drivers/) for more);

## Installation

- Start a cluster:

```bash
minikube start
```

- Build an image:

```bash
cd backend_main_django
minikube image build -t web -f Dockerfile .
cd ..
```

- Go to the `kubernetes` folder:

```bash
cd kubernetes
```

- Create the `configmap.yaml` file and fill up the environmental variables:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: django-config
  namespace: default
data:
  SECRET_KEY: replace_me
  DEBUG: "False"
  DATABASE_URL: postgres://test_k8s:OwOtBep9Frut@192.168.1.35:5432/test_k8s
  ALLOWED_HOSTS: star-burger.test
```

- Create the ConfigMap:

```bash
kubectl create -f configmap.yaml
```

- Apply the deployment and service configuration:

```bash
kubectl apply -f deploy.yaml
```

- Enable the NGINX Ingress controller:

```bash
minikube addons enable ingress
```

- Verify that the NGINX Ingress controller is running:

```bash
kubectl get pods -n ingress-nginx
```

- Create the ingress object:

```bash
kubectl apply -f ingress.yaml
```

- Verify that the Ingress is applied:

```bash
kubectl get ingress
```

- Get the minikube ip:

```bash
minikube ip
```

- Edit the `hosts` file, which is usually situated in the `C:\Windows\System32\drivers\etc\` directory (for Windows 11). Add the mapping of the minikube IP to the `star-burger.test` host name. For example:

```config
172.26.28.91 star-burger.test
```

where `172.26.28.91` is the minikube IP

- Verify that the website works (you can also just go to [star-burger.test](http://star-burger.test)):

```bash
curl star-burger.test
```

... migrate
... createsuperuser

## Clearing the session store

Create the `django-clearsessions` cronjob, which will run monthly:

```bash
kubectl create -f clearsessions.yaml
```

- Verify that the `django-clearsessions` cronjob is created:

```bash
kubectl get cronjob
```

- You can trigger the `django-clearsessions` cronjob manually:

```bash
kubectl create job --from=cronjob/django-clearsessions clear-job
```

where `clear-job` is a job name

## Usage

Open the admin site

## Project goals

The project was created for educational purposes.
It's a lesson for python and web developers at [Devman](https://dvmn.org).
