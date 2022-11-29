# Lab 1 - Setup NeuVector

In this lab, we'll deploy NeuVector from Rancher cluster tools onto the Rancher managed Kubernetes clusters. 

## Task 1 - Install NeuVector

In this task, we are going to install NeuVector onto the 2 Kubernetes clusters provided in the lab.

1. Login to Rancher Server with your own login credentials provided by the lab administrator. 
2. From the `Home page`, you will find there are 3 clusters currently managed by Rancher Server. `local` cluster is where Rancher Server is running. `cluster1` and `cluster2` are two RKE2-based clusters that we are going to deploy NeuVector onto.

![image-20221123195948110](../images/rancher-homepage.png)

3. From the Clusters table, click `cluster1` to open the Cluster Explorer of this cluster. You will be led to the page like below.

![image-20221123200447841](../images/rancher-cluster-explorer-cluster1.png)

4. Click on `Cluster Tools` at the bottom of the left-hand side navigation menu.

5. Find the NeuVector card and click `Install`.

![image-20221123200630176](../images/rancher-cluster-tools.png)

6. Choose `System` value from `Install Into Project` dropdown box and click the `Next` button to continue.

![image-20221123200752713](../images/neuvector-install-1.png)

You are now leading to the `Install NeuVector: Step 2` page where you will find there are a few `Edit Options`. 

7. Switch to the `Container Runtime` tab, uncheck the `Docker Runtime` and then check `k3s Containerd Runtime` as the cluster we are going to deploy to are RKE2 based which is inherited from the K3S containerd runtime engine. Your editing option should be like the example below.

![image-20221123201150892](../images/neuvector-install-2-container-runtime.png)

8. Switch to the `PVC Configuration` tab, check the `PVC Status` option. Leave the `Storage Class` input box as blank to assume using the default storage class which is longhorn pre-installed in the cluster. 

![image-20221123201432671](../images/neuvector-install-2-pvc.png)

9. Last but not least, switch to `Service Configuration`. Let's expose the Fed Master and Fed Managed services as NodePort. This is to prepare us to setup NeuVector federation mode in both clusters in Lab 3. Your editing options should be like below.

![image-20221123201757910](../images/neuvector-install-2-services.png)

10. Click `Install` to start deploying NeuVector onto `cluster1`. Rancher will then run the NeuVector helm chart to deploy NeuVector onto the cluster with the screen like below.

![image-20221123202107451](../images/neuvector-install-successful.png)

10. Repeat Step 2-10 to deploy NeuVector onto another cluster - `cluster2`.



## Task 2 - Post-Setup of NeuVector

Before you start this task, please make sure you have successfully deployed NeuVector onto both `cluster1` and `cluster2` as outlined in Task 1. At the point, we are going to rename the neuvector instance installed in each cluster to match with its corresponding cluster name. For the sake of convenience (and reduce the security level), we will also explicitly increase the default login timout session of NeuVector for learning purpose.



1. Ensure you are at the cluster explorer for `cluster1` in Rancher Server.

   Navigate to the NeuVector menu on the left-hand side navigation bar in Rancher UI. Click the `Full lifecycle Container Security` link in the NeuVector box to open up a new browser tab accessing to the NeuVector user interface. You should see the interface like below.



![image-20221123222918702](../images/rancher-neuvector-menu.png)

2. Let's increase the default login session timeout value from 5 mins to 1 hour to give us better learning experience of NeuVector (not recommended at production):
   * Accept the `EULA (End User License Agreement)` if it's your first time accessing to the NeuVector portal.
   * At the Dashboard of NeuVector, pull down the `Admin` menu on the top right corner and select `My Profile` menu item to continue.
   * Click `Edit Profile` link to unfreeze the Profile form. 
   * Change the `Session Timeout(seconds)` from `300` to `3600` and click `Update Profile` button to apply the changes.

![image-20221123223900416](../images/neuvector-profile-editing.png)

