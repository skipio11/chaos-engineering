---
title: "3. Attack - EC2에 네트워크 지연처리 발생"
chapter: false
weight: 13
---

## 예상 결과 정의

데모 애플리케이션은 recommendation 서비스에 지연이 발생하여도 최종 사용자 경험에 영향을 주지 않거나 1분 이내의 짧은 시간만 영향을 주어야 합니다.

---

## 공격대상 서비스 선택 및 장애주입

실험을 위해 recommendation 서비스에서 사용하는 EC2 인스턴스 2대에 네트워크 지연을 발생시킵니다.

AWS 콘솔에서 FIS Service로 이동하고 좌측 메뉴에서 **Experiment templates** 메뉴를 선택합니다.

미리 생성된 Network Attack Template을 선택하고 Actions에서 **Start experiment** 메뉴를 클릭합니다.
![image](/images/20_ec2/experiment_01.png)

다음 화면에서 **Start experiment** 버튼을 클릭합니다.
![image](/images/20_ec2/experiment_02.png)

경고문구를 확인하고 `start` 를 입력하고 **Start experiment** 버튼을 다시 한번 클릭합니다.
![image](/images/20_ec2/experiment_03.png)

이제 recommendation 서비스에서 사용하는 EC2의 네트워크에 대하여 공격이 시작됩니다.
![image](/images/20_ec2/experiment_04.png)

Details 에 Stop Conditions이 설정된 것을 확인할 수 있습니다.
![image](/images/20_ec2/experiment_05.png)

링크를 클릭하면 CloudWatch Alarm 알람으로 정상상태(p90 1초 이내의 상품정보 API 응답처리시간)를 의미하는 TargetResponseTime p90가 1초로 설정된 것을 볼 수 있습니다.
![image](/images/20_ec2/experiment_06.png)

Actions 탭을 살펴보면 공격은 5분간 지속되며, 네트워크에 5초(5000ms)의 지연을 주입하는 것을 확인할 수 있습니다.
![image](/images/20_ec2/experiment_07.png)

Targets 탭을 살펴보면 실제 어떤 EC2가 공격대상인지 조건을 확인할 수 있습니다.
![image](/images/20_ec2/experiment_08.png)

---

## 모니터링 도구를 통한 관찰

---

## 실제 결과 확인

이제 Amazon CloudWatch에 만들어졌던 Dashboards를 통해서 모니터링 해보겠습니다. recommendation 서비스에 네트워크 지연이 발생하자, 이를 호출하는 product-composite 서비스에 지연이 발생하고 API 응답시간에 지연이 발생합니다.
![image](/images/20_ec2/experiment_09.png)

지연이 계속되자 시스템이 정상상태(p90 1초 이내의 상품정보 API 응답처리시간)를 벗어나서 CloudWatch 알람이 발생하였습니다.
![image](/images/20_ec2/experiment_10.png)

FIS에 Stop conditions으로 설정된 CloudWatch 알람으로 인해 공격이 중단되었습니다.
![image](/images/20_ec2/experiment_11.png)

공격의 중단으로 서비스는 다시 정상화 되었습니다.
![image](/images/20_ec2/experiment_12.png)

---

## Action Item 도출 및 적용


product-composite 서비스는 product, review, recommendation 서비스를 Sync방식으로 호출하고 있고, 3초의 타임아웃 시간이 설정되어 있습니다.

`~/environment/chaos-fis-workshop/product-composite/src/main/java/com/skipio/demo/chaos/fis/composite/product/AppConfig.java`

![image](/images/20_ec2/improvement_01.png)

`~/environment/chaos-fis-workshop/product-composite/src/main/java/com/skipio/demo/chaos/fis/composite/product/RecommendationService.java`

![image](/images/20_ec2/improvement_02.png)

따라서 연계된 하나의 서비스만 지연되더라도 연쇄적으로 응답에 지연이 발생하고, 이는 product-composite의 가용 스레드를 소모하여 전체시스템에 영향을 주게 되었습니다. 한 서비스의 장애가 전체 서비스의 장애로 전파된 것입니다.

---

## 그룹토론

실험 계획과 예상 결과를 작성해보기 (5분 작성 / 10분 발표 토론)

---

## 실험 반복을 통한 개선사항 확인

단순히 좀 더 짧은 타임아웃을 주는 방법도 생각해 볼 수 있으나, 지연이 길어지고 요청량이 많아지면 결국 장애로 이어집니다.

따라서 서킷브레이커 패턴을 적용하여 장애가 지속되면 서킷을 열고 폴백 응답을 보내는 방식으로 개선합니다. 이를 위해 여기에서는 resilience4j를 적용합니다.

**CircuitBreaker가 동작할 수 있도록 기존 코드에서 주석을 해제하고 저장합니다.**

CircuitBreaker 어노테이션과 fallback 함수가 설정된 것을 볼 수 있습니다.

`~/environment/chaos-fis-workshop/product-composite/src/main/java/com/skipio/demo/chaos/fis/composite/product/RecommendationService.java`

![image](/images/20_ec2/improvement_03.png)

**Cloud9에서 아래의 명령어를 실행하여 코드를 재배포 합니다.**  배포가 완료되기까지 대략 2,3분 정도 기다립니다.

```
cd ~/environment/chaos-fis-workshop
./chaos-04-redeploy-product-composite.sh

```

배포가 완료되면 **원인파악 및 개선사항 도출**로 돌아가서 다시 실험을 진행합니다. recommendation 서비스에 지속적인 지연이 발생하면, 서킷이 열리고 폴백 응답을 주어 지속적인 지연이 발생하지 않습니다.

![image](/images/20_ec2/improvement_04.png)

공격중에 Cloud9에서 아래의 명령어를 실행하여 현재의 발생하는 응답의 상세내용을 확인합니다. recommendations의 상세내역을 보면 정상적인 응답이 아니라 fallback에 설정한 응답이 나오는 것을 볼 수 있습니다.

```
cd ~/environment/chaos-fis-workshop
./chaos-05-check-response-product-composite.sh
```

![image](/images/20_ec2/improvement_05.png)

실험이 끝나면 서킷이 다시 닫히고 정상적으로 recommendations의 응답이 출력되는 것을 확인할 수 있습니다.
![image](/images/20_ec2/improvement_06.png)
![image](/images/20_ec2/improvement_07.png)

---

포스트모텀 - 실패로부터 배우기
장애나 지연이 지속될 때 문제가 있는 서비스로 계속 요청을 보내는 것은 장애의 해소를 지연시키고 오히려 확산시킬 수 있습니다. 따라서 이 때에는 [서킷브레이커 패턴](https://docs.aws.amazon.com/ko_kr/whitepapers/latest/modern-application-development-on-aws/circuit-breaker.html)을 적용하여 Fail Fast 전략을 가져가는 것이 사용자 경험을 높이고, 장애를 격리시킬 수 있는 방안입니다.

실습에서 사용한 resilience4j는 Java언어에 한하여만 적용할 수 있다는 단점이 있습니다. AWS에서는 [AWS App Mesh](https://aws.amazon.com/ko/app-mesh/?aws-app-mesh-blogs.sort-by=item.additionalFields.createdDate&aws-app-mesh-blogs.sort-order=desc&whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc) 서비스를 이용하여 사용하는 언어에 관계없이 이러한 서킷 브레이커 패턴을 적용할 수 있고, 애플리케이션의 가시성을 가져갈 수 있습니다.

또한 비즈니스에 따라 비동기 방식의 처리, 그리고 격벽패턴을 적용하는 것도 시스템을 안정적으로 운영할 수 있는 방법입니다.
