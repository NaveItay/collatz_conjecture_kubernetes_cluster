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
FROM python:3.7.3-alpine3.9

RUN mkdir -p /app
WORKDIR /app

COPY ./src/requirements.txt /app/requirements.txt
RUN pip install -r requirements.txt

COPY ./src/ /app/
ENV FLASK_APP=server.py

CMD uvicorn server:app --reload --port 5000
```

###### 4. Create a docker-compose.yaml
```
version: "3.4"
services:
  python:
    build:
      context: .
    container_name: collatz_conjecture
    image: naveitayx/collatz_conjecture:1.0.0
    ports:
      - 5000:5000
```

###### 5. Build Process
```
$ docker-compose build collatz_conjecture
```

###### 6. Starting the app
```
docker-compose up collatz_conjecture
```

###### 7. Install Kubernetes command-line tool (kubectl) and add it to PATH env variables
[kubernetes.io](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)
![title](/guide_images/kubectl.PNG)




