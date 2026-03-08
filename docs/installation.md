# Installation

## Start Minikube

minikube start --cpus=4 --memory=8192 --disk-size=40g

## Create namespaces

kubectl create namespace observability
kubectl create namespace app