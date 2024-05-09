# Coworking Space Service Extension
The Coworking Space Service is a set of APIs that enables users to request one-time tokens and administrators to authorize access to a coworking space. This service follows a microservice pattern and the APIs are split into distinct services that can be deployed and managed independently of one another.

For this project, you are a DevOps engineer who will be collaborating with a team that is building an API for business analysts. The API provides business analysts basic analytics data on user activity in the service. The application they provide you functions as expected locally and you are expected to help build a pipeline to deploy it in Kubernetes.

## PHUHV10 HOW TO RUN
### Step 1
Set up codebuild, ECR on AWS
### Step 2
```
eksctl create cluster --profile udacity --name phuhv10-cluster --region us-east-1 --nodegroup-name phuhv10-nodes --node-type t3.small --nodes 1 --nodes-min 1 --nodes-max 2
```
#### Add context for local kubectl
```
aws eks update-kubeconfig --region us-east-1 --name phuhv10-cluster --profile udacity
```
#### Add Policy
```
aws iam attach-role-policy --role-name eksctl-phuhv10-cluster-nodegroup-p-NodeInstanceRole-rX5Z1qfPYkaK --policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy --profile udacity
```
#### Create Add on
```
aws eks create-addon --cluster-name phuhv10-cluster --addon-name amazon-cloudwatch-observability --profile udacity
```
### Step 3 Setup postgresql
```
cd postgresql
kubectl apply -f pvc.yaml
kubectl apply -f pv.yaml
kubectl apply -f postgresql-deployment.yaml
kubectl apply -f postgresql-service.yaml
```
### Step 4 Forward postgresql service to local and run scripts
```
kubectl port-forward service/postgresql-service 5433:5432 &
```
### Step 5 Setting application
```
cd deployment
kubectl apply -f configmap.yaml
kubectl apply -f secrets.yml
kubectl apply -f coworking.yaml
```

### Step 6 After run successfully Run this command to shutdown all resources
eksctl delete cluster --name phuhv10-cluster --region us-east-1