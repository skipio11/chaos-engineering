---
title: "2. Setup Cloud9 Environment"
chapter: false
weight: 12
---

## Create Cloud9 Environment

Go to **Cloud9** Service console.
![image](/images/10_prequisites/cloud9_01.png)

Click **Create environment** button to create Cloud9 Environment.
![image](/images/10_prequisites/cloud9_02.png)

Enter **Name** as `chaos-fis-workshop-cloud9-env` and click **Next step** button.
![image](/images/10_prequisites/cloud9_03.png)

In **Configure settings**, set the defaults without any changes then click **Next step** button.

On the **Review** page, click **Create environment** button.

You can see following IDE screen after provisioning of AWS Cloud9 is completed.
![image](/images/10_prequisites/cloud9_04.png)

---

## Install prerequisites

In Cloud9, run the following commend in the terminal area to install prerequisites.

```
sudo yum install -y jq
sudo npm install -g aws-cdk
cdk --version
```
