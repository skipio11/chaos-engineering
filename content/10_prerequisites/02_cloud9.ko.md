---
title: "2. Cloud9 환경구성"
chapter: false
weight: 12
---

## Cloud9 Environment 생성하기

Cloud9 서비스로 이동합니다.
![image](/images/10_prequisites/cloud9_01.png)

**Create environment** 버튼을 클릭하여 Cloud9 Environment 생성을 시작합니다.
![image](/images/10_prequisites/cloud9_02.png)

**Name**에 `chaos-fis-workshop-cloud9-env` 를 입력하고 **Next step** 버튼을 클릭합니다.
![image](/images/10_prequisites/cloud9_03.png)

**Configure settings** 에서는 변경사항 없이 기본값으로 설정하고 **Next step** 버튼을 클릭합니다.

**Review** 페이지에서 **Create environment** 버튼을 클릭합니다.

AWS Cloud9의 프로비저닝이 완료되면 다음과 같은 통합개발환경(IDE) 화면을 보실 수 있습니다.
![image](/images/10_prequisites/cloud9_04.png)

---

## 필수 구성 요소 설치

Cloud9에서 하단의 터미널 영역에 다음 명령어를 실행하여 필수 구성 요소를 설치합니다.

```
sudo yum install -y jq
sudo npm install -g aws-cdk
cdk --version
```
