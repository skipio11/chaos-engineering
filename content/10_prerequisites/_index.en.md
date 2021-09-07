---
title: "Module 2: Prerequisites"
chapter: true
weight: 20
---

# Prerequisites

In this workshop, you will build the following architecture.
![image](/images/architecture.png)

The demo application composed of four independent microservices: product, review, and recommendation. 

Also, the product-composite service is implemented that provides an API for integrating product information from client at once. This service calls the API to rest of the microservices to inquiry information and send it to client as a response after combining it. Each service is written in spring-boot and communicates with Eureka for service discovery.

To make the demonstration more realistic, the load-generator is configured to generate constant traffics to microservices environment. And the metrics and trace information of each service is collected through Amazon CloudWatch and AWS X-Ray.

{{% notice info %}}
This microservices made for demonstration so, this is not suitable for production environments. When you start to design microservices-based cloud-native applications, please contact AWS and we will be happy to help you.
{{% /notice %}}

{{% children showhidden="true" %}}
