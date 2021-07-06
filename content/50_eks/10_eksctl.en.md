---
title: "LAUNCH USING EKSCTL"
chapter: false
weight: 10
---

# Prerequisites

For this module, we need to download the eksctl binary:
```sh
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv -v /tmp/eksctl /usr/local/bin
```

Confirm the eksctl command works:
```sh
eksctl version
```

Enable eksctl bash-completion
```sh
eksctl completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

# Launch EKS
{{% notice warning %}}
`eksctl` version must be 0.38.0 or above to deploy EKS 1.19, get the latest version.
{{% /notice %}}

Run aws sts get-caller-identity and validate that your ARN contains `ChaosEngineeringWorkshop-Admin` and Instance Id.
```sh
{
    "Account": "123456789012",
    "UserId": "AROA1SAMPLEAWSIAMROLE:i-01234567890abcdef",
    "Arn": "arn:aws:sts::123456789012:assumed-role/ChaosEngineeringWorkshop-Admin/i-01234567890abcdef"
}
```

## Create an EKS cluster

Create an eksctl deployment file (eksworkshop.yaml) use in creating your cluster using the following syntax:

```sh
cat << EOF > eksworkshop.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eksworkshop-eksctl
  region: ${AWS_REGION}
  version: "1.19"

availabilityZones: ["${AZS[0]}", "${AZS[1]}", "${AZS[2]}"]

managedNodeGroups:
- name: nodegroup
  desiredCapacity: 3
  instanceType: t3.small
  ssh:
    enableSsm: true

# To enable all of the control plane logs, uncomment below:
# cloudWatch:
#  clusterLogging:
#    enableTypes: ["*"]

secretsEncryption:
  keyARN: ${MASTER_ARN}
EOF
```

Next, use the file you created as the input for the eksctl cluster creation.
```sh
eksctl create cluster -f eksworkshop.yaml
```

{{% notice info %}}
Launching EKS and all the dependencies will take approximately 15 minutes
{{% /notice %}}

# Test the Cluster

Confirm your nodes:
```sh
kubectl get nodes # if we see our 3 nodes, we know we have authenticated correctly
```

Export the worker role name for use throughout the workshop:
```sh
STACK_NAME=$(eksctl get nodegroup --cluster eksworkshop-eksctl -o json | jq -r '.[].StackName')
ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export ROLE_NAME=${ROLE_NAME}" | tee -a ~/.bash_profile
```

## Congratulations!

🎉  You now have a fully working Amazon EKS Cluster that is ready to use! Before you move on to any other labs, make sure to complete the steps on the next page to update the EKS Console Credentials.
