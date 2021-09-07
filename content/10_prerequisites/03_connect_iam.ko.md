---
title: "3. Cloud9에 IAM Role 연결"
chapter: false
weight: 13
---

AWS Cloud9을 프로비저닝하면 AWS Cloud9은 제한된 권한만을 가지도록 구성됩니다. 우리는 Hands-on을 위해서 AWS Cloud9 인스턴스에 **2-1. IAM Role 생성**에서 생성한 IAM Role을 연결할 것입니다.

아래 명령을 사용하여 현재 AWS Cloud9 환경에서 사용중인 Credential 정보를 다음과 같이 확인 합니다.

```
aws configure list
```

![image](/images/10_prequisites/cloud9_06.png)

Type이 **shared-credentials-file** 인것을 확인 할 수 있습니다. AWS Cloud9 이 프로비저닝 될 때 제한된 권한을 갖는 AWS managed temporary credential이 `~/.aws/credentials` 에 생성된 것을 알 수 있습니다.

AWS Cloud9 콘솔 우측 상단의 톱니바퀴 아이콘을 눌러서 Preferences 메뉴로 들어 갑니다.
![image](/images/10_prequisites/cloud9_07.png)

**AWS Settings** 메뉴에서 **'AWS managed temporary credential’**을 **disable** 합니다.
![image](/images/10_prequisites/cloud9_08.png)

다시 `aws configure list` 명령을 사용하여 현재 AWS Cloud9 환경에서 사용중인 Credential 정보를 확인 하면 다음과 같습니다.
![image](/images/10_prequisites/cloud9_09.png)

이제 EC2 콘솔로 이동하여 AWS Cloud9 인스턴스에 미리 만들어 두었던 IAM Role을 연결합니다. **Instances -> Actions -> Security -> Modify IAM role**
![image](/images/10_prequisites/cloud9_10.png)

**ChaosEngineeringWorkshop-Admin** 을 선택하고 저장합니다.
![image](/images/10_prequisites/cloud9_11.png)

AWS Cloud9 에 ROLE이 연결된 것을 확인하기 위해 다시한번 `aws configure list` 명령을 사용하여 Credential 정보를 확인합니다. **Type**의 값이 **iam-role** 인 것을 확인할 수 있습니다.
![image](/images/10_prequisites/cloud9_12.png)

이제 region 정보를 `aws configure set region us-east-1` 명령어를 사용하여 **us-east-1**으로 지정합니다.
![image](/images/10_prequisites/cloud9_13.png)

