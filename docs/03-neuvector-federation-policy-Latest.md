# Lab 3 - Manage multi-cluster security with NeuVector federation mode 

Story line for the Lab. 



High Level task which we will achieve in this lab. 

Task 1 - Setup NeuVector federation mode between cluster1 and cluster2

Task 2 - Manage Multiple NeuVector Cluster via NeuVector Federation

Task 3 - Create and Test Federated Policy (admission control policy) 

## Task 1.1 - Setup NeuVector federation mode between cluster1 and cluster2

### Task 1.1 - Identify the federation ports and IP address for both cluster 1 & 2

Let identify the IP address first 

Rancher > Cluster Management > Cluster 1 > Cluster > Node  ![Cluster1-Node-IP](../images/Cluster1-Node-IP.png)

Next step is to identify, Fed Master & Fed Managed Services port, it will be listening/serving. 

Rancher > Cluster Management > Cluster 1 > Explore >  Service Discovery > Services 

Switch to namespace as `cattle-neuvector-system`

locate the `neuvector-svc-controller-fed-master`. Note down your Port Number

Similarly note down `neuvector-svc-controller-fed-managed`.  Note down your Port Number

![Cluster1-services-neuvector-service-fed-portsPNG](../images/Cluster1-services-neuvector-service-fed-portsPNG-1669279785695-2.png)

Task 1.1.1 - Repeat the steps for Cluster 2.

### Task 1.2 - Configuring NV for multi-cluster federation

The NeuVector console can be used to manage large enterprise multi-cluster and multi-cloud deployments. One cluster should be selected as the Master cluster, and other Remote clusters will then be able to join the Master. Once connected, the Master cluster can push Federated rules down to each remote cluster, which display as Federated rules in the consoles of each remote cluster.

There MUST be network connectivity between the controllers in each cluster on the required ports. The controller is exposed external to its cluster by either a master or remote service. 

For Port Requirement, you can refer URL- https://open-docs.neuvector.com/basics/installation/native#ports-and-port-mapping

#### Task 1.2.1 - Configure Master and Remote Clusters

##### Configure Master - 

Log into the console for the `Cluster1` which will be the `Master cluster`. In the upper right drop down menu, select Multiple Clusters and then click on `Promote` . This promotes the current cluster to be Primary Cluster in NeuVector Federation. 

Provide the Cluster 1 which will be acting a Fed-Master, It's IP & Port which we have discovered in previous step and hit submit

![Promote-Fed-Master](../images/Promote-Fed-Master.PNG)

Let's verify if the federation success. 

Click on `admin/fedAdmin` drop down menu > Multiple Clusters, below results show the cluster 1 is not Fed-Master 

![Fed-Master-Promte-Success](../images/Fed-Master-Promte-Success.PNG)



After logging back into the console, select Multiple Clusters again from the upper right menu, and click on the icon to generate a token needed to connect the remote clusters. Copy the token for use in the next step. The token is valid for about 1 hour, and if expired must be generated again to connect future remote clusters.

![Fed-Master-Token](../images/Fed-Master-Token.png)

![Fed-Master-Token-Generation](../images/Fed-Master-Token-Generation.PNG)



##### Configure Remote Cluster (Managed)

Next step is to join the cluster2 to NeuVector Federation. 

Task 1.2.2 - Log into the console for the `Cluster2` which will be the `Managed cluster`. In the upper right drop down menu, select Multiple Clusters and then click on `Join`. This joins an existing Primary Cluster. 

Provide the Cluster 2 IP & Port for neuvector-svc-controller-fed-managed we have discovered in previous step and hit submit

Copy & Past the Token we created in previous step and hit submit. This automatically populate the IP & Port for neuvector-svc-controller-fed-master.

![Fed-Manage-Join](../images/Fed-Manage-Join.PNG)

You will get a message at the right hand bottom the operation was successful, if you have provided the corrects details as requested.  

We should see the below page where we now see 2 cluster & its type clearly mentioned along with its status. 

![Fed-Manage-Join-Success](../images/Fed-Manage-Join-Success.PNG)



## Task 2 - Manage Multiple NeuVector Cluster 

### Task 2. 1 - Access Master

