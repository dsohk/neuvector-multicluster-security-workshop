# Zero-trust Security for Multi-cluster Kubernetes environment with Rancher and NeuVector


Learning Rancher & NeuVector Hands-on Workshop Series

## Introduction

Enterprises operating multiple Kubernetes clusters spanning single or even multiple clouds have already been complex for the DevOps team. Securing such a multi-cluster environment adds further challenges to the team. 

With NeuVector’s multi-cluster monitoring capabilities, it brings simplicity to container security with a single console and delivers the end-to-end risk monitoring and risk reporting that enterprises need for cloud-native security.

 This hands-on workshop will explore with you how to deploy and set up NeuVector across multiple clusters in federation mode. You will create global security rules to protect workloads across multiple clusters with a centralized console.

You will be given lab instructions in exploring these technologies in a cloud-based lab environment. The best part is that you can experience all these technologies just with your own PC/laptop as long as you have the Google Chrome browser installed and have connected to fast and stable internet. You don't need to install anything on your PC/laptop.


## Pre-requisites

1. This Lab will require only chrome browser with fast and reliable internet connection. 

2. Make sure you have access to following urls behind your corporate firewall/personal computers. 

   [Github](https://github.com/) 

   [Sslip](https://sslip.io) (, *.sslip.io)

4. To spin this lab with your own personal/corporate Microsoft Azure Cloud account, please follow [this lab preparation guide with terraform automation script](https://github.com/dsohk/workshops/tree/main/scenarios/azure/rancher-with-n-rke2-clusters)  link

5. Optional - Access to [SUSE Partner Portal](https://myaccount.suse.com/)



## Open Source Technologies Used

| Software  | Version         | Remarks                                                      |
| --------  | --------------- | ------------------------------------------------------------ |
| Rancher   | v2.6.9           | Enterprise-grade Kubernetes Management Platform              |
| RKE2      | v1.24.7+rke2r2            | Rancher Kubernetes Engine 2 - with Kubernetes version 1.24.7 |
| NeuVector | v5.0.3            | Full Lifecycle Container Security |



## Before We Begin

Before we start the lab, please make sure you have been provided with the following lab access credentials from the instructor. Each participant should have their own unique environment and credentials.

| Item                              | Value                                                      |
| --------------------------------- | ---------------------------------------------------------- |
| Rancher Server URL                | https://rancher.xx.xx.xx.xx.sslip.io                       |
| Rancher Server Username           | admin                                                      |
| Rancher Server Bootstrap Password | system assigned strong password                            |




### Step 1 - Access Rancher and NeuVector Portals

a) With a Google Chrome browser, access Rancher Server URL (https://rancher.xx.xx.xx.xx.sslip.io). If you are greeted by an invalid SSL certificate error message, please follow the Note*.

b) Enter the admin credentials to login i.e. `Username: admin`  and `Password: system assigned strong password`. 

c) When it's successful, you will be landing on the Rancher Home page. 

d) Do the same for NeuVector URL (https://neuvector.yy.yy.yy.yy.sslip.io). You will then be led to NeuVector login page. If you are greeted by an invalid SSL certificate error message, please follow the Note*.

e) Enter the admin credentials to login i.e. `Username: admin`  and `Password: system assigned strong password`. 

##### *Note: 

If you are greeted by an invalid SSL certificate error message, you can click on the **Advanced** button

Click the Proceed to https://rancher.xx.xx.xx.xx.sslip.io and/or https://neuvector.yy.yy.yy.yy.sslip.io hyperlink to continue. 

Now the login page will be displayed and proceed to authenticate yourself by entering username and password.

If link isn't shown, click on the empty space on browser and type the word `thisisunsafe` without any spaces to bypass the warning message. 

Now the login page will be displayed and proceed to authenticate yourself by entering username and password.



## Lab Exercises

This lab consists of 3 exercises. 

1.  [Setup NeuVector from Rancher](docs/01-setup-neuvector-multicluster.md) 
2.  [Exploring NeuVector Security](docs/02-neuvector-behavioral-learning.md)
3.  [Setup NeuVector to manage multiple clusters with federation mode](docs/03-neuvector-federation-policy.md) 



## References

* [Rancher Documentation](https://rancher.com/docs/rancher/v2.6/en/)
* [NeuVector - Full-lifecycle container security](https://open-docs.neuvector.com/)
