---
title: "어플리케이션 배포"
chapter: false
weight: 40
---

## kubeconfig 저장

쿠버네티스 클러스터를 kubectl로 관리하기 위해서는 aws eks update-kubeconfig 명령을 실행해야 합니다. 이 명령은 실습 환경에 kubeconfig 설정 파일을 저장합니다:
```sh
Outputs:
ClusterConfigCommand43AAE40F = aws eks update-kubeconfig --name cluster-xxxxx --role-arn arn:aws:iam::112233445566:role/yyyyy
```

AWS CDK의 출력으로 나온 명령을 실습 환경 터미널에 붙여넣기하여 실행합니다. 이 명령은 아래와 비슷한 모양을 하고 있습니다. kubeconfig 파일을 새로 생성하거나 갱신합니다:
```sh
aws eks update-kubeconfig --name cluster-xxxxx --role-arn arn:aws:iam::112233445566:role/yyyyy
```

## 어플리케이션 배포

이 실습을 위해서, 양말가게(Sock Shop) 어플리케이션을 사용할 것입니다. 이 예제는 Weaveworks라는 곳에서 처음 개발하였으며 마이크로서비스 아키텍처 기반으로 구성되어 있습니다.

다음의 명령을 이용하여 네임스페이스를 만들고 어플리케이션을 배포합니다.
```sh
cd ~/environments/fisworkshop/eks/
kubectl apply -f kubernetes/manifest/sockshop-demo.yaml
```

배포가 잘 되었는 지 확인해 보겠습니다:
```sh
kubectl -n sockshop get pod -l name=front-end
```
출력 결과가 다음과 비슷하면 정상적으로 배포가 된 것입니다:
```sh
NAME                         READY   STATUS    RESTARTS   AGE
front-end-7b8bcd59cb-wd527   1/1     Running   0          9s
```

### 개별 실습 환경
개별 실습 환경이라면, 다음 명령으로 포트 포워딩을 실행합니다.

```sh
kubectl -n sockshop port-forward svc/front-end 8080:80
```
같은 환경에서 브라우저를 시작하고 http://localhost:8080 페이지를 엽니다. 양말가게 예제의 첫 화면을 볼 수 있습니다.

### Cloud9
Cloud9 환경에서 실습을 하고 있다면, 다음과 같이 어플리케이션을 연결합니다.

```sh
kubectl -n sockshop port-forward svc/front-end 8080:80
```
Cloud9 환경에서 Preview를 누르고 다음 Preview Running Application 단추를 누릅니다. 이 메뉴는 미리보기 탭을 새로 열어서 어플리케이션을 표시합니다. 양말가게 예제의 첫 화면을 볼 수 있습니다.

![sockshop-preview](/images/30_eks/weaveworks-sockshop-frontend.png)

## 축하합니다!

:tada: 예제 어플리케이션 배포를 완료 하였습니다.