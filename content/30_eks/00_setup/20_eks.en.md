---
title: "Launch EKS Cluster"
chapter: false
weight: 20
---

## Prerequisites

Run aws sts get-caller-identity and validate that your ARN contains `ChaosEngineeringWorkshop-Admin` and Instance Id.
```sh
{
    "Account": "123456789012",
    "UserId": "AROA1SAMPLEAWSIAMROLE:i-01234567890abcdef",
    "Arn": "arn:aws:sts::123456789012:assumed-role/ChaosEngineeringWorkshop-Admin/i-01234567890abcdef"
}
```

## Launch EKS

For this module, we need to download the scripts to create an EKS cluster from the repository. This module will use AWS CDK for infrastructure management.
```sh
git clone https://github.com/dns-msa/fisworkshop.git
```

## Create an EKS cluster

Run the deploy deploy to create an EKS cluster:
```sh
cd ~/environments/fisworkshop/eks/cdk
bash deploy.sh
```

{{% notice info %}}
Launching EKS and all the dependencies will take approximately 15 minutes :coffee:
{{% /notice %}}

## Test the Cluster

Confirm your nodes:
```sh
kubectl get nodes # if we see our 3 nodes, we know we have authenticated correctly
```

## Congratulations!

:tada: You now have a fully working Amazon EKS Cluster that is ready to use! Before you move on to any other labs, make sure to complete the steps on the next page to update the EKS Console Credentials.