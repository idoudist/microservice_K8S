/********************** local used files ************************/

#platforms service depoyment config file
platforms-depl.yaml

#commands service depoyment config file
commands-depl.yaml

#ingress config file
ingress-srv.yaml

#node entry for platforms service
platforms-np-srv.yaml

#local persistent volume claim
local-pvc.yaml

#mssql platform deployment file
mssql-plat-depl.yaml

#rabbitmq deployment file
rabbitmq-depl.yaml

/********************** cloud used files ************************/

#ingress-nginx installation file
https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.0/deploy/static/provider/aws/deploy.yaml

/***********welcome to the Kuberntees setup command**********/

/****** view info ******/

#version
kubectl version

#get kubernetes namespaces 
kubectl get namespace

#deployment status
kubectl get deployments

#pods status (pods are containers wrapper , pod can contain one or many containers)
kubectl get pods

#pods status in specefic namespace 
kubectl get pods --namespace=ingress-nginx

#services status
kubectl get services

#services status in specefic namespace 
kubectl get services --namespace=ingress-nginx

#storage class status
kubectl get storageclass
#different persisting volume are :
#1 persistent volume claim
#2 persistent volume
#3 storage class

#persistent volume claim status
kubectl get pvc

/****** Crete Update and Delete config *****/

#apply configuration
kubectl apply -f platforms-depl.yaml
#platforms-depl.yaml is the config file name

#delete deployment
kubectl delete deployment <deploymet name>
# you can get deployment name with "kubectl get deployments"

#restart deployment with latest docker image
kubectl rollout restart deployment <deploymet name>
# you can get deployment name with "kubectl get deployments"

#install ingress nginx (for managing access to our cluster)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.0/deploy/static/provider/aws/deploy.yaml
#this config is for docker descktop go to official site for other config
#official site : https://kubernetes.github.io/ingress-nginx/deploy/

#add secrets
kubectl create secret generic mssql --from-literal=SA_PASSWORD="pa55w0rd!"
# name : mssql
# key : SA_PASSWORD
# value : "pa55w0rd!"
/******* Docker jobs *******/

#1 build platform docker image (make sure yopu are in the same directory)
docker build -t idoudist/platformservice .

#2 push platform docker image to dockerhub (make sure yopu are in the same directory)
docker push idoudist/platformservice

#3 build command docker image (make sure yopu are in the same directory)
docker build -t idoudist/commandsservice .

#4 push command docker image to dockerhub (make sure yopu are in the same directory)
docker push idoudist/platformservice

/******* Kubernetes jobs *******/

#1 install ingress-inginx
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.0/deploy/static/provider/aws/deploy.yaml

#2 apply platforms service file (make sure yopu are in the same directory)
kubectl apply -f platforms-depl.yaml

#3 apply commands service file (make sure yopu are in the same directory)
kubectl apply -f commands-depl.yaml

#4 apply node port service file (make sure yopu are in the same directory)
kubectl apply -f platforms-np-srv.yaml

#4.5 add hosts to system 32 ( kind of dns config)
open : C:\Windows\System32\drivers\etc\hosts
add this line : 
   127.0.0.1 acme.com

#5 apply ingress config file  (make sure yopu are in the same directory)
kubectl apply -f ingress-srv.yaml

#6 apply persistent storage claim file (make sure yopu are in the same directory)
kubectl apply -f local-pvc.yaml

#7 create secret
kubectl create secret generic mssql --from-literal=SA_PASSWORD="pa55w0rd!"

#8 execute mssql config file (make sure yopu are in the same directory)
kubectl apply -f mssql-plat-depl.yaml

#9 execute rabbitmq config file (make sure yopu are in the same directory)
kubectl apply -f rabbitmq-depl.yaml

/******* Kubernetes restarting jobs *******/

#1 restart platform deployment
kubectl rollout restart deployment platforms-depl

#2 restart command deployment
kubectl rollout restart deployment commands-depl
