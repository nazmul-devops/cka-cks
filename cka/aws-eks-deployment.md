## Microservice Application deployment on AWS EKS with Ingress and SSL Certificate

##### Installing AWS CLI 

```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"

sudo installer -pkg AWSCLIV2.pkg -target /

aws --version

## Configure AWS CLI

aws configure

aws configure list
```

##### Installing eksctl 
```bash
brew tap weaveworks/tap

brew install weaveworks/tap/eksctl

eksctl version
```
##### Create RSA Key
```bash
ssh-keygen

# set location /home/ashik/.ssh/inneedc-cluster
```
##### Create AWS EKS Cluster

```bash
cd cluster-creation

nano cluster.yaml 

publicKeyPath: /Users/nazmul/.ssh/inneed-eks.pub 

# set as per you location and name

eksctl create cluster -f cluster.yaml --dry-run

after checking everything run 

eksctl create cluster -f cluster.yaml

# After wating for eksctl cluster deployment run (It will take roughly 10-15 minutes)

eksctl get cluster

kubectl get nodes -o wide
```

##### Installing helm

```bash
brew install helm

helm version
```

##### Deploying the ingress controller to the cluster

```bash
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace

kubectl get all -n ingress-nginx
```

##### Accessing AWS ECR For application image

```bash
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 138645654058.dkr.ecr.ap-south-1.amazonaws.com

# After running the command you will see 
Login Succeeded
```

##### Deploying AWS ECR Image pull secret

```bash

cat ~/.docker/config.json | base64

# copy the hash code

nano docker-secret-for-eks.yaml

# paste below code 
apiVersion: v1
kind: Secret
metadata:
  name: docker-registry-secret
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: 
    # paste the hash code here
    
kubectl create ns augmedix-dev

kubectl get ns 

kubectl apply -f docker-secret-for-kubectl.yml -n augmedix-dev

kubectl get secrets -n augmedix-dev
```

##### Deploying Cert Manager with helm

```bash 
helm repo add jetstack https://charts.jetstack.io

helm repo update

helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.11.0 \
  --set installCRDs=true

# check everything is installed in cert-manager namespace.
kubectl get pods -n cert-manager 

# Get api key from cloudflare. then apply this.
kubectl apply -f secret-cloudflare.yml --namespace=cert-manager

# Add certificate issuer to whole cluster.
kubectl apply -f clusterissuer-acme.yml --namespace=cert-manager

# Apply certificate. certtificate can be obtained for a fixed namespace.
kubectl apply -f ../augmedix-dev-deploy/certificate-for-augmedix.yaml -n augmedix-dev

kubectl get secrets -n cert-manager

# check if got the certificate obtained and status
kubectl describe certificaterequest -n augmedix-dev

kubectl describe certificate -n augmedix-dev
```

##### Deploying the Augmedix frontend service and accessing from web

```bash 
nano frontenddeployment.yaml

# change the secret name as yours:

 imagePullSecrets:
        - name: aws-ecr-registry-secret

# change the host as your desired and certificate secrets from augmedix-dev

  tls:
    - hosts:
        - augmedix.inneed.cloud
      secretName: augmedix-cert

# and everywhere namespace augmedix-dev

kubectl apply -f frontenddeployment.yaml -n augmedix-dev

kubectl get all -n augmedix-dev

kubectl get ingress -n augmedix-dev

# create cname record in cloudflare 
name - augmedix-dev 

value ingress ADDRESS - a15965578cd9d4bca93f3f7259194915-1064872734.ap-south-1.elb.amazonaws.com

DNS Only

curl https://augmedix-dev.inneed.cloud/

open browser and hit https://augmedix-dev.inneed.cloud/ to access the application

```

##### Cleaning the resources:

```bash

eksctl delete cluster --name=inneed --region=ap-south-1
```

