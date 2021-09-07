---
title: "4. Postmortem - Learning from Failure"
chapter: false
weight: 14
---

## Discussion

+ What did you expected?
+ What happend?

Then access the microservices application again. What happened? Perhaps a node shutdown by a fault injection experiment will cause the application to crash. This is because the first deployment of the application did not consider high availability.

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

## Rerun Experiment
