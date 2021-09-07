---
title: "3. Attach IAM Role to Cloud9"
chapter: false
weight: 13
---

When you provisioning AWS Cloud9, it has limited permissions. For the hands-on lab, you will attach IAM Role which is created in **2-1. Create IAM Role** to AWS Cloud9 instance.

Check the credential which currently in used on AWS Cloud9 environment with below command.

```
aws configure list
```

![image](/images/10_prequisites/cloud9_06.png)

You can see the Type is **shared-credentials-file**. When AWS Cloud9 is provisioned, AWS managed temporary credential with limited permission is created in `~/.aws/credentials`.

Click the **gear icon** at the top-right of AWS Cloud9 console to move to Preferences menu.
![image](/images/10_prequisites/cloud9_07.png)

From **AWS Settings** menu, change **'AWS managed temporary credential’** to **disable**.
![image](/images/10_prequisites/cloud9_08.png)

Re-enter `aws configure list` command to see credential information that currently being used in AWS Cloud9 environment as follows.
![image](/images/10_prequisites/cloud9_09.png)

Now go to EC2 console and attach IAM Role which you crated in advance to AWS Cloud9 instance. **Instances -> Actions -> Security -> Modify IAM role**
![image](/images/10_prequisites/cloud9_10.png)

Select **ChaosEngineeringWorkshop-Admin** and save it.
![image](/images/10_prequisites/cloud9_11.png)

To confirm that the ROLE is attached to AWS Cloud9, run `aws configure list` command once again to check credential information. You can see the value of **Type** is **iam-role**.
![image](/images/10_prequisites/cloud9_12.png)

Now using `aws configure set region us-east-1` command, set region to **us-east-1**.
![image](/images/10_prequisites/cloud9_13.png)
