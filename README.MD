# Cluster operations

**Create cluster using a config file:**

```
eksctl create cluster -f stacks-cluster.yaml

```
## Get Cluster Details

```
eksctl get cluster

```
## Delete the cluster

```
eksctl delete cluster -f cluster.yaml  < --wait >

or

eksctl  delete cluster --name [ CLUSTER_NAME ] --region [REGION]

```

## Update config details

```
cat ~/.kube/config

aws eks update-kubeconfig --name stacks-dev --region uk-west-2

```

---

# Tool Setup

## Install jq, envsubst (from GNU gettext utilities), git  and bash-completion

```
sudo yum install -y  jq gettext bash-completion moreutils

sudo yum install -y git

```
## Install AWS CLI Version 2

```

cd $HOME

aws --version

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

which aws

```

## Update AWS CLI Version 2

```
cd $HOME

which aws --> (/usr/bin/aws)

sudo ./aws/install --bin-dir /usr/bin --install-dir /usr/bin/aws-cli --update

aws --version

```

## Configure AWS CLI 

```
aws configure

AWS Access Key ID  -->  credentials of the account which will be used to deploy the cluster

AWS Secret Access Key --> credentials of the account which will be used to deploy the cluster

Default region name --> eu-west-2

Default output format --> json

```


## Install Kubectl
```

curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/linux/amd64/kubectl

chmod +x ./kubectl

echo $PATH

mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin



-------------------  OR -----------------------------


sudo curl --silent --location -o /usr/local/bin/kubectl \

https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl

sudo chmod +x /usr/local/bin/kubectl



kubectl version --short --client

```

## Install Eksctl

```

curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /usr/bin

eksctl version

eksctl help

```

---

# Environment  Setup

This is will work if your dev machine is in aws cloud.


```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))



test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set


echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
echo "export AZS=(${AZS[@]})" | tee -a ~/.bash_profile


aws configure set default.region ${AWS_REGION}
aws configure get default.region

```


# Basic Deployment To test the cluster:

```
cd nginx

cat nginx-deployment.yaml 

cat nginx-svc.yaml

```

## Create The Servcie

```
kubectl apply -f ./nginx-svc.yaml

kubectl get service -o wide
```

## Create The Deployment

```
kubectl apply -f ./nginx-deployment.yaml

kubectl get deploy,svc,po

```

## Test The Deployment

```
curl "<LOAD_BALANCER_DNS_HOSTNAME>"

```