Cluster1 > Login into NeuVector UI 

You can click on the manage icon in the list, or use the pull down multi-cluster menu at the top to switch clusters at any time. Once you have switched to a remote cluster, all menu items on the left now apply to the remote cluster.

![Manager-Mulitple-Cluster-Via-Fed-Master](../images/Manager-Mulitple-Cluster-Via-Fed-Master.PNG)



From the Home like Icon `Cluster1-fed-maser`  you can use the drop down menu to list all the NeuVector clusters which are part of NeuVector Federation & can be managed from single console. You can toggle between the listed NeuVector Clusters to manage them.

NeuVector - Cluster1 View. 

![Fed-View-Cluster1-fed-master](../images/Fed-View-Cluster1-fed-master.PNG)

### Task 2. 2 - Access Managed

NeuVector - Cluster2 View.

 You can even see a message in the right hand bottom which indicate at you are re-directed to another cluster.

![Fed-View-Cluster2-fed-managed](../images/Fed-View-Cluster2-fed-managed.PNG)

## Task 3 - Create Federated Policy (admission control policy) in cluster1 (master)

Every Organization has a security posture which needs to be uniform across your IT including Kubernetes landscape. There is no flexibility in such cases. IT Admins / DevOps Team would find it difficult If these needs to be handled manually & via different tooling. 

What if we have a tool which allow us to manage such security posture via single tool/console. 

Example 1- What if we want to ensure that no application is installed in default namespace.  

Example 2 - What if you want to restrict your developers to only use trusted images from your private registries. 

In such cases, you can create and manage such situation via SUSE NeuVector, where the policy are created from Fed-Master & rolled out to all cluster which are part of the NeuVector Federation.  Changes to the policy are handled centrally & cascaded down to all cluster. You cannot edit/modify them on the downstream cluster.

Below are out of the box Federation Policies. 

![Sample-Fed-Policy](../images/Sample-Fed-Policy.PNG)

Let's create our first Federation Policy which would be an Admission Control one. 

### Task 3.1 - Create a new Federation Policy (Admission Control). 

Cluster1 - NeuVector - Admin/fedAdmin drop down > Federated Policy > Admission Control > Add

Below form page will appear where we will need to provide details to create Admission Control Rule.  

Type = `Deny`, unless you need to enable it. 

Comment = `deny deployment in default namespace`

Criterion = Click on drop down & select `Namespace`

Operator = `is one of`

Value = `default`

Status = `Enabled`

Click on `ADD` once completed filling all details. 

--------------------------delete once created & check against the flow-----------------------------------------------------------------------------------------------------

Please see the Policy -> Federated Policy section for instructions on how to create Federated rules that will be pushed to each cluster.

1. to deny deployment into default namepsace.

2. to verify federated admission policy is appearing in cluster2 (managed)

3. Navigate to Policy > Admission Control (applies to both clusters)

   1. Observe the federated rule appearing
   2. Status change to Enabled
   3. Switch to Protect mode

   --------------------------delete once created & check against the flow-----------------------------------------------------------------------------------------------------

![Fed-Rule-Admission-Control-deny-deployment-in-default-namespace](../images/Fed-Rule-Admission-Control-deny-deployment-in-default-namespace.PNG)

Repeat the above steps for creation another Admission Control to restrict any new deloyment/pod in eshop

Type = `Deny`, unless you need to enable it. 

Comment = `deny deployment in default namespace`

Criterion = Click on drop down & select `Namespace`

Operator = `is one of`

Value = `eshop-demo`

Status = `Enabled`

Click on `ADD` once completed filling all details. 

Once the policy is created, you will see it under `Admission Control` with an corresponding ID, Criteria, Action & Type. 

Once both the Federation Rules are create you should see them in the Admission Control page. 

![2-Fed-Policy-Creation](../images/2-Fed-Policy-Creation-1669620879171-11.PNG)



![Fed-Rule-Admission-Control-deny-deployment-in-default-namespace-Success](../images/Fed-Rule-Admission-Control-deny-deployment-in-default-namespace-Success.png)

Now that we have created the Federation Policy, let's check the Federation Status. The cluster should be in sync & if they are then the Fed Policy should be sync on Cluster2-fed-managed & should also be visible under it. 

