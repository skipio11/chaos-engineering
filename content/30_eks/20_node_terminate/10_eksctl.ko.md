---
title: "EKSCTL 사용해서 클러스터 생성하기"
chapter: false
weight: 10
---

# 준비사항

이 모듈을 실습하기 위해서는 eksctl 바이너리를 내려받아야 합니다:
```sh
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv -v /tmp/eksctl /usr/local/bin
```

내려받은 파일이 잘 설치 되었는지 확인합니다:
```sh
eksctl version
```

Bash 자동완성 기능을 활성화합니다.
```sh
eksctl completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

# EKS 준비
{{% notice warning %}}
EKS 1.19 버전을 배포하기 위해서는 `eksctl` 버전이 0.38.0 보다 최신이어야 합니다. 가장 최신 버전을 준비합니다.
{{% /notice %}}

다음, `aws sts get-caller-identity` 명령을 이용하여 `ChaosEngineeringWorkshop-Admin` 역할과 인스턴스 ID가 잘 출력되는 지 점검합니다.
```sh
{
    "Account": "123456789012",
    "UserId": "AROA1SAMPLEAWSIAMROLE:i-01234567890abcdef",
    "Arn": "arn:aws:sts::123456789012:assumed-role/ChaosEngineeringWorkshop-Admin/i-01234567890abcdef"
}
```

## EKS 클러스터 만들기

아래 명령을 활용하여 배포 파일(fisworkshop.yaml)을 생성합니다. 이 파일을 새 클러스터 생성할 때 사용합니다:
```sh
cat << EOF > fisworkshop.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: fisworkshop-eksctl
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

다음, 아래 명령을 사용하여 클러스터를 만듭니다.
```sh
eksctl create cluster -f fisworkshop.yaml
```

{{% notice info %}}
EKS 클러스터와 관련된 의존성들을 띄우는 데 약 15분 정도 소요됩니다.
{{% /notice %}}

# 클러스터 확인

생성한 클러스터의 노드를 확인합니다:
3개의 노드가 보인다면 제대로 성성한 것으로 볼 수 있습니다.
```sh
kubectl get nodes
```

노드 그룹의 IAM 역할이름을 저장합니다. 워크샵의 다른 곳에서 활용할 예정입니다:
```sh
STACK_NAME=$(eksctl get nodegroup --cluster fisworkshop-eksctl -o json | jq -r '.[].StackName')
ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export ROLE_NAME=${ROLE_NAME}" | tee -a ~/.bash_profile
```

## 축하합니다!

🎉  당신은 잘 동작하는 EKS 클러스터를 생성하였습니다. 다음 단계로 넘어가기 전, 다음 단뎨에서 나와있는 안내를 따라 EKS 콘솔 자격증명을 갱신합니다.
