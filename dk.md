

## 1.Redis setup

<!-- *install redis -->
sudo apt update && sudo apt install redis -y
<!-- *to Start Redis: -->
sudo systemctl start redis

sudo systemctl enable redis

<!-- to check the redis is alredy running: sudo systemctl status redis -->
-to check the port=sudo netstat -tulnp | grep <port no>

<!-- *setup in local -->
 docker run --name redis -d -p <port no>:<port no> redis

<!-- *to check the redis is working: -->
 redis-cli ping ||return: PONG

<!-- *To instal redis in Go client  -->
go get github.com/redis/go-redis/v9

## 2. Docker setup

<!-- Update Existing Packages -->
sudo apt update
sudo apt upgrade -y

<!-- Install Required Packages -->
sudo apt install -y ca-certificates curl gnupg lsb-release

<!-- Add Docker’s GPG Key -->
sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \

sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

 <!-- Set Up the Docker Repository -->

echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

  <!-- Install Docker Engine -->
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

<!-- Verify Docker Installation -->
sudo docker version
sudo docker run hello-world

<!-- TO test it -->
docker run hello-world

<!-- Uninstall Old Versions  -->
sudo apt remove docker docker-engine docker.io containerd runc

<!-- installing Docker as the container runtime -->
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

<!-- *To build the docker image  -->
docker build -t <file name-app >.




## 3.kubernetes setup

<!-- *Insatll locally in linux -->
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

<!-- *kubectl to  install -->
curl -LO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

<!-- *To check kubectl -->
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check


<!-- *Make It Executable & Install -->
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

<!-- *To test it  -->
kubectl version --client

<!-- Verify Cluster  -->
kubectl get nodes
kubectl get pods -A


## 4. To install minikube for kubernetes


<!-- *To download in linux -->
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

<!-- *To install = -->
sudo install minikube-linux-amd64 /usr/local/bin/minikube

<!-- *to start the miniube with docker  -->
minikube start --driver=docker
<!-- *To start the minikube  -->
 minikube start

<!-- *If you want to access your app from the browser or curl: -->
minikube service <file name-service>


<!-- *To build the docker image  -->
docker build -t <file name>-app .

<!-- *To build the docker image in minikube env  -->
eval $(minikube docker-env)
docker build -t <file name>-app .

    <!-- ->To check the docker running in minicube  -->
    docker images | grep <file name-app > 
    
<!-- *To restart the deployment  -->
kubectl rollout restart deployment <file-deployment>

<!-- *To watch the log = -->
kubectl logs -l app=<file>-user -f

<!-- *If the changes made in the yaml file  -->
kubectl apply -f deployment.yaml

<!-- *To create config env  -->
kubectl create configmap <file>-env --from-env-file=.env
 
<!-- *To check the check config env  -->
kubectl get configmap <file>-env -o yaml

<!-- *To add  in config env after build -->
kubectl patch configmap <file>-env --type merge -p '{"data":{"REDIS_HOST":"redis:6379"}}'

<!-- *To describe the pod -->
kubectl describe pod <deployment name>-deployment-<pod name>

<!-- *To delete the pod -->
kubectl delete pod <deployment name>-deployment-<pod name>

<!-- *To know the services in the kubernetes cluster -->
kubectl get svc



