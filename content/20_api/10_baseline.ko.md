---
title: "3.1 API 응답 시간 측정 및 안정 상태 정의"
chapter: false
weight: 10
---

## 정상상태 정의
데모로 구축된 마이크로서비스는 쇼핑몰의 상품정보 페이지를 보여주기 위한 API를 제공하고 있습니다. 상품정보 페이지의 조회가 오래 걸릴 경우, 사용자는 사이트를 이탈할 가능성이 높습니다.

따라서 데모 시스템의 정상상태는 **p90을 기준으로 1초 이내의 상품정보조회 API 응답처리시간**입니다.

---

## 모니터링 방안

모니터링은 Amazon CloudWatch를 이용합니다. 정상상태를 확인하기 위해 product-composite의 ALB TargetResponseTime 메트릭을 모니터링합니다.

AWS 콘솔에서 CloudWatch 서비스로 이동한 후, Dashboards 메뉴를 선택하고, 실습환경 생성 과정을 통해서 만들어진 chaosMonitoringDashBoard로 들어갑니다.
![image](/images/20_api/baseline_01.png)

Dashboards 하단에 있는 product-composite 서비스와 관련된 메트릭 정보를 확인합니다.
![image](/images/20_api/baseline_02.png)

product-composite/TargetResponse/p90 메트릭의 경우 0.3~0.4초 내외의 응답시간을 보여주고 있습니다.
![image](/images/20_api/baseline_03.png)

---

## 가설구축 및 예상결과 정의

데모 애플리케이션은 recommendation 서비스에 지연이 발생하여도 최종 사용자 경험에 영향을 주지 않거나 1분 이내의 짧은 시간만 영향을 주어야 합니다.
