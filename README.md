# DEVOPS Heroes
> cbarange | 3th Feb 2022
---

**Vidéo**

https://youtu.be/NFkpZ6etW7k


DevopsHeroes - CA GIP - Team 8 - 2022 - 2nd place

On February 4, we participated in the DEVOPS HEROES Challenge By CréditAgricoleGroupInfrastructurePlatform (CA-GIP). I went to Montpellier with Jules (@j_peguet) and Alexis (@alexis_lg44) and we reached 2nd place out of 12 teams and 1, 2 and 4th for the individual ranking 😎. We provided full Devops structure based on GKE, Gitlab, SonnarCloud... 

Thanks to CAGIP ❤️

Github 👉 https://github.com/cbarange/DevopsHeroes_CA-GIP

GitLab 👉 https://gitlab.com/devops-heroes-team-8/hackathon-api

Full Video 👉 https://www.youtube.com/watch?v=Q1X9GSG-tAg&t=5105s

Demo 👉 https://www.youtube.com/watch?v=ENzylDaheck




## Hackathon-Api

https://github.com/ca-gip/hackathon-api

```bash
wget https://go.dev/dl/go1.17.6.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.6.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
go version

sudo apt install -y mongodb
```


```bash
docker run -p 27017:27017 --name some-mongo -d mongo

export MONGOURI="mongodb://127.0.0.1:27017/donation?retryWrites=true&w=majority"

sudo apt-get install -y xvfb libfontconfig wkhtmltopdf

wget https://github.com/ca-gip/hackathon-api/releases/download/v0.1.1/hackathon-reward-linux-amd64
mv hackathon-reward-linux-amd64 hackathon-reward
chmod +x hackathon-reward
mv ./hackathon-reward /usr/local/bin/hackathon-reward

git clone https://github.com/ca-gip/hackathon-api.git
cd hackathon-api
go mod tidy
go run main.go

sudo docker run  --platform linux/amd64 -e $MONGOURI -p 8080:8080 -d --name hackathon --network=host cagip/hackathon-api:dev
```


## Install `gcloud` & `kubectl`

```bash
sudo apt update
sudo apt-get install -y apt-transport-https ca-certificates gnupg
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
# OR
# echo "deb https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
sudo apt-get update && sudo apt-get install -y google-cloud-sdk
gcloud init


sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

## VM Instance & SSH

```bash
export PROJECT_ID=qwiklabs-gcp-04-e117394e957f

gcloud auth login # Open browser, login, copy token and submit
gcloud auth list # list the active account
gcloud config set project $PROJECT_ID
gcloud config list
#gcloud components list

gcloud beta interactive # When using the interactive mode, press TAB to complete file path and resource arguments

gcloud config get-value compute/zone # see what your default region and zone settings
gcloud config get-value compute/region

gcloud compute project-info describe --project $PROJECT_ID # Find the default zone and region metadata values in the output
export ZONE=us-central1-f # us-central1 region denotes a region in the Central United States that has zones us-central1-a, us-central1-b, us-central1-c, and us-central1-f

export VM_NAME=gcelab2-vm-name
gcloud compute instances create $VM_NAME --project=$PROJECT_ID --machine-type=n1-standard-2 --zone=$ZONE --tags=http-server,https-server # Start VM with http/s allowed
gcloud compute instances describe $VM_NAME
gcloud compute firewall-rules create RSUB_PORT_RULE --allow tcp:52698 --source-tags=$VM_NAME --source-ranges=0.0.0.0/0 --description="Open port for RSUB"

gcloud compute ssh $VM_NAME --zone $ZONE
gcloud compute ssh $VM_NAME --zone $ZONE -- -R 52698:localhost:52698
gcloud compute ssh $VM_NAME --zone $ZONE -- -NL 52698:localhost:52698

sudo su -
apt-get update
apt-get install nginx -y
netstat -plunt

# - RSUB -
#ssh -R 52698:localhost:52698 server_user@server_address
sudp apt install -y wget 
sudo wget -O /usr/local/bin/rsub https://raw.github.com/aurora/rmate/master/rmate
sudo chmod +x /usr/local/bin/rsub

rsub path_to_file/file.txt
```

## Kubernetes

```bash

gcloud config set compute/zone us-central1-a
gcloud container clusters create [CLUSTER-NAME]
gcloud container clusters get-credentials [CLUSTER-NAME]

kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
kubectl get service
# http://[EXTERNAL-IP]:8080

gcloud container clusters delete [CLUSTER-NAME]


gsutil cp -r gs://spls/gsp021/* . # Clone the GitHub repository from the Cloud Shell command line:
kubectl create deployment nginx --image=nginx:1.10.0
kubectl get pods
kubectl expose deployment nginx --port 80 --type LoadBalancer
kubectl get services
curl http://<External IP>:80

# - Creating Pods -
cat pods/monolith.yaml
apiVersion: v1
kind: Pod
metadata:
  name: monolith
  labels:
    app: monolith
spec:
  containers:
    - name: monolith
      image: kelseyhightower/monolith:1.0.0
      args:
        - "-http=0.0.0.0:80"
        - "-health=0.0.0.0:81"
        - "-secret=secret"
      ports:
        - name: http
          containerPort: 80
        - name: health
          containerPort: 81
      resources:
        limits:
          cpu: 0.2
          memory: "10Mi"

kubectl create -f pods/monolith.yaml
kubectl get pods
kubectl describe pods monolith


# - Interacting with Pods -
kubectl port-forward monolith 10080:80
curl http://127.0.0.1:10080
curl http://127.0.0.1:10080/secure
curl -u user http://127.0.0.1:10080/login
TOKEN=$(curl http://127.0.0.1:10080/login -u user|jq -r '.token')

curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure
kubectl logs monolith
kubectl logs -f monolith
curl http://127.0.0.1:10080

kubectl exec monolith --stdin --tty -c monolith /bin/sh
ping -c 3 google.com
exit

# - Services -
cd ~/orchestrate-with-kubernetes/kubernetes
cat pods/secure-monolith.yaml

kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf
kubectl create -f pods/secure-monolith.yaml

cat services/monolith.yaml
kind: Service
apiVersion: v1
metadata:
  name: "monolith"
spec:
  selector:
    app: "monolith"
    secure: "enabled"
  ports:
    - protocol: "TCP"
      port: 443
      targetPort: 443
      nodePort: 31000
  type: NodePort

kubectl create -f services/monolith.yaml

gcloud compute firewall-rules create allow-monolith-nodeport \
  --allow=tcp:31000
gcloud compute instances list


# - Adding Labels to Pods -
kubectl get pods -l "app=monolith"
kubectl get pods -l "app=monolith,secure=enabled"
kubectl label pods secure-monolith 'secure=enabled'
kubectl get pods secure-monolith --show-labels
kubectl describe services monolith | grep Endpoints
gcloud compute instances list
curl -k https://<EXTERNAL_IP>:31000

# - Creating Deployments -
cat deployments/auth.yaml
kubectl create -f deployments/auth.yaml
kubectl create -f services/auth.yaml
kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml
kubectl get services frontend
curl -k https://<EXTERNAL-IP>


kubectl get deployments
kubectl get replicasets
kubectl get pods

kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml


kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml
kubectl get services frontend

kubectl explain deployment.spec.replicas
kubectl scale deployment hello --replicas=5
kubectl get pods | grep hello- | wc -l
kubectl get pods | grep hello- | wc -l

kubectl edit deployment hello
kubectl get replicaset
kubectl rollout history deployment/hello

kubectl rollout pause deployment/hello
kubectl rollout status deployment/hello

kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'

kubectl rollout resume deployment/hello
kubectl rollout status deployment/hello

kubectl rollout undo deployment/hello
kubectl rollout history deployment/hello


kubectl apply -f services/hello-blue.yaml


apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
        track: stable
        version: 2.0.0
    spec:
      containers:
        - name: hello
          image: kelseyhightower/hello:2.0.0
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
          resources:
            limits:
              cpu: 0.2
              memory: 10Mi
          livenessProbe:
            httpGet:
              path: /healthz
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            periodSeconds: 15
            timeoutSeconds: 5
          readinessProbe:
            httpGet:
              path: /readiness
              port: 81
              scheme: HTTP
            initialDelaySeconds: 5
            timeoutSeconds: 1

kubectl create -f deployments/hello-green.yaml
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
kubectl apply -f services/hello-green.yaml
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
kubectl apply -f services/hello-blue.yaml
curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version





```




