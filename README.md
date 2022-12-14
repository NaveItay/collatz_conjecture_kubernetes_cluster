# Kubernetes cluster setup guide
![title](/guide_images/intro.png)

## introduction

A Kubernetes cluster is a set of nodes that run containerized applications. Containerizing applications packages an app with its dependences and some necessary services. They are more lightweight and flexible than virtual machines.

This reposity will guide you how to set an application containerized and deployed on Kubernetes.

### Project stages:
- A. Create a python apllication
- B. Set up a Kubernetes cluster and expose the apllication as a service. 
  - 1. Check if virtualization is supported in your machine
  - 2. Install docker desktop
  - 3. Create a dockerfile
  - 4. Create a docker-compose.yaml
  - 5. Build Process
  - 6. Starting the app
  - 7. Install Kubernetes command-line tool (kubectl) and add it to PATH env variables
  - 8. Kubernetes
       - Install
       - Test
  - 9. Kubernetes deployment
  - 10. Enable traffic
  - 11. Application is live running inside Kubernetes

<p>
<br />
</p>

#### Create a python apllication
#
```
from fastapi import FastAPI

app = FastAPI()


@app.get("/collatz_conjecture")
def get_collatz_conjecture(number: int):
    interactions_number = 0
    answer = "Answer: "

    while number != 1:
        interactions_number += 1
        number = collatz_conjecture(number)
        answer += f'{number},'

    answer += f'interactions number: {interactions_number}'
    return answer


def collatz_conjecture(number):
    if number % 2 == 0:
        return number // 2
    elif number % 2 == 1:
        return number * 3 + 1
```
I created a web framework for building the API with fastAPI.

<p>
<br />
</p>

#### Set up a kubernetes cluster and expose the code as a service. 
#

###### 1. Check if virtualization is supported in your machine
```
$ systeminfo
```
![title](/guide_images/virtualization_is_supported.PNG)

if virtualization in firmware is enabled, you are fine.
If not, enable it via BIOS.

![title](/guide_images/BIOS.jpg)



###### 2. Sign in and install [docker desktop](https://docs.docker.com/desktop/install/windows-install/)

###### 3. Create a dockerfile
```
FROM python:3.9

WORKDIR /code

COPY ./src/requirements.txt /code/requirements.txt
RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

COPY ./src /code/app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

###### 4. Create a docker-compose.yaml
```
version: "3.4"
services:
  python:
    build:
      context: .
    container_name: python
    image: naveitayx/python:1.0.0
    ports:
      - 8000:8000
```

###### 5. Build Process
```
$ docker-compose build python
```

###### 6. Starting the app
```
docker-compose up python
```

###### 7. Install Kubernetes command-line tool (kubectl) and add it to PATH env variables
[kubernetes.io](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)

![title](/guide_images/kubectl.PNG)

###### 8. Kubernetes
Install Kubernetes via docker desktop.

![title](/guide_images/Kubernetes.PNG)

To test whether it works
```
$ kubectl get nodes
```
![title](/guide_images/get_nodes.PNG)

You should see docker for desktop comes with single node kubernetes cluster.

Now before we start with kubernetes we're gonna want to make sure we push out the image to docker registry.
```
$ docker-compose push python
```
![title](/guide_images/push_image_to_docker_reg.PNG)

###### 9. Kubernetes deployment
Create a deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deploy
  labels:
    app: example-app
    test: test
spec:
  selector:
    matchLabels:
      app: example-app
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      labels:
        app: example-app
    spec:
      containers:
      - name: example-app
        image: naveitayx/python:1.0.0
        imagePullPolicy: Always
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "50m"
          limits:
            memory: "256Mi"
            cpu: "500m"

```
Define
```
$ kubectl apply -f kubernetes/deployments/deployment.yaml
```
![title](/guide_images/deployment.PNG)

You'll see deployments got created, check it.
```
$ kubectl get deploy
```
```
$ kubectl get pods
```
![title](/guide_images/deploy_pods.PNG)

###### 10. Enable traffic
Create a service.yaml
```
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  type: LoadBalancer
  selector:
    app: example-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
```
Let's map the 8000 port to 80.

Apply the service.
```
kubectl apply -f kubernetes/services/service.yaml
```
![title](/guide_images/service.PNG)

Get service.
```
kubectl get svc
```
![title](/guide_images/get_service.PNG)

###### 11. Application is live running inside Kubernetes
That's all, the application is live running inside Kubernetes. 

![title](/guide_images/application_is_live.PNG)

<br/><br/>

### References
* [Collatz conjecture](https://en.wikipedia.org/wiki/Collatz_conjecture)
* [Kubernetes Documentation](https://kubernetes.io/docs/home/)
* [Docker desktop](https://docs.docker.com/desktop/install/windows-install/)
* [fastAPI in Containers](https://fastapi.tiangolo.com/deployment/docker/)
* [TechWolrd with Nana channel](https://www.youtube.com/watch?v=s_o8dwzRlu4)
* [That DevOps Guy channel](https://www.youtube.com/watch?v=d1ZMnV4yM1U)
