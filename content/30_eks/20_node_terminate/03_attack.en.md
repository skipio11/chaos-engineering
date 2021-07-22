---
title: "3. Fault Injection Experiment"
chapter: false
weight: 13
---

## Start Experiment

Make sure that all your EKS node group instances are running. 
```sh
kubectl get nodes
```

Go to the AWS FIS service page and select `TerminateEKSNodes` from the list of experiment templates. Then use the on-screen **Actions** button to start the experiment. AWS FIS shuts down EKS nodes for up to 70% of currently running instances. In this experiment, this value is 40% and it is configured in the experiment template. You can edit this value in the target selection mode configuration if you want to change the number of EKS nodes to shut down. When the experiment started, you can see the terminated instances on the EC2 service page, and you will see the new instances will appear shortly after the EKS node is shut down.

![aws-fis-terminate-eks-nodes](/images/30_eks/aws-fis-terminate-eks-nodes.png)

![aws-fis-terminate-eks-nodes-action-complete](/images/30_eks/aws-fis-terminate-eks-nodes-action-complete.png)

## Result

You can see the nodes being shut down in the cluster:
```sh
kubectl -n sockshop get node -w
```
```sh
NAME                                            STATUS   ROLES    AGE     VERSION
ip-10-1-1-205.ap-northeast-2.compute.internal   Ready    <none>   21m     v1.20.4-eks-6b7464
ip-10-1-9-221.ap-northeast-2.compute.internal   Ready    <none>   4m40s   v1.20.4-eks-6b7464
ip-10-1-9-221.ap-northeast-2.compute.internal   NotReady   <none>   4m40s   v1.20.4-eks-6b7464
ip-10-1-9-221.ap-northeast-2.compute.internal   NotReady   <none>   4m40s   v1.20.4-eks-6b7464
```

## Architecture Improvements

Cluster Autoscaler is a tool that automatically adjusts the size of the Kubernetes cluster when one of the following conditions is true:

+ there are pods that failed to run in the cluster due to insufficient resources.
+ there are nodes in the cluster that have been underutilized for an extended period of time and their pods can be placed on other existing nodes.

Cluster Autoscaler provides integration with Auto Scaling groups. Cluster Autoscaler will attempt to determine the CPU, memory, and GPU resources provided by an EC2 Auto Scaling Group based on the instance type specified in its Launch Configuration or Launch Template. Click [here](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler/cloudprovider/aws) for more information.

Watch the logs to verify cluster autoscaler is installed properly. If everything looks good, we are now ready to scale our cluster.
```sh
kubectl -n kube-system logs -f deployment/cluster-autoscaler
```

Scale out pods for high availability.
```sh
cd ~/environments/fisworkshop/eks/
kubectl apply -f kubernetes/manifest/sockshop-demo-ha.yaml
```

## Discussion

실험 계획과 예상 결과를 작성해보기 (5분 작성 / 10분 발표 토론)

## Rerun Experiment