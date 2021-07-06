---
title: "EKSCTL 사용해서 클러스터 생성하기"
chapter: false
weight: 10
---

# 준비사항

이 모듈을 실습하기 위해서는 eksctl 바이너리를 내려받아야한다:
```sh
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv -v /tmp/eksctl /usr/local/bin
```

내려받은 파일이 잘 설치 되었는지 확인한다:
```sh
eksctl version
```

Bash 자동완성 기능을 활성화한다.
```sh
eksctl completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

# EKS 생성
{{% notice warning %}}
EKS 1.19 버전을 배포하기 위해서는 `eksctl` 버전이 0.38.0 보다 최신이어야 한다. 가장 최신 버전을 준비한다.
{{% /notice %}}

다음, `aws sts get-caller-identity` 명령을 이용하여 `ChaosEngineeringWorkshop-Admin` 역할과 인스턴스 ID가 잘 출력되는 지 점검한다. 
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
