---
title: "1. Create IAM Role"
chapter: false
weight: 11
---

In the AWS Cloud9, create an **IAM Role** to obtain necessary permissions for this hands on lab.
 
Go to **IAM** Service console.
![image](/images/10_prequisites/iam_01.png)

In the IAM conosle, go to  **Roles** menu and click the **Create role** button.
![image](/images/10_prequisites/iam_02.png)

On the Create role page, select **EC2** in **Choose a use case** section. Then click **Next:Permissions** button at the bottom.
![image](/images/10_prequisites/iam_03.png)

In **Attach permissions policies** section, select **AdministratorAccess** like below and click **Next:Tags** button.
![image](/images/10_prequisites/iam_04.png)

Click **Next:Review** button at the bottom with default settings.

In the **Review**, put **Role name** as `ChaosEngineeringWorkshop-Admin` and click **Create role** button to create role.
![image](/images/10_prequisites/iam_05.png)

You can see below message if the role created completely.
![image](/images/10_prequisites/iam_06.png)
