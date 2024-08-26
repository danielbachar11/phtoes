# deploy website on k8 that print welcom to New York 


## Task Description

deploy nginx website on local k8 

## Steps 

- **Step 1**:create github repo 
- **Step 2**:create basic html website  
- **step 3**: create piple that build and push image to docker hub   
- **step 4**:deploy our image on kubernetes




### Prerequisites

- kubernetes cluster  

### Steps

1. **create github repo**

create repo that contain folders like this method
 
 1.1 create folder name images/custom-nginx and inside this folder put your Dockerfile
 1.2 inside this folder create new folder and give name app and put there our html file 
 1.3 create folders kubernetes/local-cluster and put there kubernetes deployment file

2. **create basic html website:**
create repo that contain folders
create basic html webpage and push to github 
like this 
 

3. **create piple that build and push image to docker hub  :**
name: build and push nginx website 
```bash
on:
 
 push:
   branches: ["main"]
    

jobs:
  docker_build_push:
    runs-on: [self-hosted,Linux,X64] ##we running our pipline on our runner with our runner labels
    steps:
      - name: Check out the repository
        uses: actions/checkout@v3
       
      - name: docker login 
        run: docker login -u ${{secrets.DOCKER_USER}} -p ${{secrets.DOCKER_PASSWORD}}
      
      - name: build docker image and push to docker hub 
        run: |
          
          cd images/custom-nginx
          docker build -t danielbachar/nginx_website:latest .
          docker push danielbachar/nginx_website:latest
 ```
4. **create secret with kubernetes context:**  
deploy our deployment 
5. **run our pipline for deploy nginx container on kubernetes :**
now we create another pipline for deploy our kubernetes deployment
```bash
name: deploy to locak k8 
on:
  push:
    branches: ["main"]
  workflow_dispatch:  
jobs:
  checkout:
    runs-on: [self-hosted,Linux,X64]
    steps:
      - name: Check out the repository
        uses: actions/checkout@v3 
      - name: Create .kube directory
        run: mkdir -p ~/.kube 
      
      - name: Set up kubeconfig
        env:
          KUBECONFIG: ${{ secrets.KUBECONFIG }}  # Use a kubeconfig stored in GitHub Secrets
        run: |
          echo "$KUBECONFIG" > ~/.kube/config
      - name: use context 
        run: |
         kubectl config use-context kind-daniel 
         kubectl apply -f kubernetes/local-cluster/deployment.yaml  
  
```

