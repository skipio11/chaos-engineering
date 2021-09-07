---
title: "4. 포스트모텀 - 실패로부터 배우기"
chapter: false
weight: 14
---

## 그룹 토론

+ 의도한 동작은 어떤 것이었나요?
+ 실제로는 어떤 일이 발생했나요?

그런 다음 마이크로서비스 애플리케이션에 다시 액세스하십시오. 무슨 일이 있어났을까요? 아마도 결함 주입 실험에 의한 노드 종료로 인해 애플리케이션이 중단될 수 있습니다. 애플리케이션의 첫 번째 배포는 고가용성을 고려하지 않았기 때문입니다.

## 개선점 반영

클러스터 오토스케일러(Cluster Autoscaler) 은 다음의 조건을 만족하는 경우 쿠버네티스 클러스터의 크기를 자동으로 조절해주는 도구 입니다:

+ 클러스터에 자원이 부족해서 포드(Pods) 스케쥴링에 실패했을 경우.
+ 사용량이 특정 시간동안 저조했을 때, 노드에서 동작 중인 포드가 다른 노드에서 실행 가능한 경우.

클러스터 오토스케일러는 오토 스케일링 그룹(Auto Scaling groups)과의 연동 기능을 제공합니다. 클러스터 오토스케일러는 오토스케일링 그룹에서 관리하는 인스턴스의 제공되는 프로세서, 메모리, 그래픽 프로세서 측정합니다. 보다 자세한 내용은 [여기](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler/cloudprovider/aws)에서 확인할 수 있습니다.

먼저, 클러스터 오토스케일러가 잘 설치외어 있는 지 확인합니다. 만약, 오류 메시지가 보이지 않고 정상적으로 보이는 것 같다면, 여러 분의 클러스터는 스케일링 할 준비가 된 것입니다.
```sh
kubectl -n kube-system logs -f deployment/cluster-autoscaler
```

고가용성 확보를 위하여 포드를 증설합니다.
```sh
cd ~/environments/fisworkshop/eks/
kubectl apply -f kubernetes/manifest/sockshop-demo-ha.yaml
```

## 재실험을 통한 가설 검증