![Fed-Admission-Control-Policy-Creation-Sync-status](../images/Fed-Admission-Control-Policy-Creation-Sync-status.PNG)

### Task 3.1.2 - Validate Federation Policy Status across landspace. 

Since the Cluster are in Sync, let check if the policy is reflecting in the `cluster2-fed-managed`. 

From the Home like Icon `Cluster1-fed-maser`  you can use the drop down menu to `Cluster2-fed-managed` > Policy > Admission Control

We see the policy which was created via Federation on `Cluster1-fed-master` is now visible in `cluster2-fed-managed`

![2-Fed-Policy-Creation-Cluster2](../images/2-Fed-Policy-Creation-Cluster2-1669620966972-13.PNG)

Note - You can only view policy and hence operators cannot modify the policy. If any modification is required, it need to be done from Federation Master where the policy was created.

### Task 3.2 - Enable the Admission Control on the clusters. 

For the Admission Policy to take effect, we have one last step to do which is to enable the Admission Control Rule for local cluster. This can be done by toggle the status button which allow you have the admission policy in Monitor or Protect Mode based on your decision. 

Status = `Enabled` ensure the Admission Control Policy is On

Mode = `Protect` ,  Under Protect mode the Admission policy is enforced, it will only create event which would be visible under NeuVector > Notification > Risk Reports

![Fed-Admission-Control-Policy-Enabled-Mode-Protect](../images/Fed-Admission-Control-Policy-Enabled-Mode-Protect.png)



If you incidentally keep the Mode= `Monitor`, the admission policy is enforced, however you will only get notified under NeuVector > Notification > Risk Report > Filter on admission.control deny 

![`](../images/Fed-Admission-Control-Policy-Enabled-Mode-Monitor-1669288554674-22.png)

![Fed-Admission-Control-Policy-Enabled-Mode-Monitor-Security-Event-Notification](../images/Fed-Admission-Control-Policy-Enabled-Mode-Monitor-Security-Event-Notification.png)

In next task, Task 3, we will test the Federated Admission Control Policy on any of the NeuVector Federated Cluster. We will be testing on `Cluster2`. The application used in our example is spring-petclinic app and we would be deploying the application via Rancher Fleet (CD) 

### Task 3.3 - Deploy Application to validate Admission Control Policy

Rancher > Global Apps > Cluster Management > Cluster2 > Explore > Workload > Deployments > Create 

namespace = `default`

Image = `nginx:latest`

We see error as our Federated Admission Control has taken effect and is preventing the deployment in the default namespace. 

![fed-policy-deny-app-in-defaut-namespace](../images/fed-policy-deny-app-in-defaut-namespace.PNG)

Similarly, we if we try & deploy our application in `eshop-demo` namespace, it will fail again due to the Federated Admission Control policy. This helps us to ensure post the installation, there would be no more pod created which will help us to reduce our surface of attach. 

We can also validate any violation of admission control in NeuVector under Risk Reports.  

Cluster1 - NeuVector - Admin/fedAdmin drop down > cluster2-fed-managed > Notification > Risk Reports

Filter `Denied`

![admission-control-deny](../images/admission-control-deny.PNG)



Let's can take a closer look at the event for more details to understand the Admission Control Denied Rule and it would point to the federated rule we created. 

Click on the `+` sign

![federal-rule-deny-Event](../images/federal-rule-deny-Event.PNG)



However, if we select any other namespace 

Rancher > Global Apps > Cluster Management > Cluster2 > Explore > Workload > Deployments > Create 

namespace = `app-nginx` (considering you already have the namespace created in advance)

Image = `nginx:latest`

 We will no longer see the deployment error as it's not matching any of the admission control policy (Federated & or Local Admission Control).

![app-nginx-in-app-nginx-namespace-success](../images/app-nginx-in-app-nginx-namespace-success.PNG)

Let's can take a closer look at the event for more details to understand the Admission Control Denied Rule and it would point to the federated rule we created. 

Click on the `+` sign

![No-Admission-Control-Rule-Match-Allowed](../images/No-Admission-Control-Rule-Match-Allowed.PNG)



This bring us to the end of the exercise & also the workshop. 
