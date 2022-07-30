# Kubernetes cluster setup guide
![title](/guide_images/intro.png)

## introduction

A Kubernetes cluster is a set of nodes that run containerized applications. Containerizing applications packages an app with its dependences and some necessary services. They are more lightweight and flexible than virtual machines.

This reposity will guide you how to set an application containerized and deployed on Kubernetes.

### Project stages:
1. Create a python apllication
2. Set up a kubernetes cluster and expose the code as a service. 
   - Check if virtualization is supported in your machine
   - Install docker desktop

<p>
<br />
</p>

###### 1. Create a python apllication
#
```
from fastapi import FastAPI

app = FastAPI()


@app.get("/collatz_conjecture")
def clean(number: int):
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

###### 2. Set up a kubernetes cluster and expose the code as a service. 
#

###### Check if virtualization is supported in your machine
Open cmd and write
```
systeminfo
```
![title](/guide_images/virtualization_is_supported.PNG)
if virtualization in firmware is enabled, you are fine.
If not, enable it via BIOS.
![title](/guide_images/BIOS.jpg)



###### Sign in and install [docker desktop](https://docs.docker.com/desktop/install/windows-install/)