2. Let's also change the instance name of NeuVector to match the cluster name in which it's deployed on. .Navigate to the left menu `Settings`, then click `Configuration` box to open the NeuVector Settings form.

![image-20221123224028831](../images/neuvector-settings-configuration.png)

3. Locate the change cluster name input field. 

   Enter `cluster1` and click `Submit` button to apply the changes.

![image-20221123224641438](../images/neuvector-change-name.png)

4. Repeat this Task 2 for `cluster2` as well.



## Task 3 - Deploy a microservices based eshop application

In this task, we are going to deploy a microservices based application (an online boutique ecommerce shop) into both `cluster1` and `cluster2` from Rancher Marketplace Catalogue.

1. In Rancher UI, navigate to the `Apps` in the left-hand sided navigation bar, expand it to locate `Repositories`.
2. Click `Create` button to add a new repository.

![image-20221123225310394](../images/rancher-apps-marketplace.png)

3. In the `Create Repository` form, fill in the followings and then click `Create` button.
   * Name: `samples`
   * Index URL: `https://rancher.github.io/rodeo`

![image-20221123225457487](../images/rancher-add-rodeo-repo.png)

4. Now, navigate to the `Apps` and then `Charts`. Select `samples` only in the charts. You should see the following catalogue items:

![image-20221123225830310](../images/rancher-apps-catalogue-rodeo.png)

5. Click `online-boutique` and click `Install` button to continue.

6. At the `Install Step 1` page, pull the dropdown box of `Namespace` and choose `Create new namespace`. 

   * Fill in `eshop-demo` as the namespace. 

   * Fill in the same value (`eshop-demo`) in the `Name` field next to the `Namespace` to continue. This will create a new namespace `eshop-demo` to host all the microservices containers in the cluster. 

   * Click `Next` button to continue.

![image-20221123230310598](../images/rancher-online-boutique-install-1.png)

7. Uncheck the `Expose app using Layer 7 Load Balancer` option. We will navigate the online-boutique application via Rancher.

8. Switch to the `Features` tab, uncheck the features that we don't plan to use in this lab exercise like below, and then click `Install` to deploy the online-boutique app onto the cluster.

![image-20221123231218837](../images/rancher-online-boutique-install-2b.png)

9. After a few minutes, the `online-boutique` app should have been successfully deployed into the `eshop-demo` namespace. Let's now find the URL to access the online boutqiue portal in Rancher.

   * Navigate to `Service Discovery` in the left navigation bar, click `Services`. 
   * Ensure the namespace selected in the top menu is `Only User Namespace`.
   * You should find the services named `eshop-online-boutique-frontend`. 
   * The URL is highlighted by the link `http`.

   ![image-20221129082223944](../images/rancher-online-boutique-install-completed.png)

   * Click on that URL to open up the frontend. You should see the following page.

   ![image-20221123232410469](../images/rancher-online-boutique-frontend.png)

Finally, before we move the next exercise, we create additional Namespace to test Admission Control policy for lab 3. 

Rancher > Global Apps > Cluster Management > Cluster 2 > Explore > Project/Namespaces > Create 

Name = `app-nginx`

 Rancher > Global Apps > Cluster Management > Cluster 2 > Explore > Project/Namespaces >  Project: app-nginx > Create Namespace

Name = `app-nginx`

![image-20221128174753022](../images/image-20221128174753022.png)



## Conclusion

This is the end of Lab 1. At this point, you should have deployed and configured NeuVector on both `cluster1` and `cluster2`. You should have also successfully deployed the microservices app `online-boutique` on these 2 clusters as well. Additional namespace would have also created. 

Let's move to the next Lab to learn some powerful security features offered by NeuVector to protect your workload (`online-boutique` in this case.). In this lab we will focus on NeuVector `Run Time` Security.



## Next Step

- [Navigate to Lab 2](02-neuvector-behavioral-learning.md) 
- [Return to Home Page](../README.md) 

